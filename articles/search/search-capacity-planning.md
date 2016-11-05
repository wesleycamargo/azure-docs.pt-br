---
title: Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure | Microsoft Docs
description: Planejamento de capacidade na Pesquisa do Azure baseia-se em combinações de recursos de computador de partição e réplica, em que o preço de cada recurso é definido em unidades de pesquisa faturáveis.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal

ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: heidist

---
# Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure
Depois de [escolher um SKU](search-sku-tier.md) e [provisionar um serviço de pesquisa](search-create-service-portal.md), a próxima etapa é configurar, opcionalmente, os recursos do serviço.

Na Pesquisa do Azure, um serviço recebe inicialmente um nível mínimo de recursos compostos por uma partição e uma réplica. Para tipos que oferecem suporte a isso, você poderá ajustar incrementalmente a capacidade de recursos computacionais aumentando partições, se precisar de mais armazenamento e E/S ou de réplicas para volumes maiores de consulta ou melhor desempenho. Um único serviço deve ter recursos suficientes para manipular todas as cargas de trabalho (indexação e consultas). Você não pode subdividir cargas de trabalho entre vários serviços.

As configurações de escalabilidade ficam disponíveis quando você provisiona um serviço faturável no [tipo Básico](http://aka.ms/azuresearchbasic) ou em um dos [tipos Standard](search-limits-quotas-capacity.md). Para os SKUs faturáveis, a capacidade é comprada em incrementos de *unidades de pesquisa* (SU), em que cada partição e réplica conta como uma SU cada. Ficar abaixo dos limites máximo usa menos SUs, com uma fatura proporcionalmente menor. A cobrança fica em vigor durante o tempo de provisionamento do serviço. Se você não estiver usando um serviço temporariamente, a única maneira de evitar a cobrança é excluindo o serviço e, depois, o recriando quando for necessário.

Para aumentar ou alterar a alocação de réplicas e partições, recomendamos o uso do portal. O portal aplicará limites às combinações permitidas que permaneçam abaixo dos limites máximos:

1. Entre no [Portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.
2. Nas Configurações, abra a folha Escala e use os controles deslizantes para aumentar ou diminuir o número de partições e réplicas.

Como regra geral, os aplicativos de pesquisa precisam de mais réplicas do que partições, especialmente quando as operações de serviço são mais adequadas para cargas de trabalho de consulta. A seção sobre [alta disponibilidade](#HA), explica o motivo.

> [!NOTE]
> Após o provisionamento de um serviço, ele não pode ser atualizado in-loco para um SKU superior. Você precisará criar um novo serviço de pesquisa no novo tipo e recarregar os índices. Confira [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) para obter ajuda com o provisionamento do serviço.
> 
> 

## Terminologia: partições e réplicas
Partições e réplicas são os recursos principais que apoiam um serviço de pesquisa.

**Partições** fornecem armazenamento de índice e E/S para operações de leitura/gravação (por exemplo, ao recompilar ou atualizar um índice).

**Réplicas** são instâncias do serviço de pesquisa, usadas principalmente para equilibrar a carga das operações de consulta. Cada réplica sempre hospeda uma cópia de um índice. Se você tiver 12 réplicas, terá 12 cópias de cada índice carregadas no serviço.

> [!NOTE]
> Não há uma maneira de manipular ou gerenciar diretamente quais índices são executados em uma réplica. Uma cópia de cada índice em cada réplica faz parte da arquitetura do serviço.
> 
> 

<a id="HA"></a>

## Alta disponibilidade
Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam, até o máximo de réplicas e partições com suporte do SKU. Para muitos serviços nos tipos Básico ou S1, uma partição fornece armazenamento e E/S suficientes (a 15 milhões de documentos por partição).

Cargas de trabalho de consulta são executadas principalmente em réplicas. Provavelmente, você precisará de mais réplicas se precisar de mais taxa de transferência ou alta disponibilidade.

Recomendações gerais para alta disponibilidade são:

* Duas réplicas para alta disponibilidade das cargas de trabalho somente leitura (consultas)
* Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas mais indexação à medida que documentos individuais são adicionados, atualizados ou excluídos)

O SLA (Contrato de Nível de Serviço) para a Pesquisa do Azure é direcionado a operações de consulta e a atualizações de índice formadas pela adição, atualização ou exclusão de documentos.

**Disponibilidade de índice durante uma recompilação**

A alta disponibilidade para a Pesquisa do Azure pertence a consultas e atualizações de índice que não envolvem a recompilação de um índice. Se o índice exigir uma recompilação, por exemplo, se você adicionar ou excluir um campo, alterar um tipo de dados ou renomear um campo, será necessário recompilar o índice da seguinte maneira: exclua o índice, recrie o índice e recarregar os dados.

Para manter a disponibilidade do índice durante uma recompilação, é necessário ter uma segunda cópia do índice já em produção no mesmo serviço (com um nome diferente), ou um índice de mesmo nome em um serviço diferente e fornecer o redirecionamento ou lógica de failover em seu código.

## Recuperação de desastre
Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância no nível de serviço provisionando um segundo serviço de pesquisa em outra região. Assim como acontece com a disponibilidade durante uma recompilação de índice, o redirecionamento ou a lógica de failover deve vir de seu código.

## Aumentar o desempenho de consulta com réplicas
A latência da consulta é um indicador da necessidade de réplicas adicionais. Em geral, uma primeira etapa para melhorar o desempenho de consulta é adicionar mais desse recurso. Conforme você adiciona réplicas, cópias adicionais do índice são colocadas online para oferecer maior suporte a cargas de trabalho de consulta e balancear a carga das solicitações em várias réplicas.

Observe que não podemos fornecer estimativas fixas para QPS (consultas por segundo): o desempenho da consulta depende da complexidade da consulta e de cargas de trabalho concorrentes. Em média, uma réplica em SKUs Básico ou S1 pode atender cerca de 15 QPS, mas a taxa de transferência será um pouco maior ou menor dependendo da complexidade da consulta (consultas facetadas são mais complexas) e da latência de rede. Além disso, é importante reconhecer que, embora adicionar réplicas vai definitivamente adicionar escala e desempenho, o resultado final não é estritamente linear: adicionar três réplicas não garante o triplo da taxa de transferência.

Para saber mais sobre QPS, incluindo métodos para calcular o QPS para suas cargas de trabalho, veja [Gerenciar o serviço da Pesquisa](search-manage.md).

## Aumentar o desempenho de indexação com partições
Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. Adicionar partições distribui operações de leitura-gravação por um número maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para consultar. Assim, você poderá perceber que cada aumento incremental em partições requer um aumento proporcional, mas menor, em réplicas. Conforme observado anteriormente, a complexidade de suas consultas e o volume de consultas influenciarão a rapidez com que a execução da consulta é devolvida.

## Camada Básica: combinações de partição e réplica
Um serviço Básico pode ter exatamente uma partição e até três réplicas, com um limite máximo de três SUs. O único recurso ajustável são as réplicas. Conforme observado anteriormente, você precisa de um mínimo de duas réplicas para alta disponibilidade em consultas.

<a id="chart"></a>

## Tipos Standard: combinações de partição e réplica
Esta tabela mostra as unidades de pesquisa necessárias para dar suporte a combinações de réplicas e partições, sujeito ao limite de 36 SUs (unidades de pesquisa) (exclui os tipos Básico e S3 HD).

| - | - | - | - | - | - | - |
| --- | --- | --- | --- | --- | --- | --- |
| **12 réplicas** |12 SU |24 SU |36 SU |N/D |N/D |N/D |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/D |
| **5 réplicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/D |
| **4 réplicas** |4 SU |8 SU< |12 SU |16 SU |24 SU |N/ |
| **3 réplicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| N/D |**1 partição** |**2 partições** |**3 partições**< |**4 partições** |**6 partições** |**12 partições** |

Unidades de pesquisa, preço e capacidade são explicados em detalhes no site da Web do Azure. Veja [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/) para obter mais informações.

> [!NOTE]
> O número de réplicas e partições que você escolher deve poder ser dividido de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso ocorre porque a Pesquisa do Azure divide previamente cada índice em 12 fragmentos, para que possam ser distribuídos em partes iguais entre todas as partições. Por exemplo, se o serviço tiver três partições e você criar um novo índice, cada partição conterá 4 fragmentos do índice. Como a Pesquisa do Azure fragmenta um índice é um detalhe de implementação, sujeito a alterações em futuras versões. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.
> 
> 

## S3 de Alta Densidade: combinações de partição e réplica
Um S2 HD tem uma partição e até 12 réplicas, com um limite máximo de 12 SUs. O único recurso ajustável são as réplicas.

## Calcular unidades de pesquisa para combinações de alocação de recursos específicos: R X P = SU
A fórmula para calcular de quantas SUs você precisa são réplicas multiplicadas por partições. Por exemplo, 3 réplicas multiplicadas por 3 partições são cobradas como 9 unidades de pesquisa.

Ambas as camadas iniciam com uma réplica e uma partição, contadas como uma SU (unidade de pesquisa). Essa é a única instância em que uma réplica e uma partição são contadas como uma unidade de pesquisa. Cada recurso adicional, seja ele uma réplica ou partição, é contado como sua própria SU.

O custo por SU é determinado pela camada. Ele é menor para a camada Básica, quando comparada à Standard. As taxas de cada camada podem ser encontradas em [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/).

<!---HONumber=AcomDC_0914_2016-->