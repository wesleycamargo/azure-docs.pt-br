<properties
	pageTitle="Limites de serviço na Pesquisa do Azure"
	description="Os limites de Pesquisa do Azure usados no planejamento de capacidade e limites máximos em solicitações e respostas."
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
	ms.date="08/18/2015"
	ms.author="heidist"/>

#Limites de serviço na Pesquisa do Azure

Limites máximos em armazenamento, cargas de trabalho e quantidades de índices, documentos e outros objetos dependem de como você se inscreve na Pesquisa do Azure. O serviço gratuito destina-se a avaliação e teste de prova de conceito, com limites inferiores em todos os objetos e cargas de trabalho para que os recursos possam ser compartilhados de modo mais equitativo.

Execuções padrão em máquinas dedicadas que são usadas apenas pelo seu serviço. O uso exclusivo de um serviço dedicado possibilita escalar ou reduzir verticalmente, com mais capacidade de armazenamento e processamento em cada nível, incluindo a configuração mínima.

##Limites máximos para um serviço gratuito de Pesquisa (compartilhado)

Os assinantes do Azure podem usar o serviço de pesquisa (multilocatário) compartilhado para desenvolvimento ou aplicativos de pesquisa muito pequenos. O serviço compartilhado é fornecido com sua assinatura do Azure. É uma opção sem custos que permite experimentar o serviço antes de inscrever-se. Ela oferece:

Objeto|Limite
------|-----
Número máximo de índices|3
Número máximo de campos por índice|1000
Contagem máxima de documento|10\.000
Tamanho máximo de armazenamento|50 MB
Máximo de partições|N/D
Máximo de réplicas|N/D
Máximo de unidades de pesquisa|N/D
Número máximo de indexadores|3
Número máximo de fontes de dados do indexador|3
Número máximo de documentos indexados por invocação do indexador|10\.000

Observe que não há cotas nem limites máximos associados a consultas. As QPSs (consultas por segundo) variam, dependendo da largura de banda disponível e da competição por recursos do sistema. Os recursos de computação e armazenamento do Azure que apoiam o serviço compartilhado são compartilhados por vários assinantes, portanto, a QPS para sua solução vai variar conforme o número de outras cargas de trabalho em execução ao mesmo tempo.

##Limites máximos para um serviço de pesquisa padrão (dedicado)

Na camada de preços Standard, um serviço de pesquisa dedicado armazena somente dados e executa apenas as cargas de trabalho. Diferente do serviço compartilhado, a alocação de recurso para um serviço de pesquisa dedicado é ajustável, podendo ser dimensionada para qualquer nível necessário. Você pode definir de modo independente os níveis de recurso para partições (para escalar verticalmente o armazenamento) e réplicas (para fornecer alta disponibilidade e escalar verticalmente QPS e desempenho de indexação). Consulte [Gerenciar a solução de pesquisa](search-manage.md) para informações sobre configurações de recursos diferentes.

