<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Como usar o Twilio para funcionalidades de voz e SMS do Azure

Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é Twilio?][O que é Twilio?]
-   [Preços do Twilio][Preços do Twilio]
-   [Conceitos][Conceitos]
-   [Criar uma conta no Twilio][Criar uma conta no Twilio]
-   [Verificar números telefônicos][Verificar números telefônicos]
-   [Criar um aplicativo Azure][Criar um aplicativo Azure]
-   [Configurar seu aplicativo para usar as bibliotecas do Twilio][Configurar seu aplicativo para usar as bibliotecas do Twilio]
-   [Como: Fazer uma chamada externa][Como: Fazer uma chamada externa]
-   [Como: Enviar uma mensagem SMS][Como: Enviar uma mensagem SMS]
-   [Como: Fornecer as respostas TwiML de seu próprio site][Como: Fornecer as respostas TwiML de seu próprio site]
-   [Como: Usar serviços adicionais do Twilio][Como: Usar serviços adicionais do Twilio]
-   [Próximas etapas][Próximas etapas]

## <span id="WhatIs"></span></a> O que é Twilio?

Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

## <span id="Pricing"></span></a>Preços e ofertas especiais do Twilio

Os clientes do Azure recebem uma [oferta especial][oferta especial]: US$10 de cortesia de crédito da Twilio quando atualizam a conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito da Twilio e faça uma introdução em [ahoy.twilio.com/azure][ahoy.twilio.com/azure].

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio][Preços da Twilio].

## <span id="Concepts"></span></a>Conceitos

A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][Bibliotecas de API da Twilio].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <span id="Verbs"></span></a>Verbos do Twilio

A API usa os verbos do Twilio; por exemplo, o verbo **\<Say\>** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio (a página pode estar em inglês)][Documentação da linguagem de marcação da Twilio (a página pode estar em inglês)].

-   **\<Dial\>**: Conecta o chamador com outro telefone.
-   **\<Gather\>**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
-   **\<Hangup\>**: Termina uma chamada.
-   **\<Play\>**: Reproduz um arquivo de áudio.
-   **\<Pause\>**: Espera silenciosamente por um número especificado de segundos.
-   **\<Record\>**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
-   **\<Redirect\>**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
-   **\<Reject\>**: Descarta uma chamada recebida para o número do Twilio sem cobrança
-   **\<Say\>**: Converte o texto em fala que é executada em uma chamada.
-   **\<Sms\>**: Envia uma mensagem SMS.

### <span id="TwiML"></span></a>TwiML

TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o **TwiMLResponse** objeto.

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][Documentação da linguagem de marcação da Twilio (a página pode estar em inglês)]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][API da Twilio].

## <span id="CreateAccount"></span></a>Criar uma conta no Twilio

Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][Avaliar a Twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta da Twilio][página da conta da Twilio], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <span id="VerifyPhoneNumbers"></span></a>Verificar os números telefônicos

Vários números de telefone devem ser verificados com Twilio para a sua conta. Por exemplo, se você quiser fazer chamadas telefônicas, o número de telefone deve ser verificado como uma identificação de chamador saída com Twilio. Da mesma forma, se você desejar um número de telefone para receber mensagens SMS, o número de telefone de recebimento deve ser verificado com Twilio. Para obter informações sobre como verificar um número telefônico, consulte [Gerenciar números][Gerenciar números]. Parte do código abaixo se baseia em números de telefone que você precisará verificar com Twilio.

Como uma alternativa para usar um número existente para seus aplicativos, você pode adquirir um número de telefone Twilio. Para obter informações sobre como adquirir um número de telefone do Twilio, consulte [Ajuda para números de telefone do Twilio][Ajuda para números de telefone do Twilio].

## <span id="create_app"></span></a>Criar um aplicativo Azure

Um aplicativo do Azure que hospeda um aplicativo Twilio habilitado não é diferente de qualquer outro aplicativo do Azure. Basta adicionar a biblioteca do .NET Twilio e configurar a função para usar as bibliotecas do .NET de Twilio.
Para informações sobre criar um projeto Azure inicial, consulte [Criando um projeto Azure com o Visual Studio][Criando um projeto Azure com o Visual Studio].

## <span id="configure_app"></span></a>Configurar seu aplicativo para usar as bibliotecas do Twilio

