---
title: Хранилище данных SQL Azure, заметки о выпуске от апреля 2018 г. | Документация Майкрософт
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: ae3d4c3e732024baae29f75fda6f6e821af701a2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630349"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Что нового в Хранилище данных SQL Azure? Апрель 2018 г.
Хранилище данных SQL Azure постоянно совершенствуется. В этой статье описаны новые возможности и изменения, вступившие в силу с апреля 2018 года.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Возможность усечь раздел перед переключением
Пользователи часто используют переключение разделов в качестве метода для загрузки данных из одной таблицы в другую путем изменения метаданных таблицы с помощью синтаксиса `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X`. Хранилище данных SQL не поддерживает переключение разделов, если целевой раздел содержит данные. Если целевой раздел уже содержит данные, клиенту необходимо усечь его, а затем выполнить переключение.

Хранилище данных SQL теперь поддерживает эту операцию в одной инструкции T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Дополнительные сведения см. в статье об [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="improved-query-compilation-performance"></a>Улучшенная производительность компиляции запросов
В хранилище данных SQL представлен ряд изменений для улучшения этапа компиляции распределенных запросов. Эти изменения призваны улучшить время компиляции запросов в **10** раз, уменьшая общее время выполнения запросов. Эти изменения более очевидны в хранилищах данных с большим количеством объектов (таблицы, функции, представления, процедуры).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>Команды DBCC не используют слоты выдачи (изменение в поведении)
Хранилище данных SQL поддерживает подмножество [команд DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) T-SQL, например [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Раньше эти команды потребляли [слот выдачи](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots), уменьшая количество пользовательских нагрузок и запросов, которые можно было выполнить. Команды `DBCC` теперь выполняются в локальной очереди, которая не использует слот ресурсов, что улучшает общую производительность выполнения запросов.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Обновленное сообщение об ошибке для избыточных литералов (изменение в поведении)
Раньше хранилище данных SQL включало *приблизительное* количество, если запрос содержал слишком много литералов.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Сообщение об ошибке было обновлено. Теперь в нем просто говорится, что было достигнуто ограничение по литералам.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Дополнительные сведения об ограничениях см. в разделе [Запросы](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) статьи [Ограничения емкости хранилища данных SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits).

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Удалено представление SYS.PDW_DATABASE_MAPPINGS (изменение в поведении)
Представление `sys.pdw_database_mappings` не используется в хранилище данных SQL. Ранее после выбора этого представления не возвращались результаты. Поэтому это представление было удалено. 