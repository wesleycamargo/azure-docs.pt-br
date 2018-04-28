---
title: Memória e limites simultâneos - SQL Data Warehouse do Azure | Microsoft Docs
description: Exiba os limites de memória e simultaneidade alocados para os vários níveis de desempenho e classes de recursos no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4e6e95e8601e7ab8b836e2aa1aa21ef4d5779954
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Memória e limites simultâneos para SQL Data Warehouse do Azure
Exiba os limites de memória e simultaneidade alocados para os vários níveis de desempenho e classes de recursos no SQL Data Warehouse do Azure. Para obter mais informações e aplicar esses recursos em seu plano de gerenciamento de carga de trabalho, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md). 

## <a name="performance-tiers"></a>Níveis de desempenho

O SQL Data Warehouse oferece dois níveis de desempenho que são otimizados para cargas de trabalho analíticas. Um nível de desempenho é uma opção que determina a configuração do data warehouse. Essa opção é uma das primeiras escolhas que você faz ao criar um data warehouse.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- O **nível de desempenho Otimizado para Elasticidade** separa as camadas de armazenamento e computação na arquitetura. Essa opção se destaca em cargas de trabalho que podem aproveitar ao máximo a separação entre o armazenamento e a computação, dimensionando com frequência para dar suporte a curtos períodos de atividade na atividade de pico. Essa camada de computação tem o menor ponto de preço de entrada e é dimensionada para dar suporte à maioria das cargas de trabalho do cliente.

- O **nível de desempenho Otimizado para Computação** usa o hardware mais recente do Azure para apresentar um novo cache de disco de estado sólido NVMe que mantém os dados acessados com mais frequência próximos às CPUs, que está exatamente onde você os deseja. Ao criar camadas de armazenamento automaticamente, esse nível de desempenho se destaca com consultas complexas, já que todas as E/S são mantidas locais na camada de computação. Além disso, o columnstore foi aprimorado para armazenar uma quantidade ilimitada de dados no SQL Data Warehouse. O nível de desempenho Otimizado para Computação fornece o maior nível de escalabilidade, permitindo que você aumente até 30.000 cDWU (Unidades de Data Warehouse de computação). Escolha essa camada para cargas de trabalho que exijam desempenho contínuo e ultrarrápido.

## <a name="data-warehouse-limits"></a>Limites do Data warehouse
As tabelas a seguir mostram a capacidade máxima para o data warehouse em diferentes níveis de desempenho. Para alterar o nível de desempenho, consulte [escala de computação - portal](quickstart-scale-compute-portal.md).

### <a name="optimized-for-elasticity"></a>Otimizado para elasticidade

Os níveis de serviço para o intervalo de nível de desempenho Otimizado para Elasticidade de DW100 a DW6000. 

| Nível de desempenho | Máximo de consultas simultâneas | Nós de computação | Distribuições por nó de computação | Memória máxima por distribuição (MB) | Memória máxima por data warehouse (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1.200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1.600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2.000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2.400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4.000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4.800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6.000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8.000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12.000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24.000                           | 1440                               |

### <a name="optimized-for-compute"></a>Otimizado para computação

O nível de desempenho Otimizado para Computação fornece 2,5 vezes mais memória por consulta do que o nível de desempenho Otimizado para Elasticidade. Essa memória extra ajuda o nível de desempenho Otimizado para Computação entrega seu desempenho rápido.  Os níveis de desempenho para o intervalo de nível de desempenho Otimizado para Computação de DW1000c a DW30000c. 

| Nível de desempenho | Máximo de consultas simultâneas | Nós de computação | Distribuições por nó de computação | Memória máxima por distribuição (GB) | Memória máxima por data warehouse (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1.200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

O cDWU máximo é DW30000c, que tem uma 60 nós de Computação e uma distribuição por nó de Computação. Por exemplo, um data warehouse de 600 TB a DW30000c processa aproximadamente 10 TB por nó de Computação.


## <a name="concurrency-maximums"></a>Limites máximos de simultaneidade
Para garantir que cada consulta tenha recursos suficientes para executar com eficiência, o SQL Data Warehouse rastreia a utilização do recurso de computação por meio da atribuição de slots de simultaneidade a cada consulta. O sistema coloca consultas em uma fila em que elas aguardam até que os [slots de simultaneidade](resource-classes-for-workload-management.md#concurrency-slots) suficientes estejam disponíveis. 

Slots de simultaneidade também determinam a priorização de CPU. Para saber mais, confira [Analisar sua carga de trabalho](analyze-your-workload.md)

### <a name="optimized-for-compute"></a>Otimizado para computação
A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso dinâmico](resource-classes-for-workload-management.md). Isso se aplica ao nível de desempenho Otimizado para Computação.

**Classes de recursos dinâmicos**
| Nível de desempenho | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | Slots usados pelo smallrc | Slots usados pelo mediumrc | Slots usados pelo largerc | Slots usados pelo xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1.200                        | 1                     | 64                     | 128                   | 256                    |

**Classes de recursos estáticos**

A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso estático](resource-classes-for-workload-management.md).  

| Nível de Serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1.200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Otimizado para elasticidade
A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso dinâmico](resource-classes-for-workload-management.md).  Isso se aplica ao nível de desempenho Otimizado para Elasticidade.

**Classes de recursos dinâmicos**

| Nível de serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Classes de recurso estático**-A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso estático](resource-classes-for-workload-management.md).  Isso se aplica ao nível de desempenho Otimizado para Elasticidade.

| Nível de serviço | Máximo de consultas simultâneas | Máximo de slots de simultaneidade |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Quando um desses limites for atingido, novas consultas serão enfileiradas e executadas em uma base de primeira a entrar, primeira a sair.  À medida que uma consulta é concluída e o número de consultas e slots ficam abaixo do limite, o SQL Data Warehouse libera as consultas em fila. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar as classes de recursos para otimizar ainda mais a carga de trabalho, examine os seguintes artigos:
* [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md)
* [Análise de sua carga de trabalho](analyze-your-workload.md)