Twilio fornece um conjunto de bibliotecas do .NET auxiliar que encapsular vários aspectos de Twilio para fornecer maneiras simples e fáceis de interagir com o API REST de Twilio e Twilio cliente para gerar respostas de TwiML.

O Twilio fornece cinco bibliotecas para desenvolvedores .NET:

<table border="1">

<tr>

<th>
Biblioteca

</th>

<th>
Descrição

</th>

</tr>

<tr>

<td>
Twilio.API

</td>

<td>
A biblioteca de Twilio de núcleo que encapsula a API REST Twilio em uma biblioteca .NET amigável. Essa biblioteca está disponível para o .NET, Silverlight e Windows Phone 7.

</td>

</tr>

<tr>

<td>
Twilio.TwiML

</td>

<td>
Fornece uma maneira amigável do .NET para gerar marcação de TwiML.

</td>

</tr>

<tr>

<td>
Twilio.MVC

</td>

<td>
Para os desenvolvedores que usam o ASP.NET MVC, esta biblioteca inclui um TwilioController, o TwiML ActionResult e o atributo de validação de solicitação.

</td>

</tr>

<tr>

<td>
Twilio.WebMatrix

</td>

<td>
Para os desenvolvedores usando a ferramenta de desenvolvimento do WebMatrix gratuita da Microsoft, esta biblioteca contém auxiliares de sintaxe do Razor para várias ações de Twilio.

</td>

</tr>

<tr>

<td>
Twilio.Client.Capability

</td>

<td>
Contém o gerador de token de capacidade para uso com o SDK do Twilio cliente JavaScript.

</td>

</tr>

</table>
</p>
Observe que todas as bibliotecas requerem o .NET 3.5, Silverlight 4 ou Windows Phone 7 ou posterior.

Os exemplos fornecidos neste guia usam a biblioteca Twilio.API.

As bibliotecas podem ser [instaladas usando a extensão de Gerenciador de pacote do NuGet][instaladas usando a extensão de Gerenciador de pacote do NuGet] disponíveis para o Visual Studio 2010 e 2012. O código-fonte estiver hospedado em [GitHub][GitHub], que inclui um Wiki que contém a documentação completa para usar as bibliotecas.

Por padrão, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. As bibliotecas de Twilio a instalação requer a versão 1.6 do NuGet ou superior. Para obter informações sobre como instalar ou atualizar o NuGet, consulte [][]<http://nuget.org/></a>.

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>Para instalar a vers&atilde;o mais recente do NuGet, primeiro desinstale a vers&atilde;o carregada usando o Gerenciador de extens&otilde;es do Visual Studio. Para fazer isso, voc&ecirc; deve executar o Visual Studio como administrador. Caso contr&aacute;rio, o bot&atilde;o Desinstalar &eacute; desabilitado.</p>
</div>

### <span id="use_nuget"></span></a>Para adicionar as bibliotecas Twilio ao seu projeto do Visual Studio:

1.  Abra sua solução no Visual Studio.
2.  Clique com o botão direito do mouse em **Referências**.
3.  Clique em **Gerenciar Pacotes NuGet...**
4.  Clique em **Online**.
5.  Na caixa Pesquisar Online, digite *tiwlio*.
6.  Clique em **Instalar** no pacote Twilio.

## <span id="howto_make_call"></span></a>Como: Fazer uma chamada externa

Abaixo é mostrado como fazer uma chamada externa usando a classe **TwilioRestClient**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta da linguagem de marcação do Twilio (TwiML). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Instantiate the call options that are passed
    // to the outbound call
    CallOptions options = new CallOptions();

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Make the call.
    var call = client.InitiateOutboundCall(options);

Para obter mais informações sobre os parâmetros passados para o **cliente.InitiateOutboundCall** método, consulte [][1]<http://www.twilio.com/docs/api/rest/making-calls></a>

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como Fornecer as respostas TwiML de seu próprio site][Como: Fornecer as respostas TwiML de seu próprio site].

## <span id="howto_send_sms"></span></a>Como: Enviar uma mensagem SMS

A seguinte captura de tela mostra como enviar uma mensagem de SMS usando a classe **TwilioRestClient**. O **de** número é fornecido por Twilio para contas de avaliação para enviar mensagens SMS. O **para** número deve ser verificado para sua conta de Twilio antes de executar o código.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

