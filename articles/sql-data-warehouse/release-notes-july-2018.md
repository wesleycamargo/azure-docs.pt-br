---
title: Notas de versão do SQL Data Warehouse do Azure de junho de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216677"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Julho de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidos em julho de 2018.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Granularidade mais refinada para restaurações entre regiões e servidores
Agora, é possível restaurar regiões e servidores usando qualquer ponto de restauração, em vez de selecionar backups com redundância geográfica feitos a cada 24 horas. A restauração de servidor e entre regiões tem suporte para ambos os pontos de restauração automática ou definidos pelo usuário, permitindo granularidade mais refinada para proteção de dados adicional. Com mais pontos de restauração disponíveis, é possível certificar-se de que o data warehouse será logicamente consistente ao restaurar entre regiões.

![Restaurar Comando - SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Opções de Restauração - SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Para obter mais informações, consulte a postagem no blog [Pontos de restauração acelerados e flexíveis](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/).

## <a name="20-minute-restorations"></a>Restaurações de 20 minutos
O SQL Data Warehouse agora oferece restauração de qualquer data warehouse **em menos de 20 minutos** dentro da mesma região, independentemente do tamanho do banco de dados. O tempo de restauração será aplicável, se a restauração for para o mesmo servidor lógico ou para um diferente na mesma região. Além disso, o processo de instantâneo foi aprimorado para reduzir o tempo necessário para criar um ponto de restauração. Em níveis de desempenho inferiores (configurações de DWU mais baixas), a melhoria é uma *redução de 2x* no tempo para criação de instantâneos.

Para obter mais informações, consulte a postagem no blog [Pontos de restauração acelerados e flexíveis](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/).

## <a name="custom-restoration-configurations"></a>Configurações de restauração personalizadas
Agora, é possível alterar o nível de desempenho (DWU) ao restaurar no portal do Azure. Adicionalmente, você pode restaurar para um data warehouse Gen2 atualizado. Ao restaurar para uma instância Gen 2, será possível avaliar o impacto da Gen2 antes de [atualizar o data warehouse Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Configuração de restauração personalizada - SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
O procedimento armazenado [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) geralmente é usado por ferramentas para obter metadados sobre parâmetros no lote Transact-SQL. O procedimento retorna uma linha para cada parâmetro no lote com as informações de tipo deduzidas para esse parâmetro. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

O conjunto de resultados inclui metadados sobre o parâmetro `@id` (resultados parciais mostrados)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```