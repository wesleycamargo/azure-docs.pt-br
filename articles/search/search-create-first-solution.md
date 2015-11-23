<properties 
	pageTitle="Crie sua primeira solução de pesquisa usando a Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
	description="Crie sua primeira solução de pesquisa usando a Pesquisa do Azure, um serviço de pesquisa de nuvem hospedado do Microsoft Azure." 
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
	ms.date="11/04/2015" 
	ms.author="heidist"/>

# Crie sua primeira solução de pesquisa usando a Pesquisa do Azure

Esta amostra demonstra um aplicativo de pesquisa para a fabricante de bicicletas Adventure Works. Após concluir este tutorial, você terá um catálogo de produtos online com uma experiência de pesquisa rica que inclui navegação facetada, várias opções de classificação para os resultados de sua pesquisa e sugestões de consulta de digitação antecipada.

   ![][7]

A demonstração introduz a Pesquisa do Azure ao guiá-lo por estes exercícios:

+	Criar um índice de Pesquisa do Azure.
+	Carregar documentos (dados) no índice de Pesquisa do Azure de um banco de dados do SQL Server.
+	Compilar um aplicativo ASP.NET MVC4 que usa a Pesquisa do Azure para:
	+	Pesquisar e exibir resultados de um índice de Pesquisa do Azure
	+	Mostrar sugestões de digitação antecipada em uma caixa de Pesquisa ao digitar um termo de pesquisa
	+	Filtrar os resultados da pesquisa usando as facetas


<a id="sub-1"></a>
## Pré-requisitos

