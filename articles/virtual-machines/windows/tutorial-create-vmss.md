---
title: Руководство. Создание масштабируемого набора виртуальных машин для Windows в Azure | Документация Майкрософт
description: В этом руководстве описано, как с помощью Azure PowerShell создать и развернуть высокодоступное приложение на виртуальных машинах Windows с помощью масштабируемого набора виртуальных машин.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9d9a4a091e1dbeb1c15cd9ec0e90d7006000bea9
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928256"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Руководство. Создание масштабируемого набора виртуальных машин и развертывание высокодоступного приложения в Windows с помощью Azure PowerShell
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Вы можете вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования на основе использования ЦП, объема памяти или сетевого трафика. В рамках этого руководства вы развернете масштабируемый набор виртуальных машин в Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов для определения масштабируемого сайта IIS
> * Создание балансировщика нагрузки для масштабируемого набора
> * Создание масштабируемого набора виртуальных машин
> * Увеличение или уменьшение количества экземпляров в масштабируемом наборе
> * Создание правил автомасштабирования

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.


## <a name="scale-set-overview"></a>Обзор масштабируемого набора
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Виртуальные машины распределяются по логическим доменам сбоя и обновления в одной или нескольких *группах размещения*. Это группы одинаково настроенных виртуальных машин, похожие на [группы доступности](tutorial-availability-sets.md).

Виртуальные машины создаются в масштабируемом наборе по мере необходимости. Можно определить правила автомасштабирования, чтобы управлять добавлением и удалением виртуальных машин в масштабируемом наборе. Эти правила могут активироваться на основе метрик, таких как использование ЦП, использование памяти или сетевой трафик.

При использовании образа платформы Azure масштабируемые наборы поддерживают до 1000 виртуальных машин. Для рабочих нагрузок, требующих установки значительного числа компонентов или сложной настройки виртуальных машин, вы можете [создать образ виртуальной машины](tutorial-custom-images.md). При использовании пользовательского образа в масштабируемом наборе можно создать до 300 виртуальных машин.


## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Создайте масштабируемый набор виртуальных машин с помощью командлета [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). В следующем примере создается масштабируемый набор с именем *myScaleSet*, использующий образ платформы *Windows Server 2016 Datacenter*. Сетевые ресурсы Azure для виртуальной сети, общедоступный IP-адрес и подсистема балансировки нагрузки создаются автоматически. При появлении запроса введите учетные данные администратора для экземпляров виртуальных машин в масштабируемом наборе:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="deploy-sample-application"></a>Разверните пример приложения
Для проверки масштабируемого набора установите базовое веб-приложение. Расширение пользовательского скрипта Azure используется для скачивания и запуска скрипта, который устанавливает IIS на экземплярах виртуальных машин. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Дополнительные сведения см. в статье [Расширение Custom Script в ОС Windows](extensions-customscript.md).

Использование расширения пользовательских скриптов для установки базового веб-сервера IIS Примените расширение пользовательского скрипта, которое устанавливает IIS, как показано ниже:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Проверка масштабируемого набора
Чтобы изучить работу масштабируемого набора, получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример получает IP-адрес элемента *myPublicIP*, созданного ранее вместе с масштабируемым набором.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Введите общедоступный IP-адрес в веб-браузер. Отобразится веб-приложение, а также имя узла виртуальной машины, на которую подсистема балансировки нагрузки направила трафик.

![Выполнение сайта IIS](./media/tutorial-create-vmss/running-iis-site.png)

Чтобы познакомиться с работой масштабируемого набора, принудительно обновите окно веб-браузера, чтобы увидеть, как балансировщик нагрузки распределяет трафик между всеми виртуальными машинами, где выполняется приложение.


## <a name="management-tasks"></a>Задачи управления
На протяжении жизненного цикла масштабируемого набора может возникнуть необходимость выполнить одну или несколько задач управления. Кроме того, можно создавать сценарии для автоматизации различных задач жизненного цикла. Azure PowerShell позволяет быстро выполнять эти задачи. Ниже приведено несколько распространенных задач.

### <a name="view-vms-in-a-scale-set"></a>Просмотр виртуальных машин в масштабируемом наборе
Чтобы просмотреть список экземпляров виртуальных машин в масштабируемом наборе, выполните командлет [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm), как показано ниже.

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

В следующем примере выходных данных показано два экземпляра виртуальной машины в масштабируемом наборе.

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Чтобы просмотреть дополнительные сведения о конкретном экземпляре виртуальной машины, добавьте параметр `-InstanceId` в командлет [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Следующий пример возвращает сведения об экземпляре виртуальной машины *1*.

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Увеличение или уменьшение числа экземпляров виртуальной машины
Чтобы просмотреть количество экземпляров, присутствующих в масштабируемом наборе, выполните командлет [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) и отправьте запрос для *sku.capacity*.

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

После этого можно вручную увеличить или уменьшить число виртуальных машин в масштабируемом наборе, выполнив командлет [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). В следующем примере число виртуальных машин в масштабируемом наборе определяется равным *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Обновление числа экземпляров в масштабируемом наборе занимает несколько минут.


### <a name="configure-autoscale-rules"></a>Настройка правил автомасштабирования
Вместо установки числа экземпляров в масштабируемом наборе вручную можно определить правила автомасштабирования. С помощью правил среда отслеживает экземпляры в масштабируемом наборе и реагирует соответствующим образом на основе определенных метрик и пороговых значений. Приведенный ниже пример увеличивает число экземпляров на один, когда средняя загрузка ЦП превышает 60 % в течение 5 минут. Если средняя загрузка ЦП не превышает 30 % в течение 5 минут, число экземпляров уменьшается на один.

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfiles $myScaleProfile
```

Дополнительные сведения см. в [рекомендациях по автомасштабированию](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Дополнительная информация
В рамках этого руководства вы создали набор масштабирования виртуальных машин. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов для определения масштабируемого сайта IIS
> * Создание балансировщика нагрузки для масштабируемого набора
> * Создание масштабируемого набора виртуальных машин
> * Увеличение или уменьшение количества экземпляров в масштабируемом наборе
> * Создание правил автомасштабирования

Перейдите к следующему руководству, чтобы узнать о принципах балансировки нагрузки для виртуальных машин.

> [!div class="nextstepaction"]
> [Балансировка нагрузки между виртуальными машинами](tutorial-load-balancer.md)
