<properties 
	pageTitle="Novidades na atualização mais recente para a Pesquisa do Azure| Microsoft Azure" 
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
	ms.date="07/08/2015" 
	ms.author="heidist"/>

#Novidades na atualização mais recente para a Pesquisa do Azure#

Agora a Pesquisa do Azure está disponível, oferecendo um contrato de nível de serviço (SLA) com 99,9% de disponibilidade para as configurações com suporte da [versão 2015-02-28 da API](https://msdn.microsoft.com/library/azure/dn798935.aspx).

##Como é feito o controle de versão e lançamento dos recursos

Os recursos são lançados separadamente ou em conjunto por meio da [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), [SDK do .NET](http://go.microsoft.com/fwlink/?LinkId=528216) ou no painel de serviço do [Portal do Azure](https://portal.azure.com).

A biblioteca .NET e as APIs REST têm várias versões. APIs mais antigas permanecerão operacionais à medida que lançamos novos recursos. Você pode visitar o [Controle de versão de serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn864560.aspx) para saber mais sobre nossa política de controle de versão.


##.NET SDK 0.10.0-preview

Esta é a segunda iteração da biblioteca de cliente .NET, Microsoft.Azure.Search.dll. Essa versão acrescenta o suporte para a criação, gerenciamento e uso de indexadores por meio de classes .NET. Além disso, para indexadores do SQL Azure, há novo suporte para indexação de pontos de geografia.

- [Classe de indexadores](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [Classe DataSource](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##.NET SDK 0.9.6-preview
**Lançamento: 5 de março de 2015**

Esta é a primeira versão pública do SDK do .NET para a Pesquisa do Azure. Ela inclui uma biblioteca de cliente, Microsoft.Azure.Search.dll, com dois namespaces:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Exclui:

- [Indexadores](http://go.microsoft.com/fwlink/p/?LinkId=528173) (esse recurso não é excluído na versão de visualização 0.10.0)
- [API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- Recursos da versão de [Visualização de 28-02-2015](search-api-2015-02-28-Preview.md) (atualmente, os recursos apenas para visualização são processadores de idioma natural da Microsoft e `moreLikeThis`).

Visite [como usar a Pesquisa do Azure no .NET](http://go.microsoft.com/fwlink/p/?LinkId=528088) para obter orientação sobre a instalação e o uso do SDK.

##Versão da Api de visualização de 28-02-2015
**Lançamento: 22 de abril de 2015**

- Agora, os indexadores dão suporte aos constructos de fieldMapping que fornecem as atribuições de campo quando os nomes de campo reais são diferentes no banco de dados externo e no índice da Pesquisa do Azure. Confira [Indexadores](search-api-indexers-2015-02-28-Preview.md) para saber a versão do `2015-02-28-preview` da documentação dos indexadores.

- Além disso, nessa atualização de visualização, os indexadores dão suporte a transformações de tipo de campo para que você possa mapear um campo de cadeia de caracteres em uma tabela SQL para um campo de conjunto de cadeia de caracteres em um índice de pesquisa, supondo que o campo de origem representa uma matriz JSON.

**Lançamento: 5 de março de 2015**

- [Processadores de idioma natural da Microsoft](search-api-2015-02-28-Preview.md) oferecem suporte para mais idiomas e proporcionam lematização abrangente para todos os idiomas com suporte do Office e do Bing.

- [moreLikeThis =](search-api-2015-02-28-Preview.md) é um parâmetro de pesquisa, mutuamente exclusivo do `search=`, que dispara um caminho de execução de consulta alternativo. Em vez da pesquisa de texto completo do `search=` com base em uma entrada de termo de pesquisa, o `moreLikeThis=` localiza os documentos semelhantes a um determinado documento comparando seus campos de pesquisa.

##Versão da api 28-02-2015
**Lançamento: 5 de março de 2015**

- [Indexadores](http://go.microsoft.com/fwlink/p/?LinkID=528210) é um novo recurso que simplifica muito a indexação de fontes de dados no Banco de Dados SQL do Azure, Banco de Dados de Documentos do Azure e SQL Server em VMs do Azure.

- [Sugestões](https://msdn.microsoft.com/library/azure/dn798936.aspx) substitui o suporte a consultas mais limitado e com preenchimento automático da implementação anterior (havia correspondência apenas de prefixos), adicionando suporte a correspondência infixas. Essa implementação pode localizar correspondências em qualquer lugar de um termo e também dá suporte à correspondência difusa.

- [Aumento da marca](http://go.microsoft.com/fwlink/p/?LinkId=528212) permite um novo cenário para os perfis de pontuação. Particularmente, ele aproveita os dados persistentes (como preferências de compras) para poder aumentar os resultados da pesquisa de usuários individuais, com base nas informações personalizadas.

Visite [Agora a Pesquisa do Azure está disponível](http://go.microsoft.com/fwlink/p/?LinkId=528211) para ver o comunicado de serviço no blog do Azure que aborda todos esses recursos.

##Versão da Api 2014-10-20-Preview
**Lançamento: novembro de 2014, janeiro de 2015**

- [Analisadores de linguagem Lucene](search-api-2014-10-20-preview.md) foram adicionados para fornecer suporte aos vários analisadores de linguagem personalizada distribuídos com o Lucene. 

- O suporte à ferramenta foi introduzido para a criação de índices, incluindo perfis de pontuação, no [Portal de gerenciamento do Azure](https://portal.azure.com).

##Versão da Api 2014-07-31-Preview
**Lançamento: 21 de agosto de 2014**

Esta versão foi a versão de visualização pública da Pesquisa do Azure, fornecendo os seguintes recursos principais:

- API REST para operações de índice e de documento. Grande parte dessa versão da API está intacta na 2015-02-28. A documentação da versão `2014-07-31-Preview` pode ser encontrada em [ API REST do Serviço de Pesquisa do Azure versão 2014-07-31](search-api-2014-07-31-preview.md).

- Perfis de pontuação para ajuste dos resultados da pesquisa. Um perfil de pontuação adiciona critérios usados para calcular as pontuações da pesquisa. A documentação desse recurso pode ser encontrada na [API REST dos Perfis de Pontuação do Serviço de Pesquisa do Azure Versão 2014-07-31](search-api-scoring-profiles-2014-07-31-preview.md).

- O suporte geoespacial está disponível desde o início, fornecido por meio do tipo de dados `Edm.GeographyPoint`, que faz parte da Pesquisa do Azure desde sua criação.

- Provisionamento na versão de visualização do [Portal de gerenciamento do Azure](https://portal.azure.com). A Pesquisa do Azure era um dos poucos serviços disponíveis apenas no novo portal.

##Gerenciamento da versão da api 2015-02-28
**Lançamento: 5 de março de 2015**

[API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx) marca a primeira versão da API de gerenciamento pertencente à versão disponível publicamente da Pesquisa do Azure. Não há qualquer diferença de recursos entre a visualização anterior e esta.

##Gerenciamento da versão da api 2014-07-31-Preview
**Lançamento: outubro de 2014**

A versão de visualização da [API REST de gerenciamento](search-management-api-2014-07-31-preview.md) foi adicionada a fim de dar suporte à administração do serviço de forma programática. O controle de versão realizado nela é realizado independentemente da API REST do serviço.


 

<!---HONumber=July15_HO4-->