+	Uma [assinatura do Azure](../includes/free-trial-note.md). Se você não estiver pronto para se inscrever para uma assinatura de avaliação, pode ignorar este tutorial e optar por [Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/). Essa opção alternativa fornece uma Pesquisa do Azure com um aplicativo Web ASP.NET gratuitamente, uma hora por sessão, sem precisar de uma assinatura.
+	Visual Studio 2012 ou posterior com o ASP.NET MVC 4 e SQL Server instalados. Você pode baixar as edições Express gratuitas se você ainda não tiver o software instalado: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) e [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Um serviço de Pesquisa do Azure. Você precisará do nome do serviço de Pesquisa, mais a chave de administrador. Consulte [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) para obter detalhes.
+	[Projeto de Demonstração da Pesquisa do Azure da Adventure Works no CodePlex](http://go.microsoft.com/fwlink/p/?LinkID=510972). Na guia Fonte, clique em **Download** para obter um arquivo zip da solução. 

    ![][12]


Essa solução contém dois projetos:

+	O **CatalogIndex** cria um índice de Pesquisa do Azure e carrega dados de um banco de dados do SQL Server incluído no projeto.
+	O **AdventureWorksWeb** é um aplicativo baseado em MVC4 que consulta o índice de Pesquisa do Azure. Este tutorial supõe que você tenha conhecimento do trabalho com o ASP.NET MVC.

<a id="sub-2"></a>
## Criar um índice de pesquisa do Azure

Nesta etapa, você usará o **CatalogIndex** para criar um novo índice de Pesquisa do Azure chamado "catálogo" com base nos dados de um banco de dados do SQL Server AdventureWorks.

1.	No Visual Studio, abra a solução de demonstração da Pesquisa do Azure nomeada **AdventureWorksCatalog.sln**.  
2.	Clique com o botão direito do mouse em **CatalogIndexer** no Gerenciador de Soluções e selecione **Definir como projeto de inicialização** para que esse aplicativo seja executado, em vez de no projeto **AdventureWorksWeb**, ao pressionar **F5**.
3.	Abra o **App.config** nesse projeto e atualize os valores de "SearchServiceName" e "SearchServiceApiKey" para aqueles de seu serviço de Pesquisa do Azure. Para o nome de serviço da Pesquisa, se seu serviço é "mysearch.search.windows.net", você iria inserir "mysearch".
4.	Como opção, o App.config inclui uma entrada para "SourceSqlConnectionString" que assume SQL Server 2014 Express LocalDB (Server=(LocalDB)\\v11.0). Se você estiver usando uma versão diferente do SQL Server, atualize o nome do servidor de acordo. Por exemplo, se você tem uma instância padrão local, pode usar (local) ou localhost.
5.	Salve o **App.config**.
6.	Pressione **F5** para iniciar o projeto.

Um prompt de comando deve abrir com o seguinte texto: “Criando índice...”

Após alguns instantes, ele deve concluir com o texto: “Concluído. Pressione <enter> para continuar:”

   ![][8]

Pressione **Enter** para fechar o aplicativo. Neste momento, você criou o índice de Pesquisa do Azure com êxito.

> [AZURE.NOTE]Se você receber erros que incluem “valor inválido para a chave ‘attachdbfilename’” ou algum outro erro de anexação de banco de dados, você pode estar se deparando com conflitos de UAC. Para os fins desta demonstração, contorne esses erros fazendo o seguinte: copie a solução para uma pasta nova ou existente (como Temp) que forneça acesso a usuários autenticados. Use **Executar como administrador** para iniciar o Visual Studio. Abra a solução, compile-a e, em seguida, pressione **F5** para criar o índice.

Para verificar a criação do índice e o carregamento do documento, acesse o painel do serviço de Pesquisa no [Portal de Gerenciamento do Azure](https://portal.azure.com). Em uso, a contagem do índice deve ter aumentado em uma unidade e você deve ter 294 documentos, um para cada produto no banco de dados.

Clique no bloco **Índices** para mostrar a lista de índices. A lista de índices desliza e mostra o novo índice, bem como a contagem de documentos. Observe que você pode ter até três índices na camada de preços Gratuita. Se você já tiver três índices, precisa excluir um para liberar espaço para os novos.

   ![][9]


<a id="sub-3"></a>
## Explorar o CatalogIndexer

Vejamos mais de perto o projeto **CatalogIndexer** para entender como ele funciona.

1.	Abra o **Program.cs** no Gerenciador de Soluções e vá até a função `Main(string[] args)`.

    Observe como essa função gera um Uri chamado `_serviceURI` baseado no seu serviço de Pesquisa do Azure específico e cria um HttpClient chamado `_httpClient`, que usa sua chave de API para autenticar esse serviço de Pesquisa.

2.	`ChangeEnumeratorSql` e `ChangeSet` são usados para enumerar os dados da tabela Produtos no banco de dados do SQL Server AdventureWorks.

3.	Com base nos dados coletados dessa tabela, `ApplyChanges` é chamado para aplicar esses dados ao índice de Pesquisa do Azure.

4.	Mova para `ApplyChanges` no mesmo arquivo. Observe como essa função exclui o índice se ele já existe (`DeleteCatalogIndex`) e cria um novo chamado "catálogo" (`CreateCatalogIndex`).

5.	Mova para a função `CreateCatalogIndex` e observe como o índice é criado com um esquema que corresponde às colunas da tabela Produtos no SQL Server. Cada campo tem um Tipo (isto é, `Edm.String` ou `Edm.Double`), bem como atributos que definem para que esses campos são usados. Consulte a [documentação da API REST da Pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) para encontrar mais detalhes sobre esses atributos.

6.	Volte para a função `ApplyChanges`. Observe como essa função executa um loop por todos os dados nas alterações enumeradas `ChangeSet`. Em vez de aplicar as alterações uma a uma, elas são reunidas em grupos de 1.000 e aplicadas ao serviço de Pesquisa. Isso é muito mais eficiente do que aplicar os documentos um a um.

Agora que você viu como criar e preencher um índice usando dados relacionais do SQL Server, vejamos como criar um catálogo de produtos que usa nossos dados de pesquisa.

<a id="sub-4"></a>
## Compilar um aplicativo MVC4 usando a Pesquisa do Azure

Nesta etapa, você compilará e executará o aplicativo de pesquisa em um navegador da Web.

1.	No Visual Studio, abra a solução de demonstração da Pesquisa do Azure chamada **AdventureWorksCatalog.sln**.  

2.	Clique com o botão direito do mouse em **AdventureWorksWeb** no Gerenciador de Soluções e selecione **Definir como projeto de inicialização**.

3.	Abra o **Web.config** nesse projeto e atualize os valores de "SearchServiceName" e "SearchServiceApiKey" para aqueles de seu serviço de Pesquisa do Azure. Para o nome de serviço da Pesquisa, se seu serviço é "mysearch.search.windows.net", você iria inserir "mysearch".

4.	Salve o **Web.config**.

5.	Pressione **F5** para iniciar o projeto. Siga essas etapas de [Solução de problemas](#err-mvc) se você receber um erro de compilação.

    ![][10]

6.	Deixe a caixa de pesquisa vazia e clique em **Pesquisar** para retornar todos os 294 produtos.

7.	Observe a lista de facetas no lado esquerdo. Experimente clicar em uma das facetas. A pesquisa é executada novamente, mas dessa vez ela adiciona a faceta selecionada para filtrar os resultados. Talvez você queira adicionar um ponto de interrupção à função `Search` de **HomeController.cs** para percorrer (F11) cada linha.

7.	Insira um termo de pesquisa, como "mountain bikes". Conforme você digita, observe a lista suspensa de consultas sugeridas.

    ![][11]

A captura de tela do início desta demonstração aparece novamente abaixo, como um lembrete do que foi abordado. Nas seções anteriores, falamos sobre a navegação facetada (à esquerda), a contagem de documentos na parte superior da página, as sugestões e as opções de classificação para classificar por relevância, lista ou preço.

   ![][7]


<a id="sub-5"></a>
## Explorar o AdventureWorksWeb

O projeto AdventureWorksWeb nos mostra como o ASP.NET MVC 4 pode ser usado para interagir com a Pesquisa do Azure de um aplicativo da Web. Nesta seção, examinaremos as partes individuais do código do aplicativo para ver o que elas fazem.

1.	No Gerenciador de Soluções, expanda **AdventureWorksWeb** | **Controlador** e abra **HomeController.cs**

    Este é o controlador MVC que gerencia a interação da exibição Índice. Na parte superior do controlador, você observará uma referência a `CatalogSearch _catalogSearch`, que cria um objeto de conexão do HttpClient para seu serviço de Pesquisa do Azure. O código para esse `CatalogSearch` está localizado em **CatalogSeach.cs**

2. Em **HomeController.cs**, há duas funções principais:

	**Pesquisa**: quando o usuário clica no botão de pesquisa ou escolhe uma faceta, essa função é chamada para recuperar os resultados e enviá-los de volta à exibição do Índice para serem exibidos.

	**Sugerir**: conforme o usuário digita na caixa de Pesquisa, essa função é chamada para retornar uma lista de sugestões baseadas no conteúdo de seu índice de Pesquisa do Azure.

Vamos analisar essas duas funções mais detalhadamente.

3.	Na função `Search` de **HomeController.cs**, você observará uma chamada para `_catalogSearch.Search`, que inclui o objeto de conexão para seu serviço de Pesquisa do Azure. Ao chamar a função Search localizada em **CatalogSearch.cs**, você pode ver que ela utiliza esses parâmetros para criar um consulta da Pesquisa do Azure. Os resultados da consulta são armazenados em um objeto JSON chamado `result` e enviados para a exibição de `Index`, na qual os resultados são analisados e exibidos na página da Web.

4.	Abra o **Index.cshtml** em Exibições | Página Inicial, você observará o código que é usado para analisar esses resultados.

5.	Pare o aplicativo se ele ainda estiver em execução e abra o arquivo **Index.cshtml** em Exibições | Página Inicial. Ao final desse arquivo, você verá uma função JavaScript que usa `JQuery $(function ())`. Essa função é chamada quando a página carrega. Ela usa a função de preenchimento automático JQuery e vincula essa função como um retorno de chamada da caixa de texto de pesquisa, identificada como “q”. Sempre que alguém digita na caixa de texto, essa função de preenchimento automático é chamada, a qual, por sua vez, chama /home/suggest com o que é inserido. `/home/suggest` é uma referência à função em **HomeController.cs** chamada `Suggest`.

6.	Abra o **HomeController.cs** e mova para a função Suggest. Este código é bastante semelhante à função de Pesquisa que usa o objeto `_catalogSearch` para chamar uma função no **CatalogSearch.cs** chamada `Suggest`. Em vez de fazer uma consulta de pesquisa, a função `Suggest` faz uma chamada para a [API de Sugestões](http://msdn.microsoft.com/library/azure/dn798936.aspx). Ela usa os termos inseridos na caixa de texto e cria uma lista de possíveis sugestões. Os valores são retornados para o arquivo **Index.cshtml** e automaticamente listados na caixa de Pesquisa como opções de digitação antecipada.

Você pode estar se perguntando neste momento como a Pesquisa do Azure sabe sobre quais campos criar sugestões. A resposta para isso está em quando você criou o Índice. Na função `CreateCatalogIndex` dentro do arquivo Program.cs do projeto **CatalogIndexer**, há um atributo chamado `Suggestions`. Sempre que esse atributo é definido como `True`, significa que a Pesquisa do Azure pode usá-lo como um campo para recuperar sugestões.

Vamos experimentar.

7.	Novamente, compile o aplicativo e pressione **F5** para executar o aplicativo.

8.	Na caixa de pesquisa, digite a palavra "Estrada". Após um segundo, você deve ver uma lista de itens ser exibida abaixo da caixa de texto com sugestões que o usuário possivelmente escolheria.

Talvez você queira adicionar um ponto de interrupção à função `Suggest` em **HomeController.cs** e percorrer (F11) de cada chamada realizada para criar a lista de sugestões.

Isso conclui a demonstração. Agora você percorreu as operações principais que precisará conhecer antes de compilar um aplicativo ASP.NET MVC4 usando a Pesquisa do Azure.


<a id="err-mvc"></a>
## Como resolver “Não foi possível carregar o arquivo ou assembly 'System.Web.Mvc’”

Ao desenvolver o AdventureWorksWeb, se você receber “Não foi possível carregar o arquivo ou assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' ou uma de suas dependências", tente estas etapas para resolver o erro.

1. Abra o Console do Gerenciador de Pacotes: **Ferramentas** | **Gerenciador de Pacotes NuGet** | **Console do Gerenciador de Pacotes**
2. No prompt PM>, insira “Update-package -reinstall Microsoft.AspNet.Mvc”
3. Quando solicitado a recarregar o arquivo, escolha **Sim para todos**.
4. Recompile a solução e tente **F5** novamente.


<a id="next-steps"></a>
## Próximas etapas

Para estudo individual adicional, considere adicionar uma página Detalhes para se abrir quando o usuário clicar em um dos resultados da pesquisa. Para preparar, você pode fazer o seguinte:

+	Informe-se sobre a [API de Pesquisa](http://msdn.microsoft.com/library/azure/dn798929.aspx) que permite que você faça uma consulta para a Pesquisa do Azure retornar um documento específico (por exemplo, você pode passar o productID).
+	Tente adicionar uma nova função chamada Details no arquivo **HomeController.cs**. Adicione uma visualização **Details.cshtml** correspondente que receba os resultados dessa pesquisa e os exibe.
+	Confira este exemplo de código adicional e o vídeo sobre pesquisa geoespacial: [Canal 9 - Pesquisa do Azure e Dados Geoespaciais](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) e [CodePlex: Exemplo de Pesquisa Geoespacial na Pesquisa do Azure](http://azuresearchgeospatial.codeplex.com)

Você também pode examinar a [API REST da Pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) no MSDN.


<!--Anchors-->
[Prerequisites]: #sub-1
[Create an Azure Search index]: #sub-2
[Explore CatalogIndexer]: #sub-3
[Build an MVC4 Application using Azure Search]: #sub-4
[Explore AdventureWorksWeb]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG

<!---HONumber=Nov15_HO3-->