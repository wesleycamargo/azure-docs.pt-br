<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Como usar a Twilio para obter recursos de voz e SMS no Java

Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é Twilio?][O que é Twilio?]
-   [Preços do Twilio][Preços do Twilio]
-   [Conceitos][Conceitos]
-   [Criar uma conta no Twilio][Criar uma conta no Twilio]
-   [Verificar números telefônicos][Verificar números telefônicos]
-   [Criar um aplicativo Java][Criar um aplicativo Java]
-   [Configurar seu aplicativo para usar as bibliotecas do Twilio][Configurar seu aplicativo para usar as bibliotecas do Twilio]
-   [Como: Fazer uma chamada externa][Como: Fazer uma chamada externa]
-   [Como: Enviar uma mensagem SMS][Como: Enviar uma mensagem SMS]
-   [Como: Fornecer as respostas TwiML de seu próprio site][Como: Fornecer as respostas TwiML de seu próprio site]
-   [Como: Use os serviços adicionais do Twilio][Como: Use os serviços adicionais do Twilio]
-   [Próximas etapas][Próximas etapas]

## <span id="WhatIs"></span></a>O que é Twilio?

Twilio é uma API do serviço Web de telefonia que permite usar os idiomas e habilidades existentes de Web para criar aplicativos de voz e de SMS. O Twilio é um serviço de terceiro (não é um recurso do Azure e não é um produto da Microsoft).

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos façam e recebam mensagens SMS. **Twilio Cliente** permite que seus aplicativos habilitem a comunicação de voz usando as conexões existentes com a Internet, incluindo conexões para celular.

## <span id="Pricing"></span></a>Preços e ofertas especiais do Twilio

A informações sobre os preços do Twilio estão disponíveis em [Preços do Twilio][1]. Os clientes do Azure recebem uma [oferta especial][oferta especial]: um crédito de 1000 mensagens de texto gratuitas e 1000 minutos de entrada. Para se inscrever nessa oferta ou obter mais informações, visite [][oferta especial]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Conceitos

A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][Bibliotecas de API da Twilio].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <span id="Verbs"></span></a>Verbos de Twilio

A API usa os verbos do Twilio; por exemplo, o verbo **\<Say\>** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio.

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

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][TwiML]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][API da Twilio].

## <span id="CreateAccount"></span></a>Criar uma conta no Twilio

Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][Avaliar a Twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta da Twilio][página da conta da Twilio], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <span id="VerifyPhoneNumbers"></span></a>Verificar números telefônicos

Vários números de telefone devem ser verificados com Twilio para a sua conta. Por exemplo, se você quiser fazer chamadas telefônicas, o número de telefone deve ser verificado como uma identificação de chamador saída com Twilio. Da mesma forma, se você desejar um número de telefone para receber mensagens SMS, o número de telefone de recebimento deve ser verificado com Twilio. Para obter informações sobre como verificar um número de telefone, consulte [Gerenciar números][Gerenciar números]. Parte do código abaixo se baseia em números de telefone que você precisará verificar com Twilio.

Como uma alternativa para usar um número existente para seus aplicativos, você pode adquirir um número de telefone Twilio. Para obter informações sobre como adquirir um número de telefone do Twilio, consulte [Ajuda para números de telefone do Twilio][Ajuda para números de telefone do Twilio].

## <span id="create_app"></span></a>Criar um aplicativo Java

1.  Obtenha o JAR do Twilio e adicione-o ao seu caminho de compilação do Java e seu conjunto de implantação WAR. Em [][]<https://github.com/twilio/twilio-java></a>, você pode baixar as fontes GitHub e criar seu próprio JAR ou baixar um JAR predefinido (com ou sem dependências).
2.  Certifique-se de que o armazenamento de chaves **cacerts** do JDK contém o certificado de Autoridade de Certificação Segura Equifax com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Esse é o certificado da CA (autoridade de certificação) do serviço [][2]<https://api.twilio.com></a>, que é chamado quando você usa APIs da Twilio. Para obter informações sobre como garantir que seu armazenamento de chaves **cacerts** de JDK contém o certificado de autoridade de certificação correto, consulte [Adicionando um certificado ao repositório de certificados de autoridade de certificação do Java][Adicionando um certificado ao repositório de certificados de autoridade de certificação do Java].

Instruções detalhadas para usar a biblioteca de cliente do Twilio para Java estão disponíveis em [Como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure][Como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure].

## <span id="configure_app"></span></a>Configurar seu aplicativo para usar as bibliotecas do Twilio

Dentro de seu código, você pode adicionar instruções de **importação** na parte superior dos seus arquivos de origem dos pacotes ou classes do Twilio que você deseja usar em seu aplicativo.

Para arquivos de origem Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Para arquivos de origem Java Server Page (JSP):

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

Dependendo dos pacotes ou classes do Twilio que você deseja usar, as instruções de **importação** podem ser diferentes.

## <span id="howto_make_call"></span></a>Como: Fazer uma chamada externa

Abaixo é mostrado como fazer uma chamada externa usando a classe **CallFactory**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta da linguagem de marcação do Twilio (TwiML). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Para obter mais informações sobre os parâmetros passados para o método **CallFactory.create**, consulte [][3]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta de TwiML; para obter mais informações, consulte [Como fornecer respostas de TwiML em um aplicativo Java no Azure][Como: Fornecer as respostas TwiML de seu próprio site].

