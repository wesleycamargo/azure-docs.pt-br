---
title: "Níveis de desempenho do SQL Data Warehouse do Azure | Microsoft Docs"
description: "Introdução à elasticidade e aos níveis de desempenho otimizados de computação disponíveis no SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 03881c12faed723999e97431e4a69fdeb6bfa10d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Níveis de desempenho do SQL Data Warehouse do Azure (Versão prévia)
O SQL Data Warehouse oferece dois níveis de desempenho que são otimizados para cargas de trabalho analíticas. Este artigo explica os conceitos de níveis de desempenho para ajudá-lo a escolher o nível de desempenho mais adequado para sua carga de trabalho. 

## <a name="what-is-a-performance-tier"></a>O que é um nível de desempenho?
Um nível de desempenho é uma opção que determina a configuração do data warehouse. Essa opção é uma das primeiras escolhas que você faz ao criar um data warehouse.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- O **nível de desempenho Otimizado para Elasticidade** separa as camadas de armazenamento e computação na arquitetura. Essa opção se destaca em cargas de trabalho que podem aproveitar ao máximo a separação entre o armazenamento e a computação, dimensionando com frequência para dar suporte a curtos períodos de atividade na atividade de pico. Essa camada de computação tem o menor ponto de preço de entrada e é dimensionada para dar suporte à maioria das cargas de trabalho do cliente.

- O **nível de desempenho Otimizado para Computação** usa o hardware mais recente do Azure para apresentar um novo cache de disco de estado sólido NVMe que mantém os dados acessados com mais frequência próximos às CPUs, que está exatamente onde você os deseja. Ao criar camadas de armazenamento automaticamente, esse nível de desempenho se destaca com consultas complexas, já que todas as E/S são mantidas locais na camada de computação. Além disso, o columnstore foi aprimorado para armazenar uma quantidade ilimitada de dados no SQL Data Warehouse. O nível de desempenho Otimizado para Computação fornece o maior nível de escalabilidade, permitindo que você aumente até 30.000 cDWU (Unidades de Data Warehouse de computação). Escolha essa camada para cargas de trabalho que exijam desempenho contínuo e ultrarrápido.

## <a name="service-levels"></a>Níveis de serviço
O Objetivo de nível de serviço (SLO) é a configuração de escalabilidade que determina o nível de custo e desempenho do data warehouse. Os níveis de serviço para a escala de nível de desempenho Otimizado para computação são medidos em unidades de Computação do data warehouse (cDWU), por exemplo DW2000c. O nível de serviço Otimizado para Elasticidade é medido em DWUs, por exemplo DW2000. Para obter mais informações, consulte [O que é uma unidade de data warehouse?](what-is-a-data-warehouse-unit-dwu-cdwu.md)

No T-SQL, a configuração SERVICE_OBJECTIVE determina o nível de serviço e o nível de desempenho para o data warehouse.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>Limites máximos de memória
Os níveis de desempenho têm diferentes perfis de memória, que podem ser convertidos em um valor diferente de memória por consulta. O nível de desempenho Otimizado para Computação fornece 2,5 vezes mais memória por consulta do que o nível de desempenho Otimizado para Elasticidade. Essa memória extra ajuda o nível de desempenho Otimizado para Computação entrega seu desempenho ultrarrápido. A memória adicional por consulta também permite que você execute mais consultas simultaneamente, pois as consultas podem usar [classes de recursos](resource-classes-for-workload-management.md) inferiores. 

### <a name="optimized-for-elasticity"></a>Otimizado para elasticidade

Os níveis de serviço para o intervalo de nível de desempenho Otimizado para Elasticidade de DW100 a DW6000. 

| Nível de serviço | Máximo de consultas simultâneas | Nós de computação | Distribuições por nó de computação | Memória máxima por distribuição (MB) | Memória máxima por data warehouse (GB) |
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

Os níveis de serviço para o intervalo de nível de desempenho Otimizado para Computação de DW1000c a DW30000c. 

| Nível de serviço | Máximo de consultas simultâneas | Nós de computação | Distribuições por nó de computação | Memória máxima por distribuição (GB) | Memória máxima por data warehouse (GB) |
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
O SQL Data Warehouse fornece a simultaneidade líder de mercado em um único data warehouse. Para garantir que cada consulta tenha recursos suficientes para executar com eficiência, o sistema rastreia a utilização do recurso de computação por meio da atribuição de slots de simultaneidade a cada consulta. O sistema coloca consultas em uma fila em que elas aguardam até que os slots de simultaneidade suficientes estejam disponíveis. 

Slots de simultaneidade também determinam a priorização de CPU. Para saber mais, confira [Analisar sua carga de trabalho](analyze-your-workload.md)

### <a name="concurrency-slots"></a>Slots de simultaneidade
Slots de simultaneidade são uma maneira conveniente para controlar os recursos disponíveis para execução da consulta. Eles são como tíquetes que você compra para reservar assentos em um concerto, pois a capacidade é limitada. Da mesma forma, o SQL Data Warehouse tem um número limitado de recursos de computação. Consultas de reservam recursos de computação adquirindo slots de simultaneidade. Antes de iniciar a execução de uma consulta, ela deve ser capaz de reserva slots de simultaneidade suficientes. Quando uma consulta for concluída, ela libera seus slots de simultaneidade. 

* O nível de desempenho Otimizado para Elasticidade é dimensionado para 240 slots de simultaneidade.
* O nível de desempenho Otimizado para Computação é dimensionado para 1200 slots de simultaneidade.

Cada consulta consumirá zero, um ou mais slots de simultaneidade. Consultas de sistema e algumas consultas triviais não consomem nenhum slot. Por padrão, as consultas que são governadas pelas classes de recursos exigem um slot de simultaneidade. Consultas mais complexas podem exigir slots de simultaneidade adicionais.  

- Uma consulta em execução com 10 slots de simultaneidade pode acessar 5 vezes mais recursos de computação que uma consulta em execução com 2 slots de simultaneidade.
- Se cada consulta exige 10 slots de simultaneidade e houver 40 slots de simultaneidade, então, apenas 4 consultas podem ser executados simultaneamente.
 
Apenas consultas governadas por recurso consomem slots de simultaneidade. O número exato de slots de simultaneidade consumidos é determinado pela [classe de recurso](resource-classes-for-workload-management.md) da consulta.

### <a name="optimized-for-compute"></a>Otimizado para computação
A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso dinâmico](resource-classes-for-workload-management.md).  Isso se aplica ao nível de desempenho Otimizado para Computação.

**Classes de recursos dinâmicos**
| Nível de Serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | Slots usados pelo smallrc | Slots usados pelo mediumrc | Slots usados pelo largerc | Slots usados pelo xlargerc |
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

