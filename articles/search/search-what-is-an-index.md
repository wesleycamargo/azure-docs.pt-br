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

A Pesquisa do Azure é um serviço de pesquisa de nuvem hospedado que fornece um mecanismo de pesquisa, recursos de pesquisa que habilitam uma experiência semelhante ao Bing ou ao Google em seu aplicativo personalizado, .NET e APIs REST para a integração da Pesquisa do Azure ao seu aplicativo Web ou da área de trabalho, e armazenamento de dados pesquisáveis na forma de *índices*.

##O que é um índice?

Um *índice* é o armazenamento persistente de documentos e de outras construções usados por um serviço da Pesquisa do Azure.

Mais especificamente, ele contém todos os dados pesquisáveis usados no processamento de um índice, na execução de uma consulta ou no retorno de uma lista de resultados da pesquisa, uma estrutura de navegação facetada ou uma página de detalhes do seu aplicativo. Na Pesquisa do Azure, um índice também conterá dados não pesquisáveis para uso em expressões de filtro ou em perfis de pontuação (usados para refinar uma consulta ou aumentar uma pontuação de classificação de pesquisa), bem como estruturas no escopo do seu aplicativo personalizado que se integra à Pesquisa do Azure.

Se você estiver familiarizado com conceitos de banco de dados, um índice é conceitualmente semelhante a uma tabela, e documentos são equivalentes a linhas em uma tabela.

As seções principais de um índice, descritas em mais detalhes neste artigo, são as seguintes:-campos, que definem o corpo de um *documento*, atribuído para comportamentos específicos - perfis de pontuação - sugestões - opções de CORS - supervisor padrão

Como parte do provisionamento de um serviço de pesquisa a ser usado por seu aplicativo, será necessário criar um índice. A criação de um índice é uma tarefa centrada na definição de esquema. No mínimo, consiste em definir os campos e em configurar atributos. Opcionalmente, você pode definir perfis de pontuação e sugestões para aprimorar a experiência de pesquisa.

Para criar um esquema, você pode usar o portal ou escrever código que chame o SDK ou a API REST do .NET.

Depois de definir o esquema e de criar o índice, seu preenchimento será uma operação separada. Para saber mais sobre a ingestão de dados após a criação do índice, consulte [Importar dados para a Pesquisa do Azure](search-what-is-data-import.md).

##Como os índices são usados na Pesquisa do Azure?

Todas as operações relacionadas a dados executadas pela Pesquisa do Azure consomem ou retornam dados de um índice. Não há exceções: você não pode apontar o serviço para outras fontes de dados além do índice para retornar dados externos em uma resposta.

Para aplicativos que acumulam e operam em dados, como transações de vendas de um aplicativo de varejo online que armazena dados em um banco de dados OLTP, um índice da Pesquisa do Azure será uma fonte de dados adicional em sua solução geral. O índice é um armazenamento de dados dedicado usado apenas pelo seu serviço de pesquisa. Ter uma fonte de dados dedicada com proximidade ao seu serviço garante a consistência nos resultados da pesquisa, reduz a volatilidade, reduz o número de viagens de ida e volta entre seu aplicativo e a Pesquisa do Azure e melhora o desempenho geral de operações de pesquisa porque não há concorrência por recursos ou por dados.

##Diretrizes para a definição de um esquema

Os esquemas são criados como estruturas JSON. Você deve criar um índice para cada solução personalizada integrada à Pesquisa do Azure. Não é possível vincular ou associar vários índices, mas você pode criar índices complexos que acomodem uma variedade de cenários de pesquisa exigidos por sua solução. Por exemplo, se seus aplicativos dão suporte a vários idiomas, a coleção de campos no índice pode ter variantes específicas de idioma para cada campo.

Ao projetar o índice, reserve algum tempo na fase de planejamento para pensar em cada decisão. A alteração de um índice após sua implantação envolve a recriação e o recarregamento dos dados. Se você criar o índice no código, esta etapa será muito mais fácil do que criá-lo manualmente no portal.

Ter uma compreensão sólida dos dados de origem originais é essencial para a criação de um bom esquema. É uma boa ideia fazer a correspondência dos tipos de dados, saber qual campo usar como uma *chave* que identifique exclusivamente cada documento no índice e quais campos serão expostos em uma lista de resultados da pesquisa ou na página de detalhes.

