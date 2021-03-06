---
title: Руководство. Создание и администрирование виртуальных машин Windows с помощью модуля Azure PowerShell | Документация Майкрософт
description: В этом руководстве описано, как с помощью Azure PowerShell создать и администрировать виртуальные машины Windows в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d47981042fc13a96bdf5cb9690e4dc83a6aa0162
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932557"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Руководство. Создание и администрирование виртуальных машин Windows с помощью Azure PowerShell

Виртуальные машины Azure предоставляют полностью настраиваемую и гибкую вычислительную среду. В этом руководстве рассматриваются основные элементы развертывания виртуальной машины Azure, например выбор ее размера, образа и ее развертывание. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины и подключение к ней
> * Выбор и использование образов виртуальных машин
> * Просмотр и использование определенных размеров виртуальных машин
> * Изменение размера виртуальной машины
> * Просмотр виртуальной машины и оценка ее состояния

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины. В следующем примере создается группа ресурсов с именем *myResourceGroupVM* в регионе *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

Группа ресурсов указывается при создании или изменении виртуальной машины, что показывается в этом руководстве.

## <a name="create-virtual-machine"></a>Создание виртуальной машины

При создании виртуальной машины можно настроить несколько параметров, включая образ операционной системы, конфигурацию сети и учетные данные администратора. В этом примере создается виртуальная машина *myVM*, на которой выполняется последняя стандартная версия Windows Server 2016 Datacenter.

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Подключение к виртуальной машине

После завершения развертывания создайте подключение к удаленному рабочему столу виртуальной машины.

Выполните приведенные ниже команды, чтобы получить общедоступный IP-адрес виртуальной машины. Запишите этот IP-адрес. Он потребуется, чтобы подключиться к виртуальной машине в браузере и проверить возможность подключения к Интернету на следующем этапе.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

На вашем локальном компьютере используйте следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной. Замените IP-адрес значением *publicIPAddress* виртуальной машины. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```powershell
mstsc /v:<publicIpAddress>
```

В окне **Безопасность Windows** выберите **Варианты выбора** и нажмите **Использовать другую учетную запись**. Введите имя пользователя и пароль, созданные для виртуальной машины, и нажмите кнопку **ОК**.

## <a name="understand-vm-images"></a>Описание образов виртуальных машин

