<properties
	pageTitle="Como usar o Twilio for Voice e SMS (.NET) | Microsoft Azure"
	description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Amostras de código gravadas no .NET."
	services=""
	documentationCenter=".net"
	authors="devinrader"
	manager="twilio"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="devinrader"/>

# Como usar o Twilio para funcionalidades de voz e SMS do Azure

Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps).

## <a id="WhatIs"></a>O que é Twilio?
Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

## <a id="Pricing"></a>Preços e ofertas especiais da Twilio
Os clientes do Azure recebem uma [oferta especial](http://www.twilio.com/azure): US$ 10 de cortesia de crédito da Twilio quando atualizam a sua conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito da Twilio e faça uma introdução em [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][twilio_libraries].

Os principais aspectos da API do Twilio são os verbos Twilio e a TwiML (Linguagem de Marcação do Twilio).

### <a id="Verbs"></a>Verbos da Twilio
A API usa os verbos da Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui a Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio ](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: Conecta o chamador a outro telefone.
* **&lt;Gather&gt;**: Coleta os dígitos numéricos inseridos no teclado numérico do telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Play&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
* **&lt;Reject&gt;**: Descarta uma chamada recebida para o número da Twilio sem cobrar você
* **&lt;Say&gt;**: Converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o **TwiMLResponse** objeto.

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta na Twilio
Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta da Twilio][twilio_account], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <a id="create_app"></a>Criar um aplicativo Azure
Um aplicativo do Azure que hospeda um aplicativo Twilio habilitado não é diferente de qualquer outro aplicativo do Azure. Adicione a biblioteca .NET do Twilio e configure a função para usar as bibliotecas .NET do Twilio. Para saber mais sobre como criar um projeto inicial do Azure, confira [Criando um projeto do Azure com o Visual Studio][vs_project].

## <a id="configure_app"></a>Configurar seu aplicativo para usar as bibliotecas do Twilio
Twilio fornece um conjunto de bibliotecas do .NET auxiliar que encapsular vários aspectos de Twilio para fornecer maneiras simples e fáceis de interagir com o API REST de Twilio e Twilio cliente para gerar respostas de TwiML.

Twilio fornece cinco bibliotecas para desenvolvedores .NET:
Biblioteca|Descrição
---|---
Twilio.API|A biblioteca de Twilio de núcleo que encapsula a API REST Twilio em uma biblioteca .NET amigável. Essa biblioteca está disponível para o .NET, Silverlight e Windows Phone 7.
Twilio.TwiML|Fornece uma maneira amigável do .NET para gerar marcação de TwiML.
Twilio.MVC|Para os desenvolvedores que usam o ASP.NET MVC, esta biblioteca inclui um TwilioController, o TwiML ActionResult e o atributo de validação de solicitação.
Twilio.WebMatrix|Para os desenvolvedores usando a ferramenta de desenvolvimento do WebMatrix gratuita da Microsoft, esta biblioteca contém auxiliares de sintaxe do Razor para várias ações de Twilio.
Twilio.Client.Capability|Contém o gerador de token de capacidade para uso com o SDK do Twilio cliente JavaScript.

Observe que todas as bibliotecas requerem o .NET 3.5, Silverlight 4 ou Windows Phone 7 ou posterior.

Os exemplos fornecidos neste guia usam a biblioteca Twilio.API.

As bibliotecas podem ser [instaladas usando a extensão do gerenciador de pacotes NuGet](http://www.twilio.com/docs/csharp/install) disponíveis para o Visual Studio 2010 e 2012. O código-fonte é hospedado no [GitHub][twilio_github_repo], que inclui um Wiki que contém a documentação completa para usar as bibliotecas.

Por padrão, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. As bibliotecas de Twilio a instalação requer a versão 1.6 do NuGet ou superior. Para saber mais sobre como instalar ou atualizar o NuGet, confira [http://nuget.org/][nuget].

> [AZURE.NOTE] Para instalar a versão mais recente do NuGet, primeiro desinstale a versão carregada usando o Gerenciador de extensões do Visual Studio. Para fazer isso, você deve executar o Visual Studio como administrador. Caso contrário, o botão Desinstalar é desabilitado.

### <a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto do Visual Studio:

1.  Abra sua solução no Visual Studio.
2.  Clique com o botão direito do mouse em **Referências**.
3.  Clique em **Gerenciar Pacotes NuGet...**
4.  Clique em **Online**.
5.  Na caixa Pesquisar Online, digite *tiwlio*.
6.  Clique em **Instalar** no pacote Twilio.


## <a id="howto_make_call"></a>Como fazer uma chamada externa
Abaixo é mostrado como fazer uma chamada externa usando a classe **TwilioRestClient**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta TwiML (Linguagem de Marcação do Twilio). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

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

Para saber mais sobre os parâmetros passados para o método **client.InitiateOutboundCall**, confira [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para mais informações, consulte [Como fornecer respostas TwiML de seu próprio site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como enviar uma mensagem de SMS
A seguinte captura de tela mostra como enviar uma mensagem de SMS usando a classe **TwilioRestClient**. O **de** número é fornecido por Twilio para contas de avaliação para enviar mensagens SMS. O **para** número deve ser verificado para sua conta de Twilio antes de executar o código.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        Message result = client.SendMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

## <a id="howto_provide_twiml_responses"></a>Como fornecer respostas TwiML de seu próprio site
Quando o aplicativo inicia uma chamada para a API Twilio - por exemplo, por meio de **cliente.InitiateOutboundCall** método - Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo em [Como fazer uma chamada de saída](#howto_make_call) usa a URL fornecida pelo Twilio [http://twimlets.com/message][twimlet_message_url] para retornar a resposta.

> [AZURE.NOTE] Enquanto TwiML destina-se ao uso pelos serviços Web, é possível exibir o TwiML no seu navegador. Por exemplo, clique em [http://twimlets.com/message](twimlet_message_url) para ver um elemento &lt;Response&gt; vazio; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world) para ver um elemento &lt;Response&gt; que contém um elemento &lt;Say&gt;.

Em vez de contar com a URL fornecida pela Twilio, você pode criar seu próprio site URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP. Este tópico pressupõe que você irá hospedando a URL de um manipulador genérico do ASP.NET.

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

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Depois que você configurar uma forma de fornecer respostas TwiML, você pode passar essa URL para o **cliente.InitiateOutboundCall** método. Por exemplo, se você tiver um aplicativo da web chamado MyTwiML implantado em um serviço de nuvem do Azure e o nome de seu manipulador do ASP.NET for mytwiml.ashx, o URL pode ser passado para **cliente.InitiateOutboundCall** conforme mostrado no exemplo de código a seguir:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);


Para obter informações adicionais sobre como usar Twilio no Azure com o ASP.NET, consulte [como fazer uma chamada telefônica usando Twilio em uma função Web no Azure][howto_phonecall_dotnet].

[AZURE.INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]





[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md



[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]: http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]: http://nuget.org/
[twilio_github_repo]: https://github.com/twilio/twilio-csharp



[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

<!---HONumber=AcomDC_0413_2016-->