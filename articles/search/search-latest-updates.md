<properties
	pageTitle="Novidades na atualização mais recente para a Pesquisa do Azure| Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Notas de versão da Pesquisa do Azure descrevendo as atualizações mais recentes para o serviço"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/10/2016"
	ms.author="heidist"/>

#Novidades na atualização mais recente para a Pesquisa do Azure#

A Pesquisa do Azure é um serviço de pesquisa hospedado na nuvem do Microsoft Azure. Ela está disponível para o público em geral, oferecendo um SLA (contrato de nível de serviço) com disponibilidade de 99,9% para configurações da [versão de 28-02-2015 da API REST do Serviço de Pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou o [SDK .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx).

##Novidades em 2016

Recurso|Lançamento|Status|Detalhes
-------|--------|------|-------
[SDK do .NET 1.1](https://msdn.microsoft.com/library/azure/dn951165.aspx)|Fevereiro de 2016|GA|Esta é a primeira versão disponível da biblioteca de cliente .NET, `Microsoft.Azure.Search.dll`. Essa versão introduz mudanças significativas. Confira [Upgrading to the Azure Search .NET SDK version 1.1 (Atualizando para o SDK do .NET da Pesquisa do Azure versão 1.1)](search-dotnet-sdk-migration.md) para obter diretrizes sobre migração.
[Suporte à sintaxe de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Fevereiro de 2016|[GA](search-api-2015-02-28-preview.md)|A sintaxe da consulta Lucene agora está disponível na API REST e no SDK .NET. Defina o parâmetro `queryType` como `full` na API REST e a propriedade `SearchParameters.QueryType` como `QueryType.Full` no SDK .NET para habilitar a sintaxe Lucene.
[Analisadores Personalizados](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|Janeiro de 2016|[Visualização](search-api-2015-02-28-preview.md)|Configurações definidas pelo usuário dos criadores de token e filtros de token. Confira [Análise na Pesquisa do Azure](https://msdn.microsoft.com/library/azure/mt605304.aspx) no MSDN.
[Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)|Janeiro de 2016|[Visualização](search-api-2015-02-28-preview.md)|PDF, documentos do Office, XML, HTML ou até mesmo arquivos de vídeo e áudio podem ser mesclados ou incluídos em um índice de Pesquisa do Azure.
[Gerenciador de Pesquisa](search-explorer.md)|Janeiro de 2016|[Portal](https://portal.azure.com)|Ferramenta de consulta interna para consultas ad hoc em um índice.
[Pacote de Conteúdo do Power BI para Pesquisa do Azure](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|Janeiro de 2016|Ferramenta|Visualização e análise dos dados de serviço usando um novo pacote de conteúdo do Power BI para Pesquisa do Azure. Disponível por meio da Análise de Pesquisa.
[Análise de Pesquisa](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|Janeiro de 2016|Portal|Habilite a coleta de dados para informações sobre a atividade de pequisa do usuário.

##Novidades em 2015

Recurso|Lançamento|Status|Detalhes
-------|--------|------|-------
Analisadores de linguagem Lucene|Outubro de 2015|GA|Este recurso está disponível para o público em geral na API REST do serviço e no SDK do .NET.
[Processadores de linguagem natural da Microsoft](search-api-2015-02-28-preview.md)|Outubro de 2015|GA|Este recurso está disponível para o público em geral na API REST do serviço e no SDK do .NET.
[Suporte à sintaxe de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Setembro de 2015|[Visualização](search-api-2015-02-28-preview.md)|Adiciona o analisador de consulta do Lucene. Para usar a nova sintaxe, você deve especificar o `queryType` em uma operação Pesquisar Documentos.
[Processadores de linguagem natural](search-language-support.md)|Setembro de 2015|[Visualização](search-api-2015-02-28-preview.md)|Adição de processadores de linguagem da Microsoft, aumentando o número de linguagens em geral e fornecendo uma implementação alternativa.
POST em pesquisa, sugestões e consultas de pesquisa.|Setembro de 2015|[Visualização](search-api-2015-02-28-preview.md)|Aplica-se à API REST do serviço.
[API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx)|Setembro de 2015|GA|Segunda versão da API REST de Gerenciamento. Inclui checkNameAvailability, que verifica se um determinado nome de serviço já está em uso, se o intervalo de réplica anteriormente era de 1 a 6 e agora é de 1 a 12, se a propriedade de SKU foi movida do recipiente de propriedades para o nível superior da carga de serviço, se o corpo de resposta da operação Criar Serviço de Pesquisa foi atualizado para acomodar a realocação da configuração de SKU.
.NET SDK 0.10.0-preview|Agosto de 2015|Visualização|Esta é a segunda iteração da biblioteca de cliente .NET, Microsoft.Azure.Search.dll. Essa versão adiciona suporte para criação, gerenciamento e uso da [Classe DataSource](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx) e da [Classe Indexers](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) por meio das classes do .NET. Além disso, para indexadores do SQL Azure, há novo suporte para indexação de pontos de geografia.
Constructos fieldMapping|Abril de 2015|Visualização|Agora, os indexadores dão suporte aos constructos de fieldMapping que fornecem as atribuições de campo quando os nomes de campo reais são diferentes no banco de dados externo e no índice da Pesquisa do Azure. Confira [Indexadores](search-api-indexers-2015-02-28-Preview.md) para saber a versão do `2015-02-28-preview` da documentação dos indexadores.
transformações de tipo de campo|Abril de 2015|Visualização|Os indexadores agora oferecem suporte a transformações de tipo de campo para que você possa mapear um campo de cadeia de caracteres em uma tabela SQL para um campo de conjunto de cadeia de caracteres em um índice de pesquisa, supondo que o campo de origem representa uma matriz JSON.
[API REST do Serviço](https://msdn.microsoft.com/library/azure/dn798935.aspx)|Março de 2015|GA|Primeira versão de disponibilidade geral da API REST do serviço de Pesquisa do Azure. Essa versão inclui recursos anteriores. Ela também inclui [Indexadores](http://go.microsoft.com/fwlink/p/?LinkID=528210). [Sugestões](https://msdn.microsoft.com/library/azure/dn798936.aspx) substitui o suporte a consultas mais limitado e com preenchimento automático da implementação anterior (havia correspondência apenas de prefixos), adicionando suporte a correspondência infixas. Essa implementação pode localizar correspondências em qualquer lugar de um termo e também dá suporte à correspondência difusa. [Aumento da marca](http://go.microsoft.com/fwlink/p/?LinkId=528212) permite um novo cenário para os perfis de pontuação. Particularmente, ele aproveita os dados persistentes (como preferências de compras) para poder aumentar os resultados da pesquisa de usuários individuais, com base nas informações personalizadas.
.NET SDK 0.9.6-preview|Março de 2015|Visualização|Esta é a primeira versão pública do SDK do .NET para a Pesquisa do Azure. Ela inclui uma biblioteca de cliente, Microsoft.Azure.Search.dll, com dois namespaces: [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) e [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)
API REST de gerenciamento|Março de 2015|GA|Primeira versão da API REST de Gerenciamento que pertence à versão de disponibilidade geral da Pesquisa do Azure. Não há qualquer diferença de recursos entre a visualização anterior e esta.
[Processadores de linguagem natural da Microsoft](search-api-2015-02-28-preview.md)|Março de 2015|Visualização|Adiciona mais linguagens e lematização extensa para todas as linguagens com suporte do Office e Bing.
[moreLikeThis=](search-api-2015-02-28-preview.md)|Março de 2015|Visualização|Um parâmetro de pesquisa, mutuamente exclusivo de `search=`, que dispara um caminho alternativo de execução de consulta. Em vez da pesquisa de texto completo do `search=` com base em uma entrada de termo de pesquisa, o `moreLikeThis=` localiza os documentos semelhantes a um determinado documento comparando seus campos de pesquisa.

##Novidades em 2014

Recurso|Lançamento|Status|Detalhes
-------|--------|------|-------
Analisadores de linguagem Lucene|Novembro de 2014|Visualização|Adição de suporte multilíngue para os analisadores de linguagem personalizados distribuídos com o Lucene.
O suporte ao portal foi introduzido para a criação de índices|Novembro de 2014|[Portal](https://portal.azure.com)|Índices, analisadores e perfis de pontuação podem ser construídos no portal.
Gerenciamento da versão da api 2014-07-31-Preview|Outubro de 2014|Visualização|Primeira versão preview pública da API REST de gerenciamento. A documentação da versão dessa api está disponível mediante solicitação.
API REST do Serviço de Pesquisa|Agosto de 2014|Visualização|Primeira versão preview pública da API REST do serviço (api-version-2014-07-31-Preview). Essa é a API REST para operações de indexar e documentar, perfis de pontuação para ajuste dos resultados da pesquisa, suporte geoespacial. Essa versão oferece suporte ao provisionamento de serviço no Portal do Azure. A documentação da versão dessa api está disponível mediante solicitação. O controle de versão realizado nela é realizado independentemente da API REST do serviço.

##Como é feito o controle de versão e lançamento dos recursos

Os recursos são lançados separadamente ou em conjunto por meio da [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), do [SDK do .NET](http://go.microsoft.com/fwlink/?LinkId=528216) ou no painel de serviço do [Portal do Azure](https://portal.azure.com). A biblioteca .NET e as APIs REST têm várias versões. APIs mais antigas permanecerão operacionais à medida que lançamos novos recursos. Você pode visitar o [Controle de versão de serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn864560.aspx) para saber mais sobre nossa política de controle de versão.

Os recursos da versão GA (disponibilidade geral) e preview estão ligados a uma versão da API da mesma categoria.

- Os recursos da versão preview são experimentais e podem ser alterados ou até mesmo descartados antes de passarem para a versão GA. Os recursos da versão preview estão sempre disponíveis na [versão de visualização da API REST](search-api-2015-02-28-preview.md) e, às vezes, no [SDK do .NET](http://go.microsoft.com/fwlink/?LinkId=528216). A documentação do recurso sempre explicará como acessar o recurso em questão.
- Os recursos da versão GA são estáveis e é improvável que mudem. Qualquer alteração em um recurso da GA é anunciada como uma mudança significativa.

Os recursos exclusivamente do portal ou baseados em ferramenta podem mudar ao longo do tempo e não são classificados nem como da versão de visualização, nem da GA.

<!---HONumber=AcomDC_0413_2016-->