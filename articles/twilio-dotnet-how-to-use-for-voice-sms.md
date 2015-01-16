<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Como usar o Twilio para voz e SMS (.NET) - Azure"metaKeywords ="Azure Twilio, chamadas telefônicas do Azure, chamadas telefônicas Azure, Azure Twilio, Azure SMS, Azure SMS, chamadas de voz do Azure, chamadas de voz do Azure, mensagens de texto do Azure, mensagens de texto do Azure" description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Amostras de código gravadas no .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />





<h1>Como usar o Twilio para funcionalidades de voz e SMS do Azure</h1>

Este guia mostra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

<h2>Sumário</h2>
* [O que é Twilio?](#WhatIs)
* [Preços do Twilio](#Pricing)
* [Conceitos](#Concepts)
* [Criar uma conta no Twilio](#CreateAccount)
* [Verificar números telefônicos](#VerifyPhoneNumbers)
* [Criar um aplicativo Azure](#create_app)
* [Configurar seu aplicativo para usar as bibliotecas do Twilio](#configure_app)
* [Como: Fazer uma chamada externa](#howto_make_call)
* [Como: Enviar uma mensagem SMS](#howto_send_sms)
* [Como: Fornecer as respostas TwiML de seu próprio site](#howto_provide_twiml_responses)
* [Como: Usar serviços adicionais do Twilio](#AdditionalServices)
* [Próximas etapas](#NextSteps)

<h2><a id="WhatIs"></a>O que é Twilio?</h2>
O Twilio está possibilitando o futuro das comunicações de negócios, permitindo aos desenvolvedores inserir voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade do serviço e os benefícios da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **O cliente Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e dá suporte à WebRTC.

<h2><a id="Pricing"></a>Preços e ofertas especiais do Twilio</h2>
Os clientes do Azure recebem uma [oferta especial](http://www.twilio.com/azure): US$10 de cortesia de crédito da Twilio quando atualizam a conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito do Twilio e inicie uma introdução em [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

O Twilio é um serviço pré-pago. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio](http://www.twilio.com/voice/pricing).  

<h2><a id="Concepts"></a>Conceitos</h2>
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas do cliente estão disponíveis em vários idiomas; para obter uma lista, consulte [Bibliotecas da API do Twilio] [twilio_libraries].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

<h3><a id="Verbs"></a>Verbos de Twilio</h3>
A API usa os verbos do Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada. 

Veja a seguir uma lista de verbos do Twilio.  Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação do Twilio ](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: Conecta o chamador com outro telefone.
* **&lt;Gather&gt;**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Play&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para TwiML em um URL diferente.
* **&lt;Reject&gt;**: Descarta uma chamada recebida para o número do Twilio sem cobrar você
* **&lt;Say&gt;**: Converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** para fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API do Twilio, um dos parâmetros da API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar suas próprias URLs para produzir as respostas TwiML; outra opção é usar o objeto **TwiMLResponse**.

Para obter mais informações sobre os verbos do Twilio, seus atributos e o TwiML, consulte [TwiML] [twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [API do Twilio] [twilio_api].

<h2><a id="CreateAccount"></a>Criar uma conta no Twilio</h2>
Quando você estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio] [try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Ao se inscrever em uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta do Twilio] [twilio_account], nos campos rotulados como **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

<h2><a id="VerifyPhoneNumbers"></a>Verificar números telefônicos</h2>
Vários números de telefone devem ser verificados com o Twilio para a sua conta. Por exemplo, se você quiser fazer chamadas telefônicas, o número de telefone deve ser verificado como uma identificação de chamador saída com Twilio. Da mesma forma, se você desejar um número de telefone para receber mensagens SMS, o número de telefone de recebimento deve ser verificado com Twilio. Para obter informações sobre como verificar um número de telefone, consulte [Gerenciar números] [verify_phone]. Parte do código abaixo se baseia em números de telefone que você precisará verificar com o Twilio.

Como uma alternativa para usar um número existente para seus aplicativos, você pode adquirir um número de telefone Twilio. Para obter informações sobre como adquirir um número de telefone do Twilio, consulte [Ajuda para números de telefone do Twilio](https://www.twilio.com/help/faq/phone-numbers).

<h2><a id="create_app"></a>Criar um aplicativo Azure</h2>
Um aplicativo do Azure que hospeda um aplicativo Twilio habilitado não é diferente de qualquer outro aplicativo do Azure. Basta adicionar a biblioteca do .NET Twilio e configure a função para usar as bibliotecas do .NET de Twilio.
Para obter informações sobre como criar um projeto inicial do Azure, consulte [criando um projeto do Azure com o Visual Studio][vs_project].

<h2><a id="configure_app"></a>Configurar seu aplicativo para usar as bibliotecas do Twilio</h2>
Twilio fornece um conjunto de bibliotecas do .NET auxiliar que encapsular vários aspectos de Twilio para fornecer maneiras simples e fáceis de interagir com o API REST de Twilio e Twilio cliente para gerar respostas de TwiML.

Twilio fornece cinco bibliotecas para desenvolvedores .NET:
<table border="1">
    <tr>
        <th>Biblioteca</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>Twilio.API</td>
        <td>A biblioteca de Twilio de núcleo que encapsula a API REST Twilio em uma biblioteca .NET amigável. Essa biblioteca está disponível para o Windows Phone 7, Silverlight e .NET.</td>
    </tr>
    <tr>
        <td>Twilio.TwiML</td>
        <td>Fornece uma maneira amigável do .NET para gerar marcação de TwiML.</td>
    </tr>
    <tr>
        <td>Twilio.MVC</td>
        <td>Para os desenvolvedores que usam o ASP.NET MVC, esta biblioteca inclui um TwilioController, o TwiML ActionResult e o atributo de validação de solicitação.</td>
    </tr>
    <tr>
        <td>Twilio.WebMatrix</td>
        <td>Para os desenvolvedores usando a ferramenta de desenvolvimento do WebMatrix gratuita da Microsoft, esta biblioteca contém auxiliares de sintaxe do Razor para várias ações de Twilio.</td>
    </tr>
    <tr>
        <td>Twilio.Client.Capability</td>
        <td>Contém o gerador de token de capacidade para uso com o SDK do Twilio cliente JavaScript.</td>
    </tr>
</table>

Observe que todas as bibliotecas requerem o .NET 3.5, Silverlight 4 ou Windows Phone 7 ou posterior.

Os exemplos fornecidos neste guia usam a biblioteca Twilio.API.

As bibliotecas podem ser [instaladas usando a extensão de Gerenciador de pacote do NuGet](http://www.twilio.com/docs/csharp/install) disponíveis para o Visual Studio 2010 e 2012. O código-fonte estiver hospedado em [GitHub][twilio_github_repo], que inclui um Wiki que contém a documentação completa para usar as bibliotecas.

Por padrão, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. As bibliotecas de Twilio a instalação requer a versão 1.6 do NuGet ou superior. Para obter informações sobre como instalar ou atualizar o NuGet, consulte [http://nuget.org/][nuget].

<div class="dev-callout">
<b>Observação</b>
<p>Para instalar a versão mais recente do NuGet, primeiro desinstale a versão carregada usando o Gerenciador de extensões do Visual Studio. Para fazer isso, você deve executar o Visual Studio como administrador. Caso contrário, o botão Desinstalar é desativado.</p>
</div>

<h3><a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto do Visual Studio:</h3>

1.  Abra sua solução no Visual Studio.
2.   Clique com o botão direito do mouse em **Referências**.
3.   Clique em **gerenciar pacotes do NuGet...**
4.   Clique em **Online**.
5.  Na caixa Pesquisar Online, digite *twilio*.
6.   Clique em **Instalar** no pacote Twilio.


<h2><a id="howto_make_call"></a>Como: Fazer uma chamada externa</h2>
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

Para obter mais informações sobre os parâmetros passados para o método **client.InitiateOutboundCall**, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como Fornecer as respostas TwiML de seu próprio site](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS</h2>
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

<h2><a id="howto_provide_twiml_responses"></a>Como: Fornecer as respostas TwiML por meio de seu próprio site</h2>
Quando o aplicativo inicia uma chamada para a API do Twilio - por exemplo, por meio do método **cliente.InitiateOutboundCall**, o Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo em [Como: Fazer uma chamada externa](#howto_make_call) usa a URL fornecida pelo Twilio [http://twimlets.com/message][twimlet_message_url] para retornar a resposta. 

<div class="dev-callout">
<b>Observação</b>
<p>Enquanto TwiML destina-se ao uso pelos serviços da web, você pode exibir o TwiML no seu navegador. Por exemplo, clique em [http://twimlets.com/message](twimlet_message_url) para ver um elemento &lt;Response&gt; vazio; para ver outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world) para ver um elemento &lt;Response&gt; que contém um elemento &lt;Say&gt;.</p>
</div>

Em vez de confiar na URL fornecida pelo Twilio, você pode criar seu próprio site na URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP. Este tópico pressupõe que você hospedará a URL de um manipulador genérico do ASP.NET.

O seguinte manipulador de ASP.NET monta uma resposta TwiML que diz **Hello World** na chamada.

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

Como você pode ver no exemplo acima, a resposta TwiML é simplesmente um documento XML. A biblioteca do Twilio.TwiML contém classes que irão gerar TwiML para você. O exemplo a seguir produz a resposta equivalente como mostrado acima, mas usa a classe TwilioResponse.

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

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Depois que você configurar uma forma de fornecer respostas TwiML, você pode passar essa URL para o método **client.InitiateOutboundCall**. Por exemplo, se você tiver um aplicativo Web chamado MyTwiML implantado em um serviço de nuvem do Azure e o nome de seu manipulador do ASP.NET for mytwiml.ashx, o URL pode ser passado para **cliente.InitiateOutboundCall** conforme mostrado no exemplo de código a seguir:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);


Para obter informações adicionais sobre como usar Twilio no Azure com o ASP.NET, consulte [como fazer uma chamada telefônica usando o Twilio em uma função web no Azure][howto_phonecall_dotnet].

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]





[howto_phonecall_dotnet]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/



[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/pt-br/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp



[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
