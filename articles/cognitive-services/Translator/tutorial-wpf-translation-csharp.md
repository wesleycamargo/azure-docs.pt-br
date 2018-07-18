---
title: Como escrever um aplicativo WPF do Microsoft Translator com C#, Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba como usar o serviço Tradução de Texto para traduzir texto, obter uma lista localizada de idiomas com suporte e muito mais.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363758"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>Como escrever um aplicativo WPF do Microsoft Translator em C#

Neste tutorial, criaremos uma ferramenta de conversão de texto interativa usando a API de Tradução de Texto da Microsoft (V3), uma parte dos Serviços Cognitivos da Microsoft no Azure. Você aprenderá a:

> [!div class="checklist"]
> * Solicitar uma lista de códigos curtos para idiomas com suporte por serviço
> * Recuperar uma lista de nomes de idioma localizados correspondente a esses códigos de idioma
> * Obter a versão traduzida do texto inserido pelo usuário de um idioma para outro

Este aplicativo também apresenta uma integração com dois outros Serviços Cognitivos da Microsoft.

|||
|-|-|
|[Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Usada para detectar de modos automático e opcional o idioma de origem do texto a ser traduzido|
|[Verificação de Ortografia do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Para o texto de origem em português, usado para corrigir erros de ortografia para que a tradução seja mais precisa

![[Programa do tutorial em execução]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>pré-requisitos

Para esse tutorial, você precisa de qualquer edição do Visual Studio 2017, incluindo a Community Edition.

Também é preciso ter chaves de assinatura para os três serviços do Azure usados no programa. Você pode obter uma chave para o serviço de Tradução de Texto no painel do Azure. Um tipo de preço gratuito está disponível e permite converter até dois milhões de caracteres por mês sem custo adicional.

Os serviços de Análise de Texto e Verificação Ortográfica do Bing têm avaliações gratuitas, e você pode se inscrever para [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Você também pode criar uma assinatura para o serviço via painel do Azure. A Análise de Texto tem uma camada gratuita.

O código-fonte desse tutorial está disponível abaixo. As chaves de assinatura devem ser copiadas para o código-fonte, como as variáveis `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` etc., em `MainWindow.xaml.cs`.

> [!IMPORTANT]
> O serviço de Análise de Texto está disponível em várias regiões. A URI no código-fonte do tutorial está na região `westus`, que é a usada para avaliações gratuitas. Se você tiver uma assinatura em outra região, atualize a URI de acordo.

## <a name="source-code"></a>Código-fonte

Este é o código-fonte para a API de texto do Microsoft Translator. Para executar o aplicativo, copie o código-fonte para o arquivo apropriado em um novo projeto WPF no Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Este é o arquivo code-behind que fornece a funcionalidade do aplicativo.

```cs
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog when we get an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

O arquivo define a interface do usuário para o aplicativo, um formulário WPF. Se você quiser criar sua própria versão do formulário, esse XAML não será necessário.

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox" 
                HorizontalAlignment="Left" 
                Margin="306,88,0,0" 
                VerticalAlignment="Top" 
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox" 
            HorizontalAlignment="Left" 
            Margin="42,88,0,0" 
            VerticalAlignment="Top" 
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Pontos de extremidade de serviço

O serviço Microsoft Translator tem vários pontos de extremidade que fornecem diversas partes da funcionalidade de tradução. Os que usamos neste tutorial são:

|||
|-|-|
|`Languages`|Retorna o conjunto de idiomas atualmente suportados por outras operações da API de Tradução de Texto.|
|`Translate`|Dado um texto de origem, um código-fonte e um código de destino do idioma retornam uma tradução do texto de origem para o idioma de destino.|

## <a name="the-translation-app"></a>Aplicativo de tradução

A interface do usuário do aplicativo de tradução é criada usando o Windows Presentation Foundation (WPF). Crie um novo projeto WPF no Visual Studio seguindo estas etapas.

* No menu **Arquivo**, clique em **Novo > Projeto**.
* Na janela Novo Projeto, abra **Modelos Instalados > Modelos > Visual C#**. Uma lista de modelos de projeto disponíveis aparecerá no centro da caixa de diálogo.
* Verifique se **.NET Framework 4.5.2** está selecionado no menu suspenso acima da lista de modelos de projeto.
* Clique em **Aplicativo WPF (.NET Framework)** na lista de modelos de projeto.
* Usando os campos na parte inferior da caixa de diálogo, nomeie o novo projeto e a solução que o contém.
* Clique em **OK** para criar o novo projeto e a solução.

![[Criar um novo aplicativo WPF no Visual Studio]](media/translator-text-csharp-new-project.png)

Adicione referências para os seguintes assemblies do .NET framework ao seu projeto.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Além disso, instale o pacote NuGet `Newtonsoft.Json` em seu projeto.

Agora, localize o arquivo `MainWindow.xaml` no Gerenciador de Soluções e abra-o. Inicialmente ele está em branco. Veja como deve ser a interface do usuário terminada, anotada com os nomes dos controles em azul. Use os mesmos nomes para os controles na interface do usuário, uma vez que também aparecem no código.

![[Visualização anotada da janela principal no designer do Visual Studio]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> O código-fonte para este tutorial inclui a fonte XAML deste formulário. Você pode colá-lo no projeto, em vez de criar o formulário no Visual Studio.

* `FromLanguageComboBox` *(ComboBox)*  - Exibe uma lista dos idiomas suportados pelo Microsoft Translator para a tradução de texto. O usuário seleciona o idioma a partir do qual traduzir.
* `ToLanguageComboBox` *(ComboBox)*  - Exibe a mesma lista de idiomas de `FromComboBox`, mas é usado para selecionar o idioma para o qual usuário está traduzindo.
* `TextToTranslate` *(TextBox)*  - O usuário insere o texto a ser traduzido aqui.
* `TranslateButton` *(Button)*  - O usuário clica nesse botão (ou pressiona Enter) para traduzir o texto.
* `TranslatedTextLabel` *(Label)*  - A tradução do texto do usuário é exibida aqui.

Se você estiver criando sua própria versão do formulário, não será necessário que seja *exatamente* igual ao nosso. Mas, verifique se as listas suspensas de idiomas são grandes o suficiente para evitar cortes no nome de um idioma.

## <a name="the-mainwindow-class"></a>Classe MainWindow

O arquivo code-behind `MainWindow.xaml.cs` é onde o código fica e faz o programa cumprir sua função. O trabalho ocorre em duas etapas:

* Quando o programa inicia. Quando `MainWindow` é instanciado, recuperamos a lista de idiomas usando as APIs `GetLanguagesForTranslate` e `GetLanguageNames` do Translator e preenchemos os menus suspensos com eles. Essa tarefa é feita uma vez, no início de cada sessão.

* Quando o usuário clica no botão **Traduzir**, recuperamos as seleções de idioma dele e o texto inserido. Em seguida, chamamos a API `Translate` para executar a tradução. Podemos também chamar outras funções para determinar o idioma do texto e corrigir a ortografia antes da tradução.

Vejamos como podemos iniciar nossa classe:

```cs
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog when we get an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Duas variáveis de membro declaradas aqui mantêm informações sobre os idiomas:

|||
|-|-|
|`languageCodes`<br>matriz de cadeias de caracteres|Armazena em cache os códigos do idioma. O serviço Translator usa códigos curtos, como `en` para português, para identificar os idiomas.|
|`languageCodesAndTitles`<br>SortedDictionary|Mapeia os nomes "amigáveis" na interface do usuário de volta para os códigos curtos usados na API. A classificação é em ordem alfabética, sem diferenciar as letras maiúsculas e minúsculas.|

O primeiro código executado por nosso aplicativo é o construtor `MainWindow`. Primeiro, definimos o método `HandleExceptions` como o manipulador de erro global. Dessa forma, pelo menos obteremos um alerta de erro se qualquer exceção não for lidada.

Em seguida, verificamos se as chaves de assinatura da API têm exatamente 32 caracteres de comprimento. Se não tiverem, o motivo mais provável é que *alguém* não colou suas chaves de API (tsk). Nesse caso, exibimos uma mensagem de erro e socorremos. (Passar no teste não significa que as chaves são válidas, claro.)

Se tivermos chaves pelo menos com o comprimento correto, a chamada `InitializeComponent()` manterá a interface do usuário em execução localizando, carregando e instanciando a descrição XAML da janela principal do aplicativo.

Por fim, configuramos os menus suspensos do idioma. Essa tarefa requer três chamadas de método separadas. Veremos esses métodos em detalhes nas seções a seguir.

## <a name="get-supported-languages"></a>Obter idiomas com suporte

O serviço Microsoft Translator dá suporte a um total de 61 idiomas neste documento e mais podem ser adicionados de tempos em tempos. Portanto convém não incorporar o código dos idiomas com suporte em seu programa. Em vez disso, pergunte ao serviço Translator a quais idiomas ele dá suporte. Qualquer idioma com suporte pode ser traduzido em outros com suporte.

Chame a API `Languages` para obter a lista de idiomas com suporte.

A API `Languages` tem um parâmetro de consulta GET opcional, *scope*. O *scope* pode ter um dos três valores: `translation`, `transliteration` e `dictionary`. Usaremos o valor `translation`.

A API `Languages` também usa um cabeçalho HTTP opcional, `Accept-Language`. O valor desse cabeçalho determina o idioma no qual os nomes dos idiomas com suporte são retornados. O valor deve ser uma marca de linguagem BCP 47 bem formada. Usaremos o valor `en` para obter os nomes dos idiomas em português.

A API `Languages` retorna uma resposta JSON semelhante ao seguinte.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

Queremos extrair os códigos do idioma (por exemplo, `af`) e os nomes do idioma (por exemplo, `Afrikaans`). Usamos o método NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) para fazer isso.

Com essas informações, criamos o seguinte método para recuperar os códigos do idioma e seus nomes.

```cs
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

`GetLanguagesForTranslate()` primeiro cria a solicitação HTTP. O parâmetro da cadeia de consulta `scope=translation` especifica que queremos os idiomas com suporte para a tradução de texto. Adicionamos a chave de assinatura da API de Tradução de Texto aos cabeçalhos de solicitação. Também adicionamos o cabeçalho `Accept-Language` com o valor `en` para indicar que desejamos os idiomas com suporte retornados em português.

Depois da solicitação ser concluída, analisamos a resposta JSON e a convertemos em um Dicionário. Adicionamos os códigos do idioma à variável de membro `languageCodes`. Em seguida, fazemos um loop nos pares de chave/valor que contêm os códigos do idioma e os nomes amigáveis, e adicionamos à variável de membro `languageCodesAndTitles`. (Os menus suspensos no nosso formulário exibem os nomes amigáveis, mas precisamos de códigos para solicitar a tradução.)

## <a name="populate-the-language-menus"></a>Preencher os menus de idioma

Grande parte de nossa interface do usuário é definida em XAML, portanto não precisamos fazer muito para configurá-la, exceto chamar `InitializeComponent()`. A única coisa que precisamos fazer é adicionar os nomes amigáveis do idioma às listas suspensas Para e De, o que é feito em `PopulateLanguageMenus()`.

```cs
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Preencher os menus é uma simples questão de iterar o dicionário `languageCodesAndTitles` e adicionar cada chave, que é o nome "amigável", aos dois menus. Depois de preencher os menus, podemos definir os idiomas padrão Para e De para Detectar (detectar automaticamente o idioma) e Português.

> [!TIP]
> Se não definirmos uma seleção padrão para os menus, o usuário poderá clicar em **Traduzir** sem escolher um idioma Para ou De. Os padrões eliminam a necessidade de lidar com esse problema.

Agora `MainWindow` foi inicializado, criando a interface do usuário. Não temos controle novamente até que o usuário clique no botão **Traduzir**.

## <a name="perform-translation"></a>Executar a tradução

Quando o usuário clica em **Traduzir**, o WPF chama o manipulador de eventos `TranslateButton_Click()`, mostrado aqui.

```cs
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Aqui, recuperamos primeiro os idiomas Para e De no formulário, junto com o texto que o usuário inseriu.

Se o idioma de origem estiver definido para Detectar, chamaremos `DetectLanguage()` para determinar o idioma do texto. O texto pode estar em um idioma que as APIs do Translator não suportam (podem ser detectados muito mais idiomas do que podem ser traduzidos) ou a API de Análise de Texto não consegue detectá-lo. Nesse caso, exibimos uma mensagem para informar o usuário e retornar sem tradução.

Se o idioma de origem for o português (especificado ou detectado), verificaremos a ortografia do texto com `CorrectSpelling()` e aplicaremos todas as correções. O texto corrigido é colocado de volta no campo de entrada para que o usuário saiba que a correção foi feita. (O usuário pode anteceder o texto a ser traduzido com um hífen para omitir a correção ortográfica.)

Se o usuário não inseriu nenhum texto ou se os idiomas Para e De forem iguais, nenhuma tradução será necessária. Nesse caso, podemos evitar fazer a solicitação.

O código para executar a solicitação de tradução deve parecer familiar. Compilamos a URI, criamos uma solicitação, enviamos e analisamos a resposta. Para exibir o texto, podemos armazená-lo no controle `TranslatedTextLabel`.

Passamos o texto para a API `Translate` em uma matriz serializada JSON, no corpo de uma solicitação POST. A matriz JSON pode conter várias partes de texto a traduzir, mas aqui incluímos apenas uma.

O cabeçalho HTTP chamado `X-ClientTraceId` é opcional. O valor deve ser um GUID. A ID de rastreamento fornecida pelo cliente é útil para rastrear as solicitações quando as coisas não funcionam como o esperado. No entanto, para ser útil, o valor de X-ClientTraceID deve ser registrado pelo cliente. Uma ID de rastreamento do cliente e a data das solicitações podem ajudar a Microsoft a diagnosticar os problemas que podem ocorrer.

> [!NOTE]
> Este tutorial concentra-se no serviço Microsoft Translator, portanto não abordamos os métodos `DetectLanguage()` e `CorrectSpelling()` em detalhes. Os serviços de Análise de Texto e Verificação Ortográfica do Bing fornecem respostas em JSON, não em XML, e a Análise de Texto requer que a solicitação esteja formatada, bem como o JSON. Essas características explicam a maioria das diferenças de código nos métodos que já vimos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API de Tradução de Texto da Microsoft](http://docs.microsofttranslator.com/text-translate.html)
