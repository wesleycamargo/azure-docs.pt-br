<properties
	pageTitle="Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure | Microsoft Azure"
	description="Planejamento de capacidade na Pesquisa do Azure baseia-se em combinações de recursos de computador de partição e réplica, em que o preço de cada recurso é definido em unidades de pesquisa faturáveis."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="05/25/2016"
	ms.author="heidist"/>

# Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure

Em Pesquisa do Azure, você poderá ajustar incrementalmente a capacidade de recursos computacionais específicos aumentando partições se precisar de mais armazenamento e E/S ou réplicas para melhor desempenho de indexação e consulta.

A escalabilidade fica disponível quando você provisiona um serviço na [Camada básica](http://aka.ms/azuresearchbasic) ou em uma das [Camadas Standard](search-limits-quotas-capacity.md).

Para todas as camadas faturáveis, a capacidade é comprada em incrementos de *unidades de pesquisa* (SU), em que cada partição e réplica conta como uma SU cada.

- A Básica fornece até 3 SUs por serviço.
- A Standard fornece até 36 SUs por serviço.

Certifique-se de escolher uma combinação de partições e réplicas que fique abaixo do limite da camada. Se você usar o portal para escalar verticalmente, o portal vai impor limites às combinações permitidas.

Como regra geral, aplicativos de pesquisa precisam de mais réplicas que partições. A próxima seção, [alta disponibilidade](#HA), explica por quê.

<a id="HA"></a>
## Obtenção de recursos para alta disponibilidade

Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam. Para muitas implantações, uma partição fornece armazenamento e E/S suficientes (a 15 milhões de documentos por partição).

No entanto, cargas de trabalho de consulta são executadas principalmente em réplicas. Você pode precisar de réplicas adicionais se precisar de mais taxa de transferência ou alta disponibilidade.

Recomendações gerais para alta disponibilidade são:

- Duas réplicas para alta disponibilidade das cargas de trabalho somente leitura (consultas)
- Três ou mais réplicas para alta disponibilidade das cargas de trabalho de leitura/gravação (consultas e indexação)

## Recuperação de desastre

Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância no nível de serviço provisionando um segundo serviço de pesquisa em outra região.

> [AZURE.NOTE] Lembre-se de que a escalabilidade e os contratos de nível de serviço são recursos dos serviços básico e padrão. O serviço gratuito é oferecido em um nível de recurso fixo, com partições e réplicas compartilhadas por vários assinantes. Se você tiver iniciado com o serviço gratuito e agora desejar atualizar, precisará criar um novo serviço de Pesquisa do Azure no nível básico ou padrão e, em seguida, recarregar índices e dados para o novo serviço. Veja [Criar um serviço da Pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções sobre o provisionamento do serviço.

## Terminologia: partições e réplicas

As **Partições** oferecem armazenamento e E/S. Um único serviço de pesquisa pode ter um máximo de 12 partições. Cada partição é fornecida com um limite rígido de 15 milhões de documentos ou 25 GB de armazenamento, o que ocorrer primeiro. Se você adicionar partições, o serviço de pesquisa poderá carregar mais documentos. Por exemplo, um serviço com uma única partição que armazene inicialmente até 25 GB de dados pode armazenar 50 GB quando você adicionar uma segunda partição ao serviço.

**Réplicas** são cópias do mecanismo de pesquisa. Um único serviço de Pesquisa pode ter, no máximo, 12 réplicas. São necessárias pelo menos duas réplicas para disponibilidade de leitura (consulta) e pelo menos três réplicas para disponibilidade de leitura/gravação (consulta, indexação).

## Aumentar o desempenho de consulta com réplicas

A latência da consulta é um indicador de que podem ser necessárias réplicas adicionais. Em geral, é uma primeira etapa para melhorar o desempenho de consulta adicionar mais réplicas.

Uma cópia de cada índice é executada em cada réplica. Conforme você adiciona réplicas, cópias adicionais do índice são colocadas online para oferecer maior suporte a cargas de trabalho de consulta e balancear a carga das solicitações em várias réplicas. Se você tiver vários índices, digamos seis, e três réplicas, cada réplica terá uma cópia de todos os seis índices.

Observe que não fornecemos estimativas fixas para QPS (consultas por segundo): o desempenho da consulta depende da complexidade da consulta e de cargas de trabalho concorrentes. Em média, uma réplica pode atender cerca de 15 QPS, mas a taxa de transferência será um pouco maior ou menor dependendo da complexidade da consulta (consultas facetadas são mais complexas) e da latência de rede. Além disso, é importante reconhecer que, embora adicionar réplicas vai definitivamente adicionar escala e desempenho, o resultado final não é estritamente linear: adicionar três réplicas não garante o triplo da taxa de transferência.

Para saber mais sobre QPS, incluindo métodos para calcular o QPS para suas cargas de trabalho, veja [Gerenciar o serviço da Pesquisa](search-manage.md).

## Aumentar o desempenho de indexação com partições

Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. Adicionar partições distribui operações de leitura-gravação por um número maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para consultar. Assim, você poderá perceber que cada aumento incremental em partições requer um aumento proporcional, mas menor, em réplicas. Conforme observado anteriormente, a complexidade de suas consultas e o volume de consultas influenciarão a rapidez com que a execução da consulta é devolvida.

## Camada Básica: combinações de partição e réplica

Um serviço Básico pode ter 1 partição e até 3 réplicas, com um limite máximo de 3 SUs.

<a id="chart"></a>
## Camada Standard: combinações de partição e réplica

Esta tabela mostra as unidades de pesquisa necessárias para dar suporte a combinações de réplicas e partições, sujeito ao limite de 36 SUs (unidades de pesquisa).

- |- |- |- |- |- |- |
---|----|---|---|---|---|---|
**12 réplicas**|12 SU|24 SU|36 SU|N/D|N/D|N/D|
**6 réplicas**|6 SU|12 SU|18 SU|24 SU|36 SU|N/D|
**5 réplicas**|5 SU|10 SU|15 SU|20 SU|30 SU|N/D|
**4 réplicas**|4 SU|8 SU<|12 SU|16 SU|24 SU|N/|
**3 réplicas**|3 SU|6 SU|9 SU|12 SU|18 SU|36 SU|
**2 réplicas**|2 SU|4 SU|6 SU|8 SU|12 SU|24 SU|
**1 réplica**|1 SU|2 SU|3 SU|4 SU|6 SU|12 SU|
N/D|**1 partição**|**2 partições**|**3 partições**<|**4 partições**|**6 partições**|**12 partições**|


Unidades de pesquisa, preço e capacidade são explicados em detalhes no site da Web do Azure. Veja [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/) para obter mais informações.

> [AZURE.NOTE] O número de réplicas e partições que você escolher deve poder ser dividido de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso ocorre porque a pesquisa do Azure previamente divide cada índice em 12 fragmentos para que possam ser distribuídos entre as partições. Por exemplo, se o serviço tiver três partições e você criar um novo índice, cada partição conterá 4 fragmentos do índice. Como a Pesquisa do Azure fragmenta um índice é um detalhe de implementação, sujeito a alterações em futuras versões. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.

## Calcular unidades de pesquisa para combinações de alocação de recursos específicos: R X P = SU

A fórmula para calcular de quantas SUs você precisa são réplicas multiplicadas por partições. Por exemplo, 3 réplicas multiplicadas por 3 partições são cobradas como 9 unidades de pesquisa.

Ambas as camadas iniciam com uma réplica e uma partição, contadas como uma SU (unidade de pesquisa). Essa é a única instância em que uma réplica e uma partição são contadas como uma unidade de pesquisa. Cada recurso adicional, seja ele uma réplica ou partição, é contado como sua própria SU.

O custo por SU é determinado pela camada. Ele é menor para a camada Básica, quando comparada à Standard. As taxas de cada camada podem ser encontradas em [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/).

<!---HONumber=AcomDC_0601_2016-->