## <span id="howto_send_sms"></span></a>Como: Enviar uma mensagem SMS

Abaixo é mostrado como enviar uma mensagem SMS usando a classe **SmsFactory**. O número **De**, **4155992671**, é fornecido por Twilio para contas de avaliação para enviar mensagens SMS. O número **Para** deve ser verificado para sua conta de Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

Para obter mais informações sobre os parâmetros passados para o método **SmsFactory**, consulte [][4]<http://www.twilio.com/docs/api/rest/sending-sms></a>.

## <span id="howto_provide_twiml_responses"></span></a>Como: Fornecer as respostas TwiML de seu próprio site

Quando o aplicativo iniciar uma chamada para a API do Twilio, por exemplo, por meio do método **CallFactory.create**, o Twilio enviará a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL fornecida pela Twilio [][5]<http://twimlets.com/message></a>. (Enquanto TwiML destina-se ao uso pelos serviços da Web, você pode exibir o TwiML no seu navegador. Por exemplo, clique em [][5]<http://twimlets.com/message></a> para ver um elemento **\<Response\>** vazio; como outro exemplo, clique em [][6]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> para ver um elemento **\<Response\>** que contém um **\<Say\>**.)

Em vez de confiar na URL fornecida pela Twilio, você pode criar seu próprio site na URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP; este tópico pressupõe que você hospedará a URL em uma página JSP.

A página JSP seguinte resulta em uma resposta de TwiML que diz **Hello World** na chamada.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

A página JSP seguinte resulta em uma resposta de TwiML que diz algum texto, tem várias pausas e diz informações sobre a versão de API do Twilio e o nome da função do Azure.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

O parâmetro **ApiVersion** está disponível nas solicitações de voz do Twilio (não nas solicitações de SMS). Para ver os parâmetros de solicitação disponíveis para solicitações de SMS e voz do Twilio, consulte <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivamente. A variável de ambiente **RoleName** está disponível como parte de uma implantação do Azure. (Se você deseja adicionar variáveis de ambiente personalizadas para que elas possam ser detectadas de **System.getenv**, consulte a seção variáveis de ambiente em [Diversos parâmetros de configuração de função][Diversos parâmetros de configuração de função].)

Após você definir a página JSP para fornecer respostas TwiML, use a URL da página JSP conforme a URL passada para o método **CallFactory.create**. Por exemplo, se você tiver um aplicativo da Web chamado MyTwiML implantado em um serviço hospedado do Azure e o nome da página JSP for mytwiml.jsp, a URL poderá ser passada para **CallFactory.create** conforme mostrado a seguir:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Outra opção para responder com TwiML é por meio da classe **TwiMLResponse**, que está disponível no pacote **com.twilio.sdk.verbs**.

Para obter informações adicionais sobre como usar Twilio no Azure com Java, consulte [Como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure][Como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure].

## <span id="AdditionalServices"></span></a>Como: Use os serviços adicionais do Twilio

Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio][API da Twilio].

## <span id="NextSteps"></span></a>Próximas etapas

Agora que você já conhece os princípios do serviço Twilio, acesse estes links para saber mais:

-   [Diretrizes da segurança do Twilio][Diretrizes da segurança do Twilio]
-   [Instruções e código de exemplo da Twilio][Instruções e código de exemplo da Twilio]
-   [Tutoriais de início rápido do Twilio][Tutoriais de início rápido do Twilio]
-   [Twilio no GitHub][Twilio no GitHub]
-   [Fale com o suporte do Twilio][Fale com o suporte do Twilio]

  [Próximas etapas]: #NextSteps
  [Preços do Twilio]: #Pricing
  [Conceitos]: #Concepts
  [Criar uma conta no Twilio]: #CreateAccount
  [Verificar números telefônicos]: #VerifyPhoneNumbers
  [Criar um aplicativo Java]: #create_app
  [Configurar seu aplicativo para usar as bibliotecas do Twilio]: #configure_app
  [Como: Fazer uma chamada externa]: #howto_make_call
  [Como: Enviar uma mensagem SMS]: #howto_send_sms
  [Como: Fornecer as respostas TwiML de seu próprio site]: #howto_provide_twiml_responses
  [Como: Use os serviços adicionais do Twilio]: #AdditionalServices
  [1]: http://www.twilio.com/pricing
  [oferta especial]: http://ahoy.twilio.com/azure
  [Bibliotecas de API da Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API da Twilio]: http://www.twilio.com/api
  [Avaliar a Twilio]: https://www.twilio.com/try-twilio
  [página da conta da Twilio]: https://www.twilio.com/user/account
  [Gerenciar números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Ajuda para números de telefone do Twilio]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [2]: https://api.twilio.com
  [Adicionando um certificado ao repositório de certificados de autoridade de certificação do Java]: ../java-add-certificate-ca-store
  [Como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure]: ../partner-twilio-java-phone-call-example
  [3]: http://www.twilio.com/docs/api/rest/making-calls
  [4]: http://www.twilio.com/docs/api/rest/sending-sms
  [5]: http://twimlets.com/message
  [6]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [Diversos parâmetros de configuração de função]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh690945.aspx
  [Diretrizes da segurança do Twilio]: http://www.twilio.com/docs/security
  [Instruções e código de exemplo da Twilio]: http://www.twilio.com/docs/howto
  [Tutoriais de início rápido do Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio no GitHub]: https://github.com/twilio
  [Fale com o suporte do Twilio]: http://www.twilio.com/help/contact
