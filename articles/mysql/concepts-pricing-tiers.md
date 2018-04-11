---
title: Tipos de preço para o Banco de Dados do Azure para MySQL
description: Este artigo descreve os tipos de preço para o Banco de Dados do Azure para MySQL.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 9d4e42df3137108248a043bb0d9def181d766c7a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Tipos de preço no Banco de Dados do Azure para MySQL

Você pode criar um servidor do Banco de Dados do Azure para MySQL em um dos três tipos de preço diferentes: Básico, Uso Geral e Otimizado para Memória. Os tipos de preço são diferenciados pela quantidade de computação nos vCores que pode ser provisionada, pela memória por vCore e pela tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor MySQL. Um servidor pode ter um ou vários bancos de dados.

|    | **Básico** | **Uso geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Memória por vCore | Linha de base | 2 x Básico | 2x Uso Geral |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 1 TB | 5 GB a 1 TB |
| Tipo de armazenamento | Armazenamento Standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Período de retenção do backup de banco de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um tipo de preço, use a tabela a seguir como ponto de partida.

| Tipo de preço  | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Basic | Cargas de trabalho que exigem desempenho de E/S e computação leve. Os exemplos incluem servidores usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| Uso geral | A maioria das cargas de trabalho que exigem a computação e a memória balanceadas com a taxa de transferência de E/S escalonável. Os exemplos incluem servidores para hospedar aplicativos web e móveis e outros aplicativos corporativos.|
| Otimizado para memória | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para o processamento de transações mais rápido e com simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, o número de vCores pode ser aumentado ou reduzido em segundos. Você pode também, independentemente, ajustar a quantidade de armazenamento de backup e o período de retenção de backup para cima ou para baixo sem tempo de inatividade do aplicativo. Para obter mais informações, consulte a seção “Recursos de dimensionamento”.

## <a name="compute-generations-vcores-and-memory"></a>Gerações de computação, vCores e memória

Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. No momento, você pode escolher entre duas gerações de computação, Gen 4 e 5. As CPUs lógicas de 4ª geração são baseadas em processadores Intel E5-2673 v3 (Haswell) 2,4 GHz. As CPUs lógicas de 5ª geração são baseadas em processadores E5-2673 v4 (Broadwell) 2,3 GHz. As Gerações 4 e 5 estão disponíveis nas seguintes regiões (o "X" indica disponível). 

| **Região do Azure** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Centro dos EUA |  | X |
| Leste dos EUA | X | X |
| Leste dos EUA 2 | X |  |
| Centro-Norte dos EUA | X |  |
| Centro-Sul dos Estados Unidos | X |  |
| Oeste dos EUA | X | X |
| Oeste dos EUA 2 |  | X |
| Canadá Central | X | X |
| Leste do Canadá | X | X |
| Sul do Brasil | X |  |
| Norte da Europa | X | X |
| Europa Ocidental | X | X |
| Oeste do Reino Unido |  | X |
| Sul do Reino Unido |  | X |
| Ásia Oriental | X |  |
| Sudeste Asiático | X |  |
| Leste da Austrália |  | X |
| Índia Central | X |  |
| Índia Ocidental | X |  |
| Leste do Japão | X |  |
| Oeste do Japão | X |  |
| Sul da Coreia |  | X |

Dependendo do tipo de preço, cada vCore é configurado com uma quantidade específica de memória. Quando você aumenta ou reduz o número de vCores para o servidor, a memória aumenta ou diminui proporcionalmente. A camada de uso geral fornece o dobro da quantidade de memória por vCore comparada com a camada Básico. O nível de otimização de memória fornece o dobro da quantidade de memória comparado com a camada de uso geral.

## <a name="storage"></a>Armazenamento

O armazenamento provisionado é a quantidade de capacidade de armazenamento disponível para o Banco de Dados do Azure para servidor MySQL. O armazenamento é usado para os arquivos de banco de dados, os logs de transações e os logs do servidor MySQL. A quantidade total de armazenamento que você provisiona também define a capacidade disponível para o servidor.

|    | **Básico** | **Uso geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 1 TB | 5 GB a 1 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Mín 100 IOPS | 3 IOPS/GB<br/>Mín 100 IOPS |

Você pode adicionar mais capacidade de armazenamento durante e após a criação do servidor. A camada Básico não oferece garantia de IOPS. Nos tipos de preço Uso Geral e Otimizado para Memória, o IOPS é dimensionado com o tamanho de armazenamento provisionado a uma taxa de 3:1.

Você pode monitorar o consumo de E/S no Portal do Azure ou usando os comandos da CLI do Azure. As métricas relevantes para monitorar são o [limite de armazenamento, porcentagem de armazenamento, armazenamento usado e porcentagem de IO](concepts-monitoring.md).

## <a name="backup"></a>Backup

O serviço faz backups do servidor automaticamente. O período de retenção mínimo para backups é de sete dias. Você pode definir um período de retenção de até 35 dias. A retenção pode ser ajustada a qualquer momento durante o tempo de vida do servidor. Você pode escolher entre backups com redundância geográfica e com redundância local. Backups com redundância geográfica também são armazenados na [região emparelhada geograficamente](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) da região em que seu servidor será criado. Essa redundância fornece um nível de proteção em caso de desastre. Você também ganha a capacidade de restaurar o servidor para qualquer outra região do Azure na qual o serviço esteja disponível com os backups com redundância geográfica. Não é possível alterar entre as duas opções de armazenamento de backup depois que o servidor é criado.

## <a name="scale-resources"></a>Recursos de escala

Depois de criar seu servidor, você pode, independentemente, alterar vCores, a quantidade de armazenamento e o período de retenção de backup. Você não pode alterar o tipo de preço ou o tipo de armazenamento de backup depois que um servidor é criado. Os vCores e o período de retenção de backup podem ser aumentados ou reduzidos. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou da CLI do Azure. Para obter um exemplo de dimensionamento usando a CLI do Azure, consulte [Monitorar e dimensionar um servidor do Banco de Dados do Azure para MySQL usando a CLI do Azure](scripts/sample-scale-server.md).

Ao alterar o número de vCores, uma cópia do servidor original é criada com a nova alocação de computação. Depois que o novo servidor entra em execução, as conexões são alternadas para o novo servidor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Esse período varia, mas na maioria dos casos fica abaixo um minuto.

O dimensionamento do armazenamento e a alteração do período de retenção de backup são operações realmente online. Não há nenhum tempo de inatividade e o aplicativo não é afetado. Conforme o IOPS é dimensionado com o tamanho do armazenamento provisionado, você pode aumentar o IOPS disponível para seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/mysql/) do serviço. Para ver os custos da configuração desejada, o [Portal do Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) mostra o custo mensal na guia **Tipo de preço** com base nas opções que você seleciona. Se você não tem uma assinatura do Azure, pode a calculadora de preços do Azure para obter um preço estimado. No site da [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), selecione **Adicionar itens**, expanda a categoria **Bancos de dados** e escolha **Banco de Dados do Azure para MySQL** para personalizar as opções.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um servidor MySQL no portal](howto-create-manage-server-portal.md).
- Saiba como [monitorar e dimensionar um Banco de Dados do Azure para servidor MySQL usando a CLI do Azure](scripts/sample-scale-server.md).
- Saiba mais sobre as [limitações de serviço](concepts-limits.md).
