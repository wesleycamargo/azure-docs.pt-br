<properties
	pageTitle="Índices na Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="O que é um índice na Pesquisa do Azure e como ele é usado?"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# índices na Pesquisa do Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

A Pesquisa do Azure é um serviço de pesquisa de nuvem hospedado que fornece um mecanismo de pesquisa, recursos de pesquisa que habilitam uma experiência semelhante ao Bing ou ao Google em seu aplicativo personalizado, .NET e APIs REST para a integração da Pesquisa do Azure ao seu aplicativo Web ou da área de trabalho, e armazenamento de dados relacionados à pesquisa na forma de um *índice*.

##O que é um índice?

Um *índice* é o armazenamento persistente de *documentos* e de outras construções usados por um serviço da Pesquisa do Azure. Os documentos são uma unidade básica de dados pesquisáveis. Por exemplo, um varejista pode ter um documento para cada SKU, uma organização de notícias pode ter um documento para cada artigo e uma organização streaming de mídia pode ter um documento para cada vídeo ou música em sua biblioteca. Mapeando esses conceitos para equivalentes de banco de dados mais conhecidos: um *índice* é conceitualmente semelhante a uma *tabela* e *documentos* são equivalentes a *linhas* em uma tabela.

O índice que contém documentos é um conjunto de dados simples — normalmente, um subconjunto dos dados criados ou capturados durante operações normais de negócios por meio de processos computadorizados, como transações de vendas, conteúdo de publicação, atividade de compra – frequentemente armazenados em bancos de dados relacionais ou NoSQL. Um índice obtém seus documentos quando você envia por push ou faz pull em um conjunto de dados com linhas de outras fontes de dados para seu índice.

Compreendido dentro dos limites das operações de mecanismo de pesquisa, um índice contém todos os dados pesquisáveis usados para processar um índice, executar uma consulta ou retornar qualquer um destes: uma lista de resultados da pesquisa, uma estrutura de navegação facetada, uma página de detalhes de um único documento. Na Pesquisa do Azure, um índice também pode conter dados não pesquisáveis usados internamente em expressões de filtro ou nos perfis de pontuação, que fornecem critérios usados para acelerar uma pontuação de classificação de pesquisa.

##Quando devo criar um índice?

Como parte do provisionamento de um serviço de pesquisa a ser usado por seu aplicativo, será necessário criar um índice. A criação de um índice é uma tarefa centrada principalmente na definição de esquema. No mínimo, consiste em definir os campos e em configurar os atributos. Opcionalmente, você pode ampliar um índice para incluir perfis de pontuação, sugestões e valores padrão personalizados.

Para criar um esquema que defina um índice, você poderá usar o portal ou escrever um código que chame o SDK ou a API REST do .NET.

Depois de definir o esquema e de criar o índice, seu preenchimento será uma operação separada. Para saber mais sobre a ingestão de dados após a criação do índice, consulte [Importar dados para a Pesquisa do Azure](search-what-is-data-import.md).

##Especificação de esquema JSON de um índice

As seções principais de um índice da Pesquisa do Azure, como articuladas no formato de intercâmbio de dados JSON, são as seguintes:

