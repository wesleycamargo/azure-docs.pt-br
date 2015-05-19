<properties 
	pageTitle="Criar seu primeiro aplicativo de Pesquisa do Azure em .NET" 
	description="Tutorial sobre como criar uma solução usando a biblioteca de cliente .NET do SDK do .NET da Pesquisa do Azure." 
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
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#Criar seu primeiro aplicativo de Pesquisa do Azure em .NET#

Este tutorial cria um aplicativo personalizado de pesquisa Web no Visual Studio 2013 ou posterior que use a Pesquisa do Azure para sua experiência de pesquisa. O tutorial usa o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx) para criar classes para os objetos e operações usados no exemplo.

Você pode baixar o exemplo do codeplex em [Demonstração da Pesquisa do Azure usando dados USGS](https://azsearchdemos.codeplex.com/SourceControl/latest) para seguir as etapas neste tutorial. O aplicativo de exemplo usa dados do [Serviço Geológico dos Estados Unidos \(USGS\)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados no estado de Washington. Vamos usar esses dados para criar um aplicativo de pesquisa baseado em dados sobre prédios de referência como hospitais e escolas, bem como características geológicas como rios, lagos e picos.

Para executar esse exemplo, você deve ter um serviço de Pesquisa do Azure, no qual você pode se inscrever pelo [Portal do Azure](https://portal.azure.com).

Você pode começar com [Criar um serviço no portal](../search-create-service-portal/) se precisar de ajuda para provisionamento e verificação da disponibilidade do serviço. O artigo também explica como localizar o nome do serviço e chaves de administrador usadas em cada tutorial, além de uma solução que inclui a Pesquisa do Azure.

> [AZURE.TIP]Recomendamos atualizar pacotes do NuGet antes de criar os projetos para evitar erros de compilação. Clique com o botão direito do mouse na solução e escolha **Gerenciar Pacotes NuGet**. Atualize todos os pacotes usados nesta solução.

##Compilar o índice##

1. Copie nome do serviço e chave de admin do [Portal do Azure](https://portal.azure.com) e cole-a em **DataIndexer** \| **App.config**.
1. Com o botão direito do mouse no projeto **DataIndexer** para defini-lo como o projeto de inicialização.
1. Compile e execute o projeto.

Você deverá ver uma janela de console com essas mensagens.

![][1]

No portal, você verá um novo índice de recursos com xx e xx. Pode demorar vários minutos para que o portal seja atualizado, portanto, planeje atualizar a tela depois de alguns minutos para ver os resultados.

![][2]

##Compilar o aplicativo##


1. Copie nome do serviço e a chave de admin do [Portal do Azure](https://portal.azure.com) e cole-a em **SimpleSearchMVCApp** \| **Web.config**.
1. Clique com o botão direito do mouse no projeto **SimpleSearchMVCApp** para defini-lo como o projeto de inicialização.
1. Compile e execute o projeto.

Você deve ver uma página da Web no seu navegador padrão, fornecendo uma caixa de pesquisa para acessar dados USGS armazenados no índice em seu serviço de Pesquisa do Azure.

![][3]

##Pesquisar em dados USGS##

O conjunto de dados USGS inclui registros relevantes para o estado de Washington. Se você clicar em **Pesquisa** em uma caixa de pesquisa vazia, você obterá as 50 entradas principais, que é o padrão.

A inserção de um termo de pesquisa fornecerá ao mecanismo de pesquisa algo para continuar. Tente digitar um nome regional. "Snoqualmie" é uma cidade em Washington. Também é o nome de um rio, uma cascata temática, passagem pela montanha e parques estaduais.

![][4]

Você também pode tentar qualquer um destes termos:

- Seattle
- Rainier
- Seattle e Rainier
- Seattle + Rainier - Montagem \(obtém resultados de pontos de referência na Avenida Rainier ou clube Rainier, tudo dentro dos limites de cidade de Seattle\).

##Explorar o código##

Para aprender os conceitos básicos do SDK do .NET, dê uma olhada em [Como usar a Pesquisa do Azure no .NET](../search-howto-dotnet-sdk/) para obter uma explicação das classes mais comumente usadas na biblioteca de cliente.

O restante desta seção aborda alguns pontos sobre cada projeto. Quando apropriado, indicaremos a você algumas abordagens alternativas que usam recursos mais avançados.

**Projeto DataIndexer**

Para manter as coisas simples, os dados são integrados à solução, em um arquivo de texto gerado por meio de dados no [site Web dos USGS \(Serviços Geológicos dos Estados Unidos\)](http://geonames.usgs.gov/domestic/download_data.htm).

Alternativas à inserção de dados incluem [indexadores para Banco de Dados de Documentos](documentdb-search-indexer.md) ou [indexadores para o Banco de Dados SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). Indexadores extraem dados para o seu índice de Pesquisa do Azure, que pode simplificar de verdade o código que você precisa escrever e manter.

Você também pode carregar dados por meio de um banco de dados SQL Server local. [Este tutorial](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/) mostra como fazer isso.

O programa **DataIndexer** inclui um método **SearchDocuments** para pesquisa e filtragem de dados. Este método existe como uma etapa de verificação, dando suporte à saída de mensagens de status para a janela do console, ou seja, aquelas mostrando resultados da pesquisa e comportamentos de filtro. Provavelmente, você não precisa de um método como este no código que você escreve para criar e carregar um índice.

**Projeto SimpleSearchMVCApp**

O projeto MVC usa um modo de exibição e um controlador para rotear entradas e saídas para a camada de apresentação. O **index.cshtml** fornece o HTML usado para processar os resultados da pesquisa. Atualmente, isso é apenas uma tabela simples que organiza os dados do conjunto de dados. Embora seja útil para criação de protótipos e testes, você pode facilmente melhorar os resultados após a apresentação. Para obter dicas sobre como agrupar resultados em lote e colocar contagens em uma página, consulte [Paginar resultados e paginação na Pesquisa do Azure](search-pagination-page-layout.md).

Conexões à Pesquisa do Azure, além da execução de uma solicitação de consulta, são definidas no arquivo **FeatureSearch.cs**.

Como uma observação final, se você ainda não estiver convencido do valor e a simplicidade do SDK do .NET, compare os arquivos de origem deste exemplo com esse baseado na API REST: [Demonstração do Adventure Works da Pesquisa do Azure](https://azuresearchadventureworksdemo.codeplex.com/). A versão do SDK do .NET descrita neste tutorial é muito mais simples, com menos linhas de código.

##Próximas etapas##

Este é o primeiro tutorial da Pesquisa do Azure com base no conjunto de dados do USGS. Ao longo do tempo, ampliaremos este tutorial e criaremos novos outros para demonstrar os recursos de pesquisa que talvez você queira usar em suas soluções personalizadas.

Se você já tiver alguma experiência com a Pesquisa do Azure, use este exemplo como um trampolim para tentar sugestores \(consultas de preenchimento automático\), filtros e navegação facetada. Você também pode melhorar a página de resultados da pesquisa adicionando contagens e documentos em lote para que os usuários possam percorrer os resultados.

Ainda não conhece a Pesquisa do Azure? Recomendamos os outros tutoriais para desenvolver uma compreensão do que você pode criar. Visite nossa [página de documentação](http://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. Você também pode ver os links em nossa [Lista de vídeos e Tutoriais](https://msdn.microsoft.com/library/azure/dn798933.aspx) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-20150228/consolemessages.png
[2]: ./media/search-get-started-20150228/portalindexstatus.png
[3]: ./media/search-get-started-20150228/usgssearchbox.png
[4]: ./media/search-get-started-20150228/snoqualmie.png
<!--HONumber=54-->