## <span id="howto_provide_twiml_responses"></span></a>Como: Fornecer as respostas TwiML de seu próprio site

Quando o aplicativo inicia uma chamada para a API Twilio - por exemplo, por meio de **cliente.InitiateOutboundCall** método - Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo em [Como: Fazer uma chamada external][Como: Fazer uma chamada externa] usa a URL fornecida pelo Twilio [][2]<http://twimlets.com/message></a> para retornar a resposta.

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>Enquanto TwiML destina-se ao uso pelos servi&ccedil;os Web, &eacute; poss&iacute;vel exibir o TwiML no seu navegador. Por exemplo, clique em [http://twimlets.com/message](http://twimlets.com/message) para ver um elemento &lt;Response&gt; vazio; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) para ver um elemento &lt;Response&gt; que cont&eacute;m um elemento &lt;Say&gt;.</p>
</div>

Em vez de confiar na URL fornecida pela Twilio, você pode criar seu próprio site na URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP. Este tópico pressupõe que você irá hospedando a URL de um manipulador genérico do ASP.NET.

O seguinte manipulador de ASP.NET monta uma resposta de TwiML, diz **Hello World** na chamada.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = System.Text.Encoding.UTF8;
                string twiMLResponse = "<Response><Say>Hello World.</Say></Response>";
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Como você pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca do Twilio.TwiML contém classes que irão gerar TwiML para você. O exemplo a seguir produz a resposta equivalente como mostrado acima, mas usa a classe TwilioResponse.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new Twilio.TwiML.TwilioResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Para obter mais informações sobre TwiML, consulte [][3]<https://www.twilio.com/docs/api/twiml></a>.

Depois que você configurar uma forma de fornecer respostas TwiML, você pode passar essa URL para o **cliente.InitiateOutboundCall** método. Por exemplo, se você tiver um aplicativo da web chamado MyTwiML implantado em um serviço de nuvem do Azure e o nome de seu manipulador do ASP.NET for mytwiml.ashx, o URL pode ser passado para **cliente.InitiateOutboundCall** conforme mostrado no exemplo de código a seguir:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);

Para obter informações adicionais sobre como usar Twilio no Azure com o ASP.NET, consulte [como fazer uma chamada telefônica usando Twilio em uma função Web no Azure][como fazer uma chamada telefônica usando Twilio em uma função Web no Azure]

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]

  [Próximas etapas]: #NextSteps
  [Preços do Twilio]: #Pricing
  [Conceitos]: #Concepts
  [Criar uma conta no Twilio]: #CreateAccount
  [Verificar números telefônicos]: #VerifyPhoneNumbers
  [Criar um aplicativo Azure]: #create_app
  [Configurar seu aplicativo para usar as bibliotecas do Twilio]: #configure_app
  [Como: Fazer uma chamada externa]: #howto_make_call
  [Como: Enviar uma mensagem SMS]: #howto_send_sms
  [Como: Fornecer as respostas TwiML de seu próprio site]: #howto_provide_twiml_responses
  [Como: Usar serviços adicionais do Twilio]: #AdditionalServices
  [oferta especial]: http://www.twilio.com/azure
  [ahoy.twilio.com/azure]: http://ahoy.twilio.com/azure
  [Preços da Twilio]: http://www.twilio.com/voice/pricing
  [Bibliotecas de API da Twilio]: https://www.twilio.com/docs/libraries
  [API da Twilio]: http://www.twilio.com/api
  [Avaliar a Twilio]: https://www.twilio.com/try-twilio
  [página da conta da Twilio]: https://www.twilio.com/user/account
  [Gerenciar números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Ajuda para números de telefone do Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Criando um projeto Azure com o Visual Studio]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405487.aspx
  [instaladas usando a extensão de Gerenciador de pacote do NuGet]: http://www.twilio.com/docs/csharp/install
  [GitHub]: https://github.com/twilio/twilio-csharp
  []: http://nuget.org/
  [1]: http://www.twilio.com/docs/api/rest/making-calls
  [2]: http://twimlets.com/message
  [3]: https://www.twilio.com/docs/api/twiml
  [como fazer uma chamada telefônica usando Twilio em uma função Web no Azure]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/

