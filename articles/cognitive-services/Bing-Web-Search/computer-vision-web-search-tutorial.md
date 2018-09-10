---
title: Tutorial de aplicativo móvel da Pesquisa Visual
description: Aplicativo C# de software livre implementando pesquisa visual usando a API da Pesquisa Visual Computacional dos Serviços Cognitivos, API de Pesquisa na Web do Bing e estrutura de plataforma cruzada Xamarin.Forms.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363729"
---
# <a name="visual-search-mobile-app-tutorial"></a>Tutorial de aplicativo móvel da Pesquisa Visual

## <a name="introduction"></a>Introdução  
Este tutorial explora a [API da Pesquisa Visual Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) e os pontos de extremidade da [API de Pesquisa na Web do Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) e mostra como podem ser utilizados para criar um aplicativo de pesquisa visual básico com [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  No geral, este tutorial abrange os seguintes tópicos: 

> [!div class="checklist"]
> * Configurar o Visual Studio para desenvolver aplicativos Xamarin.Forms
> * Usar o [Plug-in de Mídia do Xamarin](https://github.com/jamesmontemagno/MediaPlugin) para capturar e importar dados de imagem
> * Analisar texto de uma imagem usando as APIs da Pesquisa Visual Computacional
> * Enviar solicitações de pesquisa para a API de Pesquisa na Web do Bing
> * Analisar respostas JSON de ambas as APIs com [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (com LINQ e deserialização de objeto de modelo)
> * Criar uma interface do usuário Xamarin.Forms de plataforma cruzada para pesquisa visual 

## <a name="prerequisites"></a>pré-requisitos

Este exemplo foi desenvolvido usando o Xamarin.Forms com o [Visual Studio 2017](https://www.visualstudio.com/downloads/). O Community Edition do Visual Studio 2017 pode ser usado. Para obter mais informações sobre o uso do Xamarin com Visual Studio, consulte a [documentação do Xamarin](https://developer.xamarin.com/guides/cross-platform/getting_started/).

Este exemplo utiliza os pacotes do NuGet a seguir:

> [!div class="checklist"]
> * [Plug-in de Mídia do Xamarin](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

O aplicativo usa as API de Serviços Cognitivos a seguir:

> [!div class="checklist"]
> * [API de Pesquisa na Web do Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [API da Pesquisa Visual Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

Para obter chaves de avaliação de 30 dias para essas APIs, consulte [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Para obter mais informações sobre como obter chaves para uso comercial, consulte [Preço](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>Configuração para desenvolvimento  

### <a name="installing-xamarin-on-windows"></a>Instalar o Xamarin no Windows
Com qualquer edição do Visual Studio 2017 instalada, abra o Instalador do Visual Studio, selecione o menu de hambúrguer associado à sua instalação do Visual Studio e escolha **Modificar**.  

![Instalador do Visual Studio](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Role para baixo até Móvel e Jogos e habilite **Desenvolvimento Mobile com .NET**, se ainda não estiver habilitado.

![Instalador do Visual Studio com Xamarin.Forms selecionado](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Por fim, clique em **Modificar** no canto inferior direito da janela e aguarde a instalação do Xamarin.

### <a name="installing-xamarin-on-macos"></a>Instalar o Xamarin no macOS
O Xamarin vem pré-empacotado com Visual Studio para Mac. Nenhuma instalação deverá ser necessária.

## <a name="building-and-running-the-app"></a>Compilar e executar o aplicativo

Um arquivo de solução do Visual Studio *(.sln)* para o aplicativo de Pesquisa Visual pode ser baixado do [Aplicativo de Pesquisa Visual com Serviços Cognitivos](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/). É possível baixar o arquivo ZIP usando um navegador da Web, cloná-lo na estação de trabalho a partir do GitHub ou baixá-lo usando o Visual Studio.

Para começar a trabalhar com o exemplo, abra `VisualSearchApp.sln` no Visual Studio.  Inicializar os componentes necessários pode demorar um pouco.

O aplicativo exige duas bibliotecas de terceiros: **Json.NET** e o **Plug-in de Mídia do Xamarin**. É possível instalar essas bibliotecas diretamente no Visual Studio com o Gerenciador de Pacotes do NuGet. Escolha **Ferramentas > Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para Solução**, ou clique com o botão direito do mouse na solução no Gerenciador de Soluções e escolha **Gerenciar Pacotes do NuGet** no menu de contexto.

Na janela do NuGet, pesquise e instale **plug-in de Mídia do Xamarin** (Xam.Plugin.Media) versão 2.6.2 e **Json.NET** (Newtonsoft.Json) 10.0.3. Certifique-se de selecionar todos os projetos ao instalar.

Para compilar o aplicativo para todas as plataformas disponíveis, pressione **Ctrl + Shift + B** ou clique em **Compilar** no menu de faixa de opções e escolha **Compilar Solução**.  Para compilar e testar o código para iOS a partir de um sistema Windows, consulte [este guia](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/) para obter ajuda.

Antes de executar o aplicativo, é necessário selecionar uma Configuração, Plataforma e Projeto de destino.  Aplicativos Xamarin.Forms compilam para código nativo para Windows, Android e iOS. Este tutorial inclui capturas de tela da versão do Windows de exemplo, mas todas as versões são funcionalmente equivalentes. As configurações de implantação usadas neste guia são mostradas aqui.  

![Visual Studio configurado para compilar para um celular Android](./media/computer-vision-web-search-tutorial/configuration-selection.png)

Após o processo de compilação ocorrer com êxito e você tiver selecionado uma plataforma de destino, clique no botão **Iniciar** na barra de ferramentas ou pressione **F5**. O Visual Studio implanta a solução na plataforma de destino e a inicializa.

A página Adicionar Chaves é exibida. (Esta página é definida em `AddKeysPage.xaml`.)  

![Imagem da página Adicionar Chaves](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Aqui, cole as chaves de API da Pesquisa Visual Computacional e API de Pesquisa na Web do Bing. A API da Pesquisa Visual Computacional também exige que você escolha o servidor no qual o ponto de extremidade está hospedado.

> [!TIP]
> Para ignorar essa página, insira essas informações nos locais apropriados em `App.xaml.cs`. 

O aplicativo valida as chaves realizando uma consulta de teste e, em seguida, direciona-o para a página Seleção de OCR (definida em `OcrSelectPage.xaml`).
   
![Imagem da página Seleção de OCR](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

Na parte superior dessa tela, é possível escolher se o texto que você quer reconhecer é impresso ou manuscrito.

> [!TIP]
> Mantenha o item que você está tentando reconhecer o texto o mais nivelado possível e certifique-se de que está uniformemente iluminado, sem reflexos. Descobrimos que o OCR de Manuscrito às vezes funciona melhor para fontes de script ou outro texto "sofisticado".

Em seguida, clique em **Tirar Foto** ou **Importar Foto** para tirar uma foto usando a câmera do dispositivo ou escolher uma foto armazenada no dispositivo.

Depois que uma foto é tirada ou escolhida, a imagem é passada para a API da Pesquisa Visual Computacional. A página Palavras Encontradas (definida em `OcrResultsPage.xaml`) exibe quaisquer palavras reconhecidas na imagem.

![Imagem da página Resultados de OCR](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> A imagem que usamos para esses resultados está no repositório de origem como `SamplePhotos\TestImage.jpg`.

Ao clicar em um item na página Palavras Encontradas, a página Resultados da Web (`WebResultsPage.xaml`) é exibida, mostrando os principais resultados do Bing para essa pesquisa.

![Imagem da página Resultados da Web](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Por fim, escolha um item na página Resultados da Web para abrir o link em um WebView inserido. (Ou navegue de volta para escolher um resultado diferente.)

![Imagem da página Exibição da Web](./media/computer-vision-web-search-tutorial/web-view-page.png)  

É possível interagir com a página como faria em qualquer navegador da Web ou navegar de volta para a página Resultados da Web. 

## <a name="review-and-learn"></a>Analisar e aprender

Agora que você tem o aplicativo de Pesquisa Visual para experimentar, vamos explorar exatamente como estamos usando as duas APIs de Serviços Cognitivos da Microsoft.  Se você estiver usando esse exemplo como ponto de partida para o próprio aplicativo ou, simplesmente como um procedimento para as APIs, é importante percorrer o aplicativo tela a tela para examinar exatamente como funciona.

### <a name="add-keys-page"></a>Página Adicionar Chaves
A interface do usuário da página Adicionar Chaves é definida em `AddKeysPage.xaml` e a lógica primária é definida em `AddKeysPage.xaml.cs`. Como as chaves são validadas fazendo uma solicitação de teste, é o momento de estabelecer como usar os pontos de extremidade dos Serviços Cognitivos em C#.  

A estrutura básica dessa interação é: 

1. Instanciar objetos *HttpResponseMessage* and *HttpClient* de *System.Net.Http*
2. Anexar quaisquer cabeçalhos desejados (definidos na referência da API de cada ponto de extremidade) ao objeto *HttpClient*
3. Enviar uma solicitação GET ou POST com os dados, adicionando os parâmetros necessários ao URI do ponto de extremidade
4. Verifique se a resposta teve êxito
5. Passar a resposta para processamento adicional

A função que verifica a validade da chave de API de Pesquisa do Bing é `CheckBingSearchKey()`, mostrado aqui.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

Uma função similar, `CheckComputerVisionKey()`, é usada para validar a chave de Pesquisa Visual Computacional.

### <a name="ocr-select-page"></a>Página Seleção de OCR

A página Seleção de OCR (`OcrSelectPage.xaml`) tem duas funções importantes. Primeira, determina que tipo de OCR é realizado na foto de destino. Segunda, permite ao usuário capturar ou abrir a imagem que quer processar.

A segunda tarefa é tradicionalmente complexa em um aplicativo de plataforma cruzada, porque cada plataforma exige um código diferente. O Plug-in de Mídia do Xamarin manipula apenas algumas linhas de código.

A função a seguir incorpora um exemplo do uso do Plug-in de Mídia do Xamarin para captura de foto.  Nela, é possível:

1. Certificar-se de que uma câmera está disponível no dispositivo atual
2. Instanciar um objeto `StoreCameraMediaOptions` para especificar onde salvar a imagem capturada
3. Capturar uma imagem e obter um objeto `MediaFile` contendo os dados da imagem
4. Desempacotar o `MediaFile` em uma matriz de bytes
5. Retornar a matriz de bytes para processamento adicional

Aqui está `TakePhoto()`, a função que usa o Plug-in de Mídia do Xamarin para captura de foto.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
O utilitário de importação de fotos funciona de maneira semelhante. Ambas as partes da funcionalidade podem ser encontradas em `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> O ponto de extremidade de OCR de Manuscrito somente pode manipular fotos com menos de 4 MB. Para evitar problemas de tamanho de arquivo, reduzimos a foto para 50% do tamanho original definindo a opção `PhotoSize = PhotoSize.Medium` no objeto `StoreCameraMediaOptions`.  Se o dispositivo tirar fotos excepcionalmente de alta qualidade e você estiver recebendo erros, tente `PhotoSize = PhotoSize.Small`.

Aqui, está a função de utilitário usada para converter um *MediaFile* em uma matriz de bytes.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>Página Resultados de OCR

A página Resultados de OCR exibe o texto extraído da imagem usando o **Json.NET** [método SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm).  Os dois pontos de extremidade de OCR funcionam de forma um pouco diferente, por isso é importante discutir ambos.  

Como a API da Pesquisa Visual Computacional está hospedada apenas em alguns locais do servidor, o ponto de extremidade deve ser construído dinamicamente no tempo de execução. A função `SetOcrLocation` (parte da classe estática *AppConstants* localizada em `AppConstants.cs`) define o local do ponto de extremidade do URI. Isso corresponde à seleção do usuário na Página Adicionar Chaves ou usa o valor definido em `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Vamos examinar mais detalhadamente essas solicitações de API. A API de OCR da Pesquisa Visual Computacional é capaz de analisar texto de um idioma indeterminado, porém informamos para procurar texto em inglês com o objetivo de melhorar os resultados. Adicionalmente, permitimos que a API detecte a orientação do texto. O uso de uma orientação fixa pode melhorar nossos resultados de análise, mas a detecção de orientação pode ser útil em um aplicativo móvel.

É possível saber mais sobre os parâmetros disponíveis com esse ponto de extremidade a partir da [Referência de API de Reconhecimento de Caractere Óptico de Impressão](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc).  

A API de OCR de Manuscrito ainda está em versão prévia e atualmente funciona apenas com texto em inglês.  Por esse motivo, o único parâmetro atualmente é um sinalizador indicando se deve ou não analisar texto manuscrito. A API de OCR de Manuscrito pode analisar o texto manuscrito e impresso no computador, mas `handwriting=false` produz melhores resultados no texto impresso. 

Como esse aplicativo está em inglês, poderíamos ter usado apenas o OCR de Manuscrito para esse exemplo e definir o sinalizador `handwriting`, de acordo com o que o usuário nos informa para reconhecer.  Usamos os dois pontos de extremidade para fins ilustrativos.  

É possível saber mais sobre os parâmetros disponíveis com esse ponto de extremidade a partir da [Referência de API de Reconhecimento de Caractere Óptico Manuscrito](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).

Agora, vamos examinar as funções que chamam essas APIs.

`FetchPrintedWordList()` usa o ponto de extremidade de OCR de Impressão para analisar o texto impresso das imagens. A solicitação HTTP segue uma estrutura semelhante à chamada usada na página Adicionar Chaves para validar a chave, mas precisamos enviar uma foto. Uma foto é muito grande para passar em uma cadeia de caracteres de consulta, portanto, devemos usar uma solicitação HTTP POST, em vez de uma solicitação GET. É necessário codificar a foto (que existe na memória como uma matriz de bytes) em um objeto `ByteArrayContent` e adicionar um cabeçalho a esse objeto definindo o tipo de dados que estamos enviando. 

Leia sobre os tipos de conteúdo aceitáveis na [Referência de API da Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Observe como estamos usando o **Json.NET** [método SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) para extrair texto do objeto de resposta. `SelectToken` é ideal quando precisamos apenas de uma parte específica da resposta JSON, que podemos então passar para a próxima função. Em outras partes do código, desserializamos as respostas JSON em objetos de modelo definidos em `ModelObjects.cs`.

A principal diferença entre a solicitação OCR de Impressão e OCR de Manuscrito é que o serviço OCR de Impressão retorna o texto reconhecido como parte da resposta HTTP, enquanto o serviço de OCR de Manuscrito exige uma solicitação adicional para obter as informações. A solicitação inicial de OCR de Manuscrito retorna um status HTTP 202, que sinaliza apenas que o processamento foi iniciado. Essa resposta contém um ponto de extremidade que o cliente deve verificar para obter a resposta completa quando estiver disponível. Consulte `FetchHandwrittenWordList()` para saber como tudo funciona.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` é a segunda parte do processo de OCR de Manuscrito. A função executa ping de URI de extração de metadados da resposta inicial até que um resultado seja obtido ou a função expire.  É importante que essa função seja chamada de forma assíncrona no próprio segmento. Caso contrário, esse método bloqueará a interface do usuário até que o processamento seja concluído.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Página Resultados da Web
Depois que o usuário seleciona um termo de pesquisa exibido na página Resultados de OCR, acessamos a página Resultados da Web.  Aqui, construímos uma solicitação de [API de Pesquisa na Web do Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/), a enviamos para o ponto de extremidade de serviço e desserializamos a resposta JSON usando o método **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm).  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

A API de Pesquisa na Web do Bing funciona melhor quando você fornece o máximo de informações possíveis sobre o que o usuário pode querer. Em particular, quase sempre será necessário usar os parâmetros `mkt` e `setLang` (definidos para inglês dos EUA) para identificar o local e o idioma preferido do usuário.

> [!NOTE]
> Mantivemos nossa consulta de Pesquisa na Web do Bing simples para garantir que o código-fonte fosse fácil de entender.  Em um aplicativo real, você deve adicionar os seguintes cabeçalhos à solicitação HTTP para obter melhores resultados. 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> É possível saber mais sobre esses valores de cabeçalho na [Referência de API de Pesquisa na Web do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)

## <a name="next-steps"></a>Próximas etapas
Os Serviços Cognitivos da Microsoft fornecem muitos serviços adicionais que você pode integrar facilmente a esse aplicativo.  Por exemplo, você pode:

* Adicionar a [Pesquisa de Entidade do Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para aumentar os resultados da pesquisa na Web
* Alternar [Pesquisa Personalizada do Bing](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) no lugar da Pesquisa na Web do Bing
* Usar o recurso de insights de imagem da [Pesquisa de Imagem do Bing](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) para saber mais sobre a imagem capturada e localizar imagens semelhantes na Web
* Implantar a [Verificação Ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) para melhorar ainda mais a qualidade do texto analisado
* Integrar o [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) para ver o texto extraído em diferentes idiomas
* Combinar e corresponder os outros serviços do [portal dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/); o céu é o limite!