Azure Marketplace содержит множество образов виртуальных машин, которые можно использовать для создания виртуальной машины. На предыдущих шагах виртуальная машина создавалась с помощью образа Windows Server 2016 Datacenter. На этом шаге модуль PowerShell используется для поиска других образов Windows на сайте Marketplace, которые можно также использовать для создания виртуальных машин. Этот процесс заключается в поиске сведений об издателе, предложении, номера SKU и (необязательно) номера версии для [идентификации](cli-ps-findimage.md#terminology) образа. 

Используйте команду [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), чтобы получить список издателей образов:

```powershell
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Используйте команду [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), чтобы получить список предложений образов. Эта команда возвращает список, отфильтрованный по указанному издателю:

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

Команда [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) отфильтрует список по имени издателя и названию предложения, отобразив список имен образов.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Эти сведения можно использовать для развертывания виртуальной машины на основе конкретного образа. В этом примере развертывается виртуальная машина с помощью последней версии Windows Server 2016 и образа контейнеров.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Параметр `-AsJob` создает виртуальную машину как фоновую задачу, поэтому PowerShell отображает запрос о возврате. Подробные сведения о фоновых заданиях можно просмотреть с помощью командлета `Get-Job`.


## <a name="understand-vm-sizes"></a>Описание размеров виртуальных машин

Размер виртуальной машины определяет количество выделяемых ей вычислительных ресурсов, таких как ЦП, GPU и память. Размер создаваемых виртуальных машин должен соответствовать ожидаемой рабочей нагрузке. При увеличении рабочей нагрузки размер существующей виртуальной машины может быть изменен.

### <a name="vm-sizes"></a>Размеры виртуальных машин

В приведенной ниже таблицы указаны категории размеров и примеры использования.  
| type                     | Распространенные размеры           |    ОПИСАНИЕ       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Универсальные](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0–7| Сбалансированное соотношение ресурсов ЦП и памяти. Идеально подходят для разработки и тестирования малых и средних приложений и решений для обработки данных.  |
| [Оптимизированные для вычислений](sizes-compute.md)   | Fs, F             | Высокое соотношение ресурсов ЦП и памяти. Подходят для приложений со средним объемом трафика, сетевых устройств и пакетных процессов.        |
| [Оптимизированные для памяти](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Высокое соотношение ресурсов памяти и числа ядер. Отлично подходят для реляционных баз данных, кэша среднего и большого объема, а также выполняющейся в памяти аналитики.                 |
| [Оптимизированные для хранилища](sizes-storage.md)      | Ls                | Высокая пропускная способность дисков и количество операций ввода-вывода. Идеальный вариант для работы с большими данными, а также с базами данных SQL и NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Специализированные виртуальные машины, предназначенные для ресурсоемкой отрисовки изображений и редактирования видео.       |
| [Высокопроизводительные](sizes-hpc.md) | H, A8–A11          | Виртуальные машины с самыми мощными ЦП, для которых можно настроить сетевые интерфейсы с высокой пропускной способностью (RDMA). 


### <a name="find-available-vm-sizes"></a>Поиск всех доступных размеров виртуальных машин

Чтобы просмотреть список доступных размеров виртуальных машин в определенном регионе, используйте команду [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Изменение размера виртуальной машины

После развертывания виртуальной машины ее размер можно изменить, чтобы увеличить или уменьшить выделенные ей ресурсы.

Перед изменением размера виртуальной машины проверьте, доступен ли желаемый размер в текущем кластере виртуальных машин. Команда [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) возвращает список размеров. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Если желаемый размер доступен, то размер виртуальной машины можно изменить во включенном состоянии, однако виртуальную машину нужно будет перезагрузить.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Если желаемый размер в текущем кластере недоступен, то перед изменением размера виртуальную машину нужно освободить. Обратите внимание на то, что после повторного включения виртуальной машины все данные на временном диске будут удалены, а общедоступный IP-адрес изменится, если только не используется статический IP-адрес. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>Состояния включенной виртуальной машины

Включенная виртуальная машина Azure может находиться в одном из многих состояний. Это состояние отражает текущее состояние виртуальной машины с точки зрения гипервизора. 

### <a name="power-states"></a>Состояния включения

| Состояние включения | ОПИСАНИЕ
|----|----|
| Запуск | Указывает, что виртуальная машина запущена. |
| Выполнение | Указывает, что виртуальная машина работает. |
| Остановка | Указывает, что виртуальная машина останавливается. | 
| Остановлено | Указывает, что виртуальная машина остановлена. Обратите внимание, что за виртуальные машины в остановленном состоянии по-прежнему взимается плата за вычислительные операции.  |
| Отмена выделения | Указывает, что виртуальная машина освобождается. |
| Освобождено | Указывает, что виртуальная машина полностью удалена из гипервизора, но по-прежнему доступна в плоскости управления. За виртуальные машины в освобожденном состоянии не взимается плата за вычислительные операции. |
| - | Указывает, что состояние включенной виртуальной машины неизвестно. |

### <a name="find-power-state"></a>Поиск состояние включения

Чтобы получить состояние конкретной виртуальной машины, используйте команду [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Необходимо указать допустимое имя виртуальной машины и группы ресурсов. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Выходные данные:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Задачи управления

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать скрипты для автоматизации повторяющихся или сложных задач. С помощью Azure PowerShell можно выполнять множество распространенных задач управления в командной строке или в скриптах.

### <a name="stop-virtual-machine"></a>Прекращение работы виртуальной машины

Остановите и освободите виртуальную машину с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

Если вы хотите сохранить виртуальную машину в подготовленном состоянии, задайте параметр -StayProvisioned.

### <a name="start-virtual-machine"></a>Запуск виртуальной машины

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Удалить группу ресурсов

При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы изучили основы создания виртуальной машины и управления ею. Вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины и подключение к ней
> * Выбор и использование образов виртуальных машин
> * Просмотр и использование определенных размеров виртуальных машин
> * Изменение размера виртуальной машины
> * Просмотр виртуальной машины и оценка ее состояния

Перейдите к следующему руководству, чтобы узнать о дисках виртуальных машин.  

> [!div class="nextstepaction"]
> [Управление дисками Azure с помощью Azure CLI](./tutorial-manage-data-disk.md)