A tabela a seguir é uma lista de limites superiores, mas examine o gráfico de matriz adicional para compreender a capacidade em termos de [combinações de partições e réplicas](#chart) permitidas.

Objeto|Limite
------|----
Número máximo de índices|50 por serviço de Pesquisa
Número máximo de campos por índice|1000
Contagem máxima de documento|15 milhões por partição
Tamanho máximo de armazenamento|25 GB por partição
Máximo de partições|12 por serviço de Pesquisa
Máximo de réplicas|6 por serviço de Pesquisa
Máximo de unidades de pesquisa|36 por serviço de Pesquisa
Número máximo de indexadores|50 por serviço de Pesquisa
Número máximo de fontes de dados do indexador|50 por serviço de Pesquisa
Número máximo de documentos indexados por invocação do indexador|Ilimitado

A capacidade na Pesquisa do Azure pode ser adquirida em incrementos chamados de unidades de pesquisa. A camada de preços Standard permite até 36 unidades de pesquisa por serviço de pesquisa. Esse limite substitui os limites individuais em partições e réplicas. Por exemplo, você não pode dimensionar seu serviço até 12 partições e 6 réplicas, já que fazer isso exigiria 72 unidades de pesquisa (12 x 6), excedendo o limite de 36 unidades de pesquisa por serviço.

##Sobre partições e réplicas

**Partições** oferecem armazenamento e E/S. Um único serviço de pesquisa pode ter um máximo de 12 partições. Cada partição é fornecida com um limite rígido de 15 milhões de documentos ou 25 GB de armazenamento, o que ocorrer primeiro. Se você adicionar partições, o serviço de pesquisa poderá carregar mais documentos. Por exemplo, um serviço com uma única partição que armazene inicialmente até 25 GB de dados pode armazenar 50 GB quando você adicionar uma segunda partição ao serviço.

**Réplicas** são cópias do mecanismo de pesquisa. Um único serviço de pesquisa pode ter um máximo de seis réplicas. São necessárias pelo menos duas réplicas para disponibilidade de leitura (consulta) e pelo menos três réplicas para disponibilidade de leitura/gravação (consulta, indexação).

Uma cópia de cada índice é executada em cada réplica. Conforme você adiciona réplicas, cópias adicionais do índice são colocadas online para oferecer maior suporte a cargas de trabalho de consulta e balancear a carga das solicitações em várias réplicas. Se você tiver vários índices, digamos seis, e três réplicas, cada réplica terá uma cópia de todos os seis índices.

Observe que não fornecemos estimativas rígidas quanto a QPS (consultas por segundo), já que a execução da consulta pode variar muito conforme a complexidade da consulta e cargas de trabalho concorrentes. Em média, uma réplica pode atender cerca de 15 QPS, mas a taxa de transferência será um pouco maior ou menor dependendo da complexidade da consulta (consultas facetadas são mais complexas) e da latência de rede. Além disso, é importante reconhecer que, embora adicionar réplicas vai definitivamente adicionar escala e desempenho, o resultado final não é estritamente linear: adicionar três réplicas não garante o triplo da taxa de transferência. A latência da consulta é um indicador de que podem ser necessárias réplicas adicionais.

<a id="chart"></a>
##Combinações de partições e réplicas com suporte

Conforme observado anteriormente, o limite efetivo em partições e réplicas baseia-se na combinação de recursos que você selecionar, ao mesmo tempo mantendo o limite de 36 unidades de pesquisa por serviço. Os recursos são alocados em termos de SU (unidades de pesquisa). Um serviço de Pesquisa dedicado começa com uma réplica e uma partição, como uma unidade de pesquisa.

Capacidade adicional é calculada como partições multiplicadas por réplicas, produzindo um número total de unidades de pesquisa necessárias para oferecer suporte a uma determinada configuração.

A tabela a seguir é um gráfico que lista as réplicas no eixo vertical e as partições no eixo horizontal. A interseção mostra o número de unidades de pesquisa necessárias para oferecer suporte a cada combinação, sujeito ao limite de 36 unidades de pesquisa (SU) por serviço. Por exemplo, se você quiser seis réplicas e duas partições, essa configuração exigiria 12 unidades de pesquisa. Para usar quatro réplicas e duas partições, você precisaria de oito unidades de pesquisa. Como regra geral, a maioria dos aplicativos de pesquisa normalmente precisa de mais réplicas do que de partições.

<table cellspacing="0" border="1">
<tr><td><b>6 réplicas</b></td><td>6 SU</td><td>12 SU</td><td>18 SU</td><td>24 SU</td><td>36 SU</td><td>N/D</td></tr>
<tr><td><b>5 réplicas</b></td><td>5 SU</td><td>10 SU</td><td>15 SU</td><td>20 SU</td><td>30 SU</td><td>N/D</td></tr>
<tr><td><b>4 réplicas</b></td><td>4 SU</td><td>8 SU</td><td>12 SU</td><td>16 SU</td><td>24 SU</td><td>N/D </td></tr>
<tr><td><b>3 réplicas</b></td><td>3 SU</td><td>6 SU</td><td>9 SU</td><td>12 SU</td><td>18 SU</td><td>36 SU</td></tr>
<tr><td><b>2 réplicas</b></td><td>2 SU</td><td>4 SU</td><td>6 SU</td><td>8 SU</td><td>12 SU</td><td>24 SU</td></tr>
<tr><td><b>1 réplica</b></td><td>1 SU</td><td>2 SU</td><td>3 SU</td><td>4 SU</td><td>6 SU</td><td>12 SU</td></tr>
<tr><td>N/D</td><td><b>1 partição</b></td><td><b>2 partições</b></td><td><b>3 partições</b></td><td><b>4 partições</b></td><td><b>6 partições</b></td><td><b>12 partições</b></td></tr> 
</table>

Unidades de pesquisa, preço e capacidade são explicados em detalhes no site da Web do Azure. Consulte [Detalhes de Preço](http://azure.microsoft.com/pricing/details/search/) para obter mais informações.

> [AZURE.NOTE]O número de partições que você escolher deve poder ser dividido de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso ocorre porque a pesquisa do Azure previamente divide cada índice em 12 fragmentos para que possam ser distribuídos entre as partições. Por exemplo, se o serviço tiver três partições e você criar um novo índice, cada partição conterá 4 fragmentos do índice. Como a Pesquisa do Azure fragmenta um índice é um detalhe de implementação, sujeito a alterações em futuras versões. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.

##Escolher uma combinação de partições e réplicas para alta disponibilidade

Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam. Para muitas implantações, uma partição fornece armazenamento e E/S suficientes (a 15 milhões de documentos por partição).

Cargas de trabalho de consulta, no entanto, dependem das réplicas. Você pode precisar de réplicas adicionais se precisar de mais taxa de transferência ou alta disponibilidade.

Recomendações gerais para alta disponibilidade são:

- Duas réplicas para alta disponibilidade das cargas de trabalho somente leitura (consultas)
- Três ou mais réplicas para alta disponibilidade das cargas de trabalho de leitura/gravação (consultas e indexação)

Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. Em vez disso, você pode adicionar redundância no nível de serviço. Para uma discussão mais aprofundada das soluções alternativas, consulte [esta publicação no fórum](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE]Lembre-se de que a escalabilidade e os contratos de nível de serviço são recursos do serviço padrão. O serviço gratuito é oferecido em um nível de recurso fixo, com partições e réplicas compartilhadas por vários assinantes. Se você tiver iniciado com o serviço gratuito e agora desejar atualizar, precisará criar um novo serviço de Pesquisa do Azure no nível padrão e, em seguida, recarregar índices e dados para o novo serviço. Consulte [Criar um serviço de Pesquisa do Azure no portal](search-create-portal.md) para obter instruções sobre o provisionamento do serviço.

##Limites de chave de API

As chaves de API são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação. Chaves de consulta são especificadas na URL. Consulte [Gerenciar o serviço de pesquisa no Microsoft Azure](search-manage.md) para obter detalhes sobre o gerenciamento de chaves.

- Máximo de duas chaves de administração por serviço
- Máximo de 50 chaves de consulta por serviço

##Limites de solicitação

- Máximo de 16 MB por solicitação
- Comprimento máximo da URL de 8 KB 
- Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
- Máximo de 32 campos na cláusula $orderby
- O tamanho do termo de pesquisa é de 32766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

##Limites de resposta

- Máximo de 1000 documentos retornados por página de resultados da pesquisa
- Máximo de 100 sugestões retornadas por solicitação de Sugerir API

<!---HONumber=August15_HO8-->