|Elemento de esquema|Descrição|
|--------------|-----------|
|Coleção de campos|Os campos definem um documento. Um conjunto de dados que você envia por push ou faz pull em um índice deve fornecer valores ou valores nulos para cada campo, compatíveis com o tipo de dados e o comprimento do campo expressos no esquema.|
|[Atributos](#index-attributes)|As propriedades ou anotações em um campo que especificam o tipo de dados, o comprimento, o valor e os comportamentos permitidos para esse campo. Você pode especificar se o campo é pesquisável, recuperável ou classificável, tudo de forma campo por campo. Você também pode especificar se o analisador de idioma será substituído no nível do campo.
|[Perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx)|Os critérios usados para melhorar a classificação de uma ocorrência de pesquisa com mais características do que as definidas pelo perfil. Por exemplo, suponha que um termo de pesquisa corresponda a um nome de produto e a uma descrição do produto, talvez que você queira que as correspondências no nome do produto tenham uma classificação mais alta do que as encontradas em uma descrição.|
|[Sugestores](https://msdn.microsoft.com/library/azure/mt131377.aspx)|Também conhecidos como consultas de preenchimento automático, são definidos como uma seção no índice.|
|[Analistas de idioma padrão]()|Opcionalmente, você pode especificá-los no nível do índice, globalmente aplicáveis a todos os campos.|
|Opções de CORS|Opcionalmente, permite o compartilhamento de recursos entre origens, onde há solicitações de um recurso usado por uma página da Web emitido em um limite de domínio. O CORS sempre estará desativado, a menos que você o habilite especificamente para o índice.|

<a name="index-attributes"></a>
##Atributos de índice

Os atributos são definidos em campos individuais para especificar como o campo será usado. A tabela a seguir enumera os atributos que você pode especificar.

|**Propriedade**|Descrição|
|------------|-----------|
|**Recuperável**|Especifica se um campo pode ser retornado em um resultado da pesquisa.|
|**Filtrável**|Permite que o campo seja usado em consultas **$filter**.|
|**Classificável**|Permite que o campo seja usado como uma coluna de classificação em um conjunto de resultados.|
|**Com faceta**|Permite que um campo seja usado em uma estrutura de navegação facetada para filtragem autodirigida do usuário. Normalmente, os campos que contêm valores repetidos que você pode usar para agrupar vários documentos (por exemplo, vários documentos que estejam em uma única categoria de marca ou de serviço) funcionam melhor como facetas.|
|**Chave**|Uma cadeia de caracteres que fornece a ID exclusiva de cada documento, usada para pesquisar documentos. Todos os índices devem ter uma chave. Somente um campo pode ser a chave e deve ser definido para o Edm.String.|
|**Pesquisável**|Marca o campo como texto completo pesquisável.|


##Como os índices são usados na Pesquisa do Azure?

Todas as operações relacionadas a dados executadas pela Pesquisa do Azure consomem ou retornam dados de um índice. Não há exceções: você não pode apontar o serviço para outras fontes de dados além do índice para retornar dados externos em uma resposta emitida pelo ser serviço de pesquisa.

Para aplicativos que acumulam e operam em dados, como transações de vendas de um aplicativo de varejo online, um índice da Pesquisa do Azure será uma fonte de dados adicional em sua solução geral. O índice é um armazenamento de dados dedicado usado apenas pelo seu serviço de pesquisa. Ter uma fonte de dados dedicada com proximidade ao seu serviço de pesquisa garante a consistência nos resultados da pesquisa, reduz a volatilidade, reduz o número de viagens de ida e volta entre seu aplicativo e a Pesquisa do Azure e melhora o desempenho geral de operações de pesquisa porque não há concorrência por recursos ou por dados.

##Diretrizes para a definição de um esquema

Os esquemas são criados como estruturas JSON. Você deve criar um índice para cada solução personalizada integrada à Pesquisa do Azure. Não é possível vincular ou associar vários índices, mas você pode criar índices complexos que acomodem uma variedade de cenários de pesquisa exigidos por sua solução. Por exemplo, se seus aplicativos dão suporte a vários idiomas, a coleção de campos no índice pode ter variantes específicas de idioma para cada campo.

Ao projetar o índice, reserve algum tempo na fase de planejamento para pensar em cada decisão. A alteração de um índice após sua implantação envolve a recriação e o recarregamento dos dados. Se você criar o índice no código, esta etapa será muito mais fácil do que criá-lo manualmente no portal.

Ter uma compreensão sólida dos dados de origem originais é essencial para a criação de um bom esquema. É uma boa ideia fazer a correspondência dos tipos de dados, saber qual campo usar como uma *chave* que identifique exclusivamente cada documento no índice e quais campos serão expostos em uma lista de resultados da pesquisa ou na página de detalhes.

**Introdução aos dados relacionais**

Os dados relacionais podem ser difíceis de serem transformados em um conjunto de dados simples. Se possível, tente usar o data warehouse da empresa ou o banco de dados de relatório, se houver um disponível. Geralmente, essa será a melhor opção porque o modelo já estará desordenado. Caso contrário, você terá de depender de consultas para obter o conjunto de linhas necessário. Para obter um exemplo e uma explicação de como fazer isso, consulte [Modelando o banco de dados de inventário da AdventureWorks para a Pesquisa do Azure](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx).

**Incluindo dados binários**

Talvez você queira conteúdo de imagem, de áudio ou de vídeo em seus resultados da pesquisa. Nesse caso, os arquivos deverão ser armazenados em outro lugar, com o índice incluindo um campo representativo com uma URL para o recurso. Qualquer conteúdo que não seja pesquisável, especialmente o conteúdo binário, deve ser armazenado em um repositório mais barato e então referenciado pelo URI em um campo no índice.

**Sincronizando dados**

Como você pode imaginar, o índice deverá ser sincronizado regularmente com outras fontes de dados usadas em sua solução. Em um catálogo online de varejo, o banco de dados de inventário que captura as transações de vendas deve ter os mesmos SKUs, preços e disponibilidade dos dados exibidos por meio de resultados da pesquisa. Dependendo da latência aceitável para sua solução, talvez seja melhor executar a sincronização de dados uma vez por semana, uma vez por dia ou quase em tempo real usando gravações simultâneas em um banco de dados de inventário e em um índice da Pesquisa do Azure. [Importar dados para a Pesquisa do Azure](search-what-is-data-import.md) explica detalhadamente as opções disponíveis.

## Armazenamento na nuvem

Como a Pesquisa do Azure é um serviço de pesquisa totalmente gerenciado, o armazenamento de dados é tratado como uma operação interna. Como programador, é importante entender que não é possível ignorar as APIs da Pesquisa do Azure para conectar-se diretamente ao índice, nem você pode monitorar o tamanho ou a integridade do índice, exceto por meio de notificações do portal ou de chamadas de API. Não há um recurso para fazer backup ou restaurar um índice, nem para otimizar ou ajustar o desempenho na camada de armazenamento. Em segundo plano, os dados são armazenados como armazenamento de blob, mas a melhor maneira de pensar sobre o armazenamento físico e o gerenciamento de um índice é como um detalhe de implementação que poderá ser alterado no futuro. Um dos principais benefícios do serviço é a abordagem prática para gerenciamento físico dos recursos de computação do seu aplicativo de pesquisa.

Se os requisitos de armazenamento de dados ou de volume de consulta mudarem com o tempo, você poderá aumentar ou diminuir a capacidade adicionando ou movendo partições e réplicas. Para obter detalhes, consulte [Gerenciar o serviço Pesquisa no Azure](search-manage.md) ou [Limites de serviço](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO4-->