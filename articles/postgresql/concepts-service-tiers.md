---
title: "Camadas de serviço no Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Camadas de serviço no Banco de Dados do Azure para PostgreSQL"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/16/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: a946c114824597cc55e435a455cd888816789dbf
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções e desempenho do Banco de Dados do Azure para PostgreSQL: compreender o que está disponível em cada camada de serviço

Enquanto estiver no modo de visualização, o Banco de Dados do Azure para PostgreSQL oferece apenas as camadas de serviço Básico e Standard. A camada Premium ainda não está disponível.

Cada camada de serviço tem vários níveis de desempenho para lidar com tipos diferentes de requisitos de cargas de trabalho. Os níveis de desempenho mais elevados fornecem recursos adicionais, projetados para oferecer uma taxa de transferência crescente. Você pode alterar dinamicamente os níveis de desempenho dentro de uma camada de serviço sem qualquer tempo de inatividade do aplicativo.

No futuro, será possível atualizar ou fazer downgrade de uma camada de serviço para outra.

> [!IMPORTANT]
> No momento, o serviço está em visualização pública e, portanto, não ainda fornece um SLA (Contrato de Nível de Serviço).

Você pode criar um único servidor PostgreSQL com recursos dedicados dentro de uma camada de serviço em um nível de desempenho específico. Depois, você pode criar de um a vários bancos de dados no servidor no qual os recursos são compartilhados entre vários bancos de dados. Os recursos disponíveis para um único servidor PostgreSQL são expressos em termos de Unidades de Computação e Unidades de Armazenamento. Para saber mais sobre Unidades de Computação e Unidades de Armazenamento, confira [Explicar a Unidade de Computação e a Unidade de Armazenamento](concepts-compute-unit-and-storage.md)

## <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço

A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
| :----------- | :----------------|
| Basic | Mais adequada para cargas de trabalho pequenas que exigem computação e armazenamento escalonáveis sem garantia de IOPS. Os exemplos incluem servidores usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| Standard | A opção certa para aplicativos de nuvem que precisam de garantia de IOPS com uma capacidade de escalar de forma independente para níveis de computação e armazenamento superiores para obtenção de uma taxa de transferência maior. Entre os exemplos incluem aplicativos analíticos ou da Web. |
| Premium | Ideal para cargas de trabalho que precisam de latências muito breves para transações e E/S, juntamente com alta taxa de transferência de carga de trabalho de E/S. Fornece o melhor suporte para muitos usuários simultâneos. Aplicável a bancos de dados que oferece suporte a aplicativos de missão crítica.<br />A camada de serviço Premium não está disponível no modo visualização. |
| &nbsp; | &nbsp; |

Para decidir sobre uma camada de serviço, comece determinando se sua carga de trabalho precisa de garantia de IOPS. Depois, determine os recursos mínimos necessários:

| **Recursos de camada de serviço** | **Básico** | **Standard** | **Premium** * |
| :------------------------ | :-------- | :----------- | :------------ |
| Unidades de computação máxima | 100 | 2.000 | Não disponível no modo visualização |
| Armazenamento total máximo | 1.050 GB | 10.000 GB | Não disponível no modo visualização |
| Garantia IOPS de armazenamento | N/D  | Sim | Não disponível no modo visualização |
| IOPS de armazenamento máximo | N/D  | 3.000 | Não disponível no modo visualização |
| Período de retenção do backup de banco de dados | 7 dias | 35 dias | 35 dias |


> [!NOTE]
> A camada de serviço Standard, atualmente na versão prévia, dá suporte a até 800 Unidades de computação e, no máximo, 1.000 GB de armazenamento.

Depois de determinar a camada de serviço mínima, você está pronto para determinar o nível de desempenho do servidor PostgreSQL (as Unidades de Computação). As Unidades de Computação 200 e 400 padrão costumam ser um bom ponto de partida para aplicativos que exigem maior simultaneidade de usuários para suas cargas de trabalho de análise ou da Web. 

No entanto, você pode expandir ou reduzir as Unidades de Computação de forma independente às Unidades de Armazenamento, com base nos requisitos da carga de trabalho. Se a carga de trabalho precisar de um ajuste com relação aos recursos de computação, aumente ou diminua dinamicamente as Unidades de Computação. Se sua carga de trabalho precisar de mais IOPS ou armazenamento, você também poderá dimensionar o Armazenamento.

