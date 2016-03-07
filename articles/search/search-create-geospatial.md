<properties 
	pageTitle="Criar um aplicativo de pesquisa geoespacial usando a Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa em nuvem hospedado" 
	description="Crie um aplicativo de pesquisa geoespacial usando o Bing e a Pesquisa do Azure, um serviço de pesquisa de nuvem hospedado do Microsoft Azure." 
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
	ms.date="02/18/2016" 
	ms.author="heidist"/>

# Criar um aplicativo de pesquisa geoespacial usando a Pesquisa do Azure

Este tutorial demonstra como adicionar a pesquisa geoespacial aos aplicativos Web usando a Pesquisa do Azure e o Bing Mapas. Ao usar a pesquisa geográfica, você pode encontrar destinos de pesquisa dentro de uma distância determinada de um ponto (como encontrar todos os restaurantes a uma distância de 5 Km do meu local atual). A capacidade geoespacial da Pesquisa do Azure dá suporte às técnicas de mapeamento mais usadas. Por exemplo, se você deseja usar formas de polígono em um aplicativo imobiliário mostrando casas para venda dentro do limite do polígono, você pode facilmente fazer isso usando a sintaxe de pesquisa simples ou OData.

Para obter mais visão geral, assista a este vídeo no Canal 9 sobre a [Pesquisa do Azure e Dados Geoespaciais](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

![][7]

Para criar o aplicativo, utilizaremos o serviço de mapeamento do Bing para geocodificar endereços carregados de um arquivo CSV e armazenar os dados resultantes em um índice de Pesquisa.

Este tutorial é baseado na [Pesquisa do Azure – Demonstração do Adventure Works](http://azuresearchadventureworksdemo.codeplex.com). Se você ainda não percorreu aquela demonstração, comece lá para obter alguma experiência em criar um índice e chamar a API de Pesquisa do Azure de um aplicativo da Web.

<a id="sub-1"></a>
## Pré-requisitos

+	Visual Studio 2012 ou posterior com o ASP.NET MVC 4 e SQL Server instalados. Você pode baixar as edições Express gratuitas se você ainda não tiver o software instalado: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) e [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Um serviço de Pesquisa do Azure. Você precisará do nome do serviço de Pesquisa, mais a chave de administrador. Consulte [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) para obter detalhes.
+	Um serviço de mapa do Bing e uma chave para acessá-lo. As instruções são fornecidas na próxima seção
+	[Exemplo de GeoSearch da Pesquisa do Azure no CodePlex](https://azuresearchgeospatial.codeplex.com/). Na guia Fonte, clique em **Download** para obter um arquivo zip da solução. 

    ![][12]

Essa solução contém dois projetos:

+	O **StoreIndexer** cria um índice de Pesquisa do Azure e carrega dados.
+	O **AdventureWorksWebGeo** é um aplicativo baseado em MVC4 que consulta o índice de Pesquisa do Azure e mostra locais e armazenamento em um mapa do Bing.

[AZURE.INCLUDE [Você precisa de uma conta do Azure para concluir este tutorial:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Bing Mapas

Vamos usar a API do Bing Mapas para duas coisas.

+ **Geocodificar endereços:** nos dados, temos endereços (cidade, estado, código postal), mas também queremos as coordenadas de longitude e latitude de um endereço para que possamos fazer pesquisas geoespaciais. Para obter as coordenadas, usaremos a API do Bing Mapas DataFlow para enviar um grupo de endereços para geocodificação. Ao usar a conta de avaliação do Bing, estamos limitados a 50 endereços de uma vez, mas isso será suficiente para este tutorial.

+ **Bing Mapas:** quando o aplicativo é executado, usaremos o Bing Mapas para exibir locais de armazenamento, sobrepostos na parte superior de um Bing Mapa.

### Criar uma conta para o Bing Mapas

1. Vá até o [Portal do Bing Mapas](https://www.bingmapsportal.com/) e crie uma nova conta. Insira os detalhes para criar a conta.

2. Depois que a conta for criada, escolha **Criar ou exibir as chaves** e insira os detalhes para criar uma chave. Para essa demonstração, você pode escolher **Chave de Avaliação**.

3. Clique em **Enviar**. Você deve ver os detalhes da chave para seu aplicativo de mapas do Bing. Anote essa chave, visto que a usaremos mais tarde.

<a id="sub-3"></a>
## Geocodificação de endereços no C# usando a API Bing Mapas DataFlow

Nesta etapa, usamos a API do Bing Mapas DataFlow para geocodificar alguns endereços para várias lojas de bicicletas no mundo todo.

Esses dados vêm de um arquivo CSV chamado store\_locations.csv localizado na fonte que você baixou antes. Se você abrir esse arquivo em um editor de texto ou no Excel, verá que ele tem a coluna de ID para cada loja, o nome da loja e seu endereço.

Vamos explorar o código que explica como isso funciona.

1. Abra a solução AdventureWorksGeo no Visual Studio, expanda o projeto **StoreIndexer** no Gerenciador de Soluções e abra Program.cs. Como já abordamos a criação de índice em [Pesquisa do Azure – Demonstração do Adventure Works](http://azuresearchadventureworksdemo.codeplex.com/), ignoraremos a discussão sobre como isso funciona em Program.cs.

2. Vá até a função **Principal** e observe que ela chama o **ApplyStoreData**. Mova para essa função e explore o código.

3. O **ApplyStoreData** carrega dados de um arquivo CSV chamado "store\_locations.csv" para um System.Data.DataTable.

    Esse arquivo contém todas as lojas, incluindo os endereços que desejamos carregar na Pesquisa do Azure. Ao fazer a iteração de cada linha neste arquivo, podemos criar um conjunto de **indexOperations** que são inseridos em um índice da Pesquisa do Azure (criado anteriormente na função **CreateStoresIndex()**).

    Se você olhar com cuidado para o índice posteriormente, observará que o campo **GeoPt** que conterá a longitude e latitude para cada loja está vazio. Isso nos leva à próxima etapa da função **Main**.

5. Mova para a função **ExtractAddressInfoToXML()**. Essa função extrai as informações do endereço ao arquivo store\_locations.csv e carrega essas informações a um arquivo XML que é formatado de uma maneira que o Bing Mapas pode aceitar para geocodificação. Assim que o arquivo for criado, ele é enviado para processar para o Bing Mapas DataFlow ao chamar a função **GeoCoding.CreateJob**.

6. Como o processo de geocodificação pode demorar um pouco, há um loop que chama o **GeoCoding.CheckStatus** a cada 10 segundos para ver se o trabalho está concluído. Assim que estiver concluído, os resultados são baixados ao chamar o **GeoCoding.DownloadResults** em uma classe de endereços.

7. A etapa final é pegar estes endereços geocodificados e enviá-los para a Pesquisa do Azure. Vamos examinar mais de perto como isso é feito abrindo a função **UpdateStoreData**.

  **UpdateStoreData** usa a ação **@search.action: merge** para atualizar o campo localização do tipo Edm.GeographyPoint com as coordenadas de longitude e latitude geocodificadas que foram baixadas do Bing Mapas. Isso é feito procurando a storeId que é a chave única para o documento no índice "stores" e mesclando estes novos dados no documento existente.

8. Antes de executar o aplicativo, adicione as informações da API do Bing Mapas e a Pesquisa do Azure ao abrir o App.config e atualizar os valores para "SearchServiceName", "SearchServiceApiKey" e "BingMapsAPI" para estes de seu serviço de Pesquisa do Azure e da API do Bing Mapas. Para o nome de serviço da Pesquisa, se seu serviço é "mysearch.search.windows.net", você iria inserir "mysearch".

9. Clique com o botão direito do mouse no projeto **StoreIndexer** e escolha **Depurar** | **Iniciar nova instância** para executá-lo.

<a id="sub-4"></a>
## Adicionar o mapeamento para um aplicativo MVC4 usando a Pesquisa do Azure e o Bing Mapas

Nessa etapa, criaremos e executaremos o aplicativo de pesquisa em um navegador da Web que carregará a pesquisa para as lojas e traçará na parte superior de um mapa do Bing.

1.	No Visual Studio, abra a Solução de demonstração da Pesquisa do Azure chamada de AdventureWorksGeo.sln. 
	
2.	Clique com o botão direito do mouse em **AdventureWorksWebGeo** no Gerenciador de Soluções e selecione **Definir como projeto de inicialização**.
	
3.	Abra o Web.config neste projeto e atualize os valores para "SearchServiceName", "SearchServiceApiKey" e "BingMapsAPI" para escolher seu serviço de Pesquisa do Azure e a API do Bing Mapas. Para o nome de serviço da Pesquisa, se seu serviço é "mysearch.search.windows.net", você iria inserir "mysearch".

4.	Salve o Web.config.
	
5.	Pressione **F5** para iniciar o projeto. Siga essas etapas de [Solução de problemas](#err-mvc) se você receber um erro de compilação.

Observe como que as lojas são sobrepostas como pontos no mapa. Clique em uma das lojas e você verá um pop-up que descreve os detalhes da loja. Todas estas informações estão vindo do índice de Pesquisa do Azure chamado "stores" que foi criado nas etapas anteriores.

<a id="sub-5"></a>
## Explorar o AdventureWorksWebGeo

O projeto **AdventureWorksWebGeo** nos mostra como o ASP.NET MVC 4 pode ser usado para interagir com a Pesquisa do Azure para compilar um aplicativo de mapeamento que utiliza a geopesquisa. Nessa seção, revisaremos as partes individuais do código do aplicativo para ver o que eles fazem.

1.	No Solution Explorer, expanda **AdventureWorksWebGeo** | **Controlador** e abra HomeController.cs. A função **Index ()** é chamada quando o aplicativo é iniciado e a página de Índice é carregada. Nessa função a API do Bing Mapas é carregada do Web.config e passada para o modo de exibição do Index como ViewBag.BingAPI.

2.	Abra o Index.cshtml de **Exibições** | **Home**.

3.	Esse arquivo segue a forma típica que você adicionaria o Bing Mapas a um aplicativo da Web, no entanto, algumas coisas a destacar são:

+	O ViewBag do controlador é usado para carregar as credenciais do mapa usando: credenciais: '@ViewBag.BingAPI' 

+	Depois que o mapa é carregado um JQuery $.post é feito para a função de **Pesquisa** do HomeController consultando: /home/pesquisa

+	A função **Pesquisa** recupera os locais de lojas que são então recebidos como PushPins para o mapa do Bing.

4.	Abra o HomeController.cs em **Controladores** e examine a função **Pesquisa**. Observe como a chamada é feita para o \_storeSearch.Search(lat, lon, 10000). Isso fará que uma consulta seja executada para encontrar todas as lojas a uma distância de 10.000 Km da latitude (lat) e longitude (lon) especificadas. Os resultados dessa consulta são processados e depois enviados de volta para a exibição Index para serem processados como PushPins sobrepostos no mapa do Bing.

Isso conclui a demonstração. Você percorreu agora as operações principais que precisará conhecer antes desenvolver um mapa baseado no aplicativo ASP.NET MVC4 usando a Pesquisa do Azure.


<a id="err-mvc"></a>
## Como resolver “Não foi possível carregar o arquivo ou assembly 'System.Web.Mvc’”

Ao desenvolver o AdventureWorksWeb, se você receber “Não foi possível carregar o arquivo ou assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' ou uma de suas dependências", tente estas etapas para resolver o erro.

1. Abra o Console do Gerenciador de Pacotes: **Ferramentas** | **Gerenciador de Pacotes NuGet** | **Console do Gerenciador de Pacotes**
2. No prompt PM>, insira “Update-package -reinstall Microsoft.AspNet.Mvc”
3. Quando solicitado a recarregar o arquivo, escolha **Sim para todos**.
4. Recompile a solução e tente **F5** novamente.

<a id="next-steps"></a>
## Próximas etapas

Para um estudo individual adicional, considere adicionar mais capacidade ao aplicativo de mapeamento. Por exemplo, você talvez queira adicionar:

+ Caixa de Pesquisa, para permitir que os usuário pesquisem por lojas específicas.

+ Facetas para permitir aos usuário filtrarem por país ou província.

+ Áreas de seleção desenhadas pelo usuário que permitem aos usuários selecionar regiões específicas para serem pesquisadas ao arrastar uma área no mapa. A área é então filtrada pela Pesquisa do Azure usando a API geo-intersect e traçada no mapa.


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG

<!---HONumber=AcomDC_0224_2016-->