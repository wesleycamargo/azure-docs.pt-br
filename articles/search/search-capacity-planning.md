<properties
	pageTitle="Planejamento de capacidade na Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedada"
	description="A capacidade da Pesquisa do Azure baseia-se em partições e réplicas, em que cada uma é uma unidade de pesquisa faturável."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Planejamento de Capacidade na Pesquisa do Azure

Na Pesquisa do Azure, é possível ajustar de forma incremental a capacidade de recursos computacionais específicos. É possível aumentar as partições se precisar de mais armazenamento e E/S, ou réplicas para um melhor desempenho de indexação e consulta.

A escalabilidade fica disponível quando você provisiona um serviço na [Camada básica](http://aka.ms/azuresearchbasic) ou em uma das [Camadas Standard](search-limits-quotas-capacity.md).

Para ambas as camadas, a capacidade é comprada em incrementos de *unidades de pesquisa* (SU), em que cada partição e réplica são contadas como uma SU cada uma.

- A Básica fornece até 3 SUs por serviço.
- A Standard fornece até 36 SUs por serviço.

É necessário escolher uma combinação de partições e réplicas que fique abaixo do limite da camada. Por exemplo, você não poderia escalonar o serviço Standard até 12 partições e 6 réplicas, já que isso exigiria 72 unidades de pesquisa (12 x 6), excedendo o limite de 36 unidades de pesquisa por serviço.

Em geral, os aplicativos de pesquisa tendem a precisar de mais réplicas do que partições. Veja a seção sobre [alta disponibilidade](#HA) para obter detalhes.

**Calcular unidades de pesquisa para combinações de alocação de recursos específicos: R X P = SU**

A fórmula para calcular de quantas SUs você precisa são réplicas multiplicadas por partições. Por exemplo, 3 réplicas multiplicadas por 3 partições são cobradas como 9 unidades de pesquisa.

Ambas as camadas iniciam com uma réplica e uma partição, contadas como uma SU (unidade de pesquisa). Essa é a única instância em que uma réplica e uma partição são contadas como uma unidade de pesquisa. Cada recurso adicional, seja ele uma réplica ou partição, é contado como sua própria SU.

O custo por SU é determinado pela camada. Ele é menor para a camada Básica, quando comparada à Standard. As taxas de cada camada podem ser encontradas em [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/).

## Camada Básica: combinações de partição e réplica

Um serviço Básico pode ter 1 partição e até 3 réplicas, com um limite máximo de 3 SUs.

<table cellspacing="0" border="1">
<tr><td><b>3 réplicas</b></td><td>3 SU</td></tr>
<tr><td><b>2 réplicas</b></td><td>2 SU</td></tr>
<tr><td><b>1 réplica</b></td><td>1 SU</td></tr>
<tr><td></td><td><b>1 partição</b></td></tr>
</table>

<a id="chart"></a>
## Camada Standard: combinações de partição e réplica

Esta tabela mostra as unidades de pesquisa necessárias para dar suporte a combinações de réplicas e partições, sujeito ao limite de 36 SUs (unidades de pesquisa).

<table cellspacing="0" border="1">
<tr><td><b>12 réplicas</b></td><td>12 SU</td><td>24 SU</td><td>36 SU</td><td>N/D</td><td>N/D</td><td>N/D</td></tr>
<tr><td><b>6 réplicas</b></td><td>6 SU</td><td>12 SU</td><td>18 SU</td><td>24 SU</td><td>36 SU</td><td>N/D</td></tr>
<tr><td><b>5 réplicas</b></td><td>5 SU</td><td>10 SU</td><td>15 SU</td><td>20 SU</td><td>30 SU</td><td>N/D</td></tr>
<tr><td><b>4 réplicas</b></td><td>4 SU</td><td>8 SU</td><td>12 SU</td><td>16 SU</td><td>24 SU</td><td>N/D </td></tr>
<tr><td><b>3 réplicas</b></td><td>3 SU</td><td>6 SU</td><td>9 SU</td><td>12 SU</td><td>18 SU</td><td>36 SU</td></tr>
<tr><td><b>2 réplicas</b></td><td>2 SU</td><td>4 SU</td><td>6 SU</td><td>8 SU</td><td>12 SU</td><td>24 SU</td></tr>
<tr><td><b>1 réplica</b></td><td>1 SU</td><td>2 SU</td><td>3 SU</td><td>4 SU</td><td>6 SU</td><td>12 SU</td></tr>
<tr><td>N/D</td><td><b>1 partição</b></td><td><b>2 partições</b></td><td><b>3 partições</b></td><td><b>4 partições</b></td><td><b>6 partições</b></td><td><b>12 partições</b></td></tr>
</table>

Unidades de pesquisa, preço e capacidade são explicados em detalhes no site da Web do Azure. Veja [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/) para obter mais informações.

> [AZURE.NOTE] O número de réplicas e partições que você escolher deve poder ser dividido de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso ocorre porque a pesquisa do Azure previamente divide cada índice em 12 fragmentos para que possam ser distribuídos entre as partições. Por exemplo, se o serviço tiver três partições e você criar um novo índice, cada partição conterá 4 fragmentos do índice. Como a Pesquisa do Azure fragmenta um índice é um detalhe de implementação, sujeito a alterações em futuras versões. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.

<a id="HA"></a>
## Escolher uma combinação de partições e réplicas para alta disponibilidade

Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam. Para muitas implantações, uma partição fornece armazenamento e E/S suficientes (a 15 milhões de documentos por partição).

Cargas de trabalho de consulta, no entanto, dependem das réplicas. Você pode precisar de réplicas adicionais se precisar de mais taxa de transferência ou alta disponibilidade.

Recomendações gerais para alta disponibilidade são:

- Duas réplicas para alta disponibilidade das cargas de trabalho somente leitura (consultas)
- Três ou mais réplicas para alta disponibilidade das cargas de trabalho de leitura/gravação (consultas e indexação)

Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. Em vez disso, você pode adicionar redundância no nível de serviço. Para obter uma discussão mais aprofundada da soluções alternativas, veja [esta postagem no fórum](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE] Lembre-se de que a escalabilidade e os contratos de nível de serviço são recursos do serviço padrão. O serviço gratuito é oferecido em um nível de recurso fixo, com partições e réplicas compartilhadas por vários assinantes. Se você tiver iniciado com o serviço gratuito e agora desejar atualizar, precisará criar um novo serviço de Pesquisa do Azure no nível padrão e, em seguida, recarregar índices e dados para o novo serviço. Veja [Criar um serviço da Pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções sobre o provisionamento do serviço.

## Sobre partições e réplicas

As **Partições** oferecem armazenamento e E/S. Um único serviço de pesquisa pode ter um máximo de 12 partições. Cada partição é fornecida com um limite rígido de 15 milhões de documentos ou 25 GB de armazenamento, o que ocorrer primeiro. Se você adicionar partições, o serviço de pesquisa poderá carregar mais documentos. Por exemplo, um serviço com uma única partição que armazene inicialmente até 25 GB de dados pode armazenar 50 GB quando você adicionar uma segunda partição ao serviço.

**Réplicas** são cópias do mecanismo de pesquisa. Um único serviço de Pesquisa pode ter, no máximo, 12 réplicas. São necessárias pelo menos duas réplicas para disponibilidade de leitura (consulta) e pelo menos três réplicas para disponibilidade de leitura/gravação (consulta, indexação).

Uma cópia de cada índice é executada em cada réplica. Conforme você adiciona réplicas, cópias adicionais do índice são colocadas online para oferecer maior suporte a cargas de trabalho de consulta e balancear a carga das solicitações em várias réplicas. Se você tiver vários índices, digamos seis, e três réplicas, cada réplica terá uma cópia de todos os seis índices.

Observe que não fornecemos estimativas rígidas quanto a QPS (consultas por segundo), já que a execução da consulta pode variar muito conforme a complexidade da consulta e cargas de trabalho concorrentes. Em média, uma réplica pode atender cerca de 15 QPS, mas a taxa de transferência será um pouco maior ou menor dependendo da complexidade da consulta (consultas facetadas são mais complexas) e da latência de rede. Além disso, é importante reconhecer que, embora adicionar réplicas vai definitivamente adicionar escala e desempenho, o resultado final não é estritamente linear: adicionar três réplicas não garante o triplo da taxa de transferência. A latência da consulta é um indicador de que podem ser necessárias réplicas adicionais. Para saber mais sobre QPS, incluindo métodos para calcular o QPS para suas cargas de trabalho, veja [Gerenciar o serviço da Pesquisa](search-manage.md).

<!---HONumber=AcomDC_0518_2016-->