> [!NOTE]
> No momento, no modo visualização, as camadas Básico e Standard não dão suporte ao dimensionamento dinâmico do armazenamento. Planejamos adicionar esse recurso no futuro.

> [!NOTE]
> Na camada de serviço Standard, o IOPS é dimensionado proporcionalmente ao tamanho do armazenamento provisionado em uma taxa fixa de 3:1. O armazenamento incluído de 125 GB garante 375 IOPS provisionados, cada um com um tamanho de E/S de 256 KB. Se você provisionar 1.000 GB, obterá 3.000 IOPS provisionados. Você deve monitorar o consumo das unidades de computação do servidor e escalar verticalmente a fim de utilizar totalmente o IOPS provisionado disponível.

## <a name="service-tiers-and-performance-levels"></a>Camadas de serviço e níveis de desempenho

O Banco de Dados do Azure para PostgreSQL oferece vários níveis de desempenho dentro de cada camada de serviço. Você tem a flexibilidade de escolher o nível que melhor atenda às suas demandas de carga de trabalho usando uma das opções a seguir:

- [Portal do Azure](quickstart-create-server-database-portal.md), localizado em [http://portal.azure.com](http://portal.azure.com)
- [CLI do Azure](quickstart-create-server-database-azure-cli.md)

Independentemente do número de bancos de dados hospedados em cada servidor PostgreSQL, o banco de dados ainda obterá um conjunto garantido de recursos e as características de desempenho esperadas de seu servidor não são afetadas.

### <a name="basic-service-tier"></a>Camada de serviço Básico:

| **Nível de desempenho** | **50** | **100** |
| --------------------: | :----- | :------ |
| Unidades de computação máxima | 50 | 100 |
| Tamanho do armazenamento incluído | 50 GB | 50 GB |
| Tamanho máximo de armazenamento do servidor\* | 1.050 GB | 1.050 GB |

### <a name="standard-service-tier"></a>Camada de serviço Standard:

| **Nível de desempenho** | **100** | **200** | **400** | **800** |
| --------------------: | :------ | :------ | :------ | :------ |
| Unidades de computação máxima | 100 | 200 | 400 | 800 |
| Tamanho de armazenamento incluído e IOPS provisionado | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS |
| Tamanho máximo de armazenamento do servidor\* | 1 TB | 1 TB | 1 TB | 1 TB |
| IOPS máximo provisionado do servidor | 3.000 IOPS | 3.000 IOPS | 3.000 IOPS | 3.000 IOPS |
| IOPS máximo provisionado do servidor por GB | 3 IOPS fixos por GB | 3 IOPS fixos por GB | 3 IOPS fixos por GB | 3 IOPS fixos por GB |

\* Tamanho máximo de armazenamento do servidor refere-se ao tamanho máximo de armazenamento provisionado para o servidor.

## <a name="scaling-up-or-down-a-server"></a>Aumentar ou diminuir um único servidor

Depois de escolher inicialmente uma camada de serviço e um nível de desempenho, você pode escalar verticalmente e de forma dinâmica o servidor com base em seus requisitos de carga de trabalho. Se precisar escalar verticalmente, altere facilmente a camada de seu banco de dados usando o Portal do Azure ou a CLI do Azure.

A alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. Essa janela varia, mas é em média inferior a quatro segundos e, em mais de 99% dos casos, de menos de 30 segundos. Se houver grandes números de transações em andamento no momento em que as conexões estiverem desabilitadas, esta janela poderá ser maior.

A duração de todo o processo de expansão depende a camada tamanho e de serviço do servidor antes e após a alteração. Por exemplo, um servidor que está mudando as Unidades de Computação, de, para ou dentro de uma camada de serviço Standard, deverá concluir dentro de seis horas. As novas propriedades do servidor não serão aplicadas até que as alterações sejam concluídas.

Você pode usar o portal do Azure para escalar verticalmente, ou usar a CLI do Azure para monitorar e escalar seu servidor. Consulte: [Monitorar e escalar um único servidor PostgreSQL usando a CLI do Azure](scripts/sample-scale-server-up-or-down.md)

### <a name="details-about-scaling-up-or-down"></a>Detalhes sobre como escalar verticalmente

- Para fazer o downgrade de um servidor, as Unidades de Armazenamento dele devem ter um tamanho inferior ao máximo permitido para a camada de serviço de destino.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Como fazer backup e restaurar um Banco de Dados do Azure para servidor PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
- As novas propriedades do servidor não serão aplicadas até que as alterações sejam concluídas.