O conteúdo de vídeo, de áudio ou de imagem deve ser armazenado em outro lugar, desde que o índice inclua um campo representativo com uma URL para o recurso. Qualquer conteúdo que não seja pesquisável, especialmente o conteúdo binário, deve ser armazenado em outro lugar e então referenciado pelo URI em um campo no índice.

Como você pode imaginar, o índice deverá ser sincronizado regularmente com outras fontes de dados usadas em sua solução. Em um catálogo online de varejo, o banco de dados de inventário que captura as transações de vendas deve ter os mesmos SKUs, preços e disponibilidade dos dados exibidos por meio de resultados da pesquisa. Dependendo da latência aceitável para sua solução, talvez seja melhor executar a sincronização de dados uma vez por semana, uma vez por dia ou quase em tempo real usando gravações simultâneas em um banco de dados de inventário e em um índice da Pesquisa do Azure. [Importar dados para a Pesquisa do Azure](search-what-is-data-import.md) explica detalhadamente as opções disponíveis.

##Elementos de esquema

Os índices consistem em campos atribuídos, valores de dados e outras construções que dão forma à construção das consultas e dos resultados da pesquisa.

- Os campos definem o corpo de um documento. Um varejo online precisará de documentos para cada SKU, uma organização de notícias precisará de documentos para cada artigo e um portal de mídia de informação precisará de documentos para cada fornecedor ou vitrine eletrônica.
- Os atributos (ou as propriedades) são anotações no campo que especificam o tipo de dados, o comprimento, o valor e os comportamentos permitidos para esse campo. Você pode especificar se o campo é pesquisável, recuperável ou classificável, tudo de forma campo por campo. Você também pode especificar qual analisador de idioma será usado no nível do campo. Uma *Chave* é um campo reservado no esquema que fornece um identificador exclusivo para todos os documentos na coleção de campos.
- Os perfis de pontuação são critérios usados para melhorar a classificação de uma ocorrência de pesquisa com mais características do que as definidas pelo perfil. Por exemplo, suponha que um termo de pesquisa corresponda a um nome de produto e a uma descrição do produto, talvez que você queira que as correspondências no nome do produto tenham uma classificação mais alta do que as encontradas em uma descrição.
- As sugestões, também conhecidas como consultas de preenchimento automático, são definidas como uma seção no índice.
- Os analisadores de idioma padrão podem ser especificados no nível do índice, globalmente aplicáveis a todos os campos.
- As opções de CORS permitem scripts entre domínios e também fazem parte de uma definição de índice.

## Atributos de índice

|**Propriedade**|Descrição|
|------------|-----------|
|**Recuperável**|Especifica se um campo pode ser retornado em um resultado de pesquisa.|
|**Filtrável**|Permite que o campo seja usado nas consultas **$filter**.|
|**Classificável**|Permite que o campo seja usado como uma opção de classificação.|
|**Com faceta**|Permite que um campo seja usado em uma estrutura de navegação facetada para filtragem autodirigida. Normalmente os campos que contêm valores repetidos que você pode usar para agrupar vários documentos (por exemplo, vários documentos que estão em um único produto ou serviço) funcionam melhor como facetas.|
|**Chave**|Fornece a ID exclusiva de cada documento, usada para pesquisar documentos. Todos os índices devem ter uma chave. Somente um campo pode ser a chave e deve ser definido para o Edm.String.|
|**Pesquisável**|Marca o campo como texto completo pesquisável.|

## Armazenamento na nuvem

Como a Pesquisa do Azure é um serviço de pesquisa totalmente gerenciado, o armazenamento de dados é tratado como uma operação interna. Como programador, é importante entender que não é possível ignorar as APIs da Pesquisa do Azure para conectar-se diretamente ao índice, nem você pode monitorar o tamanho ou a integridade do índice, exceto por meio de notificações do portal ou de chamadas de API. Não há um recurso para fazer backup ou restaurar um índice, nem para otimizar ou ajustar o desempenho na camada de armazenamento. Em segundo plano, os dados são armazenados como armazenamento de blob, mas a melhor maneira de pensar sobre o armazenamento físico e o gerenciamento de um índice é como um detalhe de implementação que poderá ser alterado no futuro. Um dos principais benefícios do serviço é a abordagem prática para a administração de dados.

Se os requisitos de armazenamento de dados ou de volume de consulta mudarem com o tempo, você poderá aumentar ou diminuir a capacidade adicionando ou movendo partições e réplicas. Para obter detalhes, consulte [Gerenciar o serviço Pesquisa no Azure](search-manage.md) ou [Limites de serviço](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO3-->