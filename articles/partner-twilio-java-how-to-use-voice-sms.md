<properties 
	pageTitle="Como usar o Twilio para voz e SMS (Java) | Microsoft Azure" 
	description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de códigos escritos em Java." 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Como usar a Twilio para obter recursos de voz e SMS no Java

Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps).

## <a id="WhatIs"></a>O que é Twilio?
Twilio é uma API do serviço Web de telefonia que permite usar os idiomas e as habilidades existentes de Web para criar aplicativos de voz e SMS. O Twilio é um serviço de terceiro (não é um recurso do Azure e não é um produto da Microsoft).

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos façam e recebam mensagens SMS. **Twilio Cliente** permite que seus aplicativos habilitem a comunicação de voz usando as conexões existentes com a Internet, incluindo conexões para celular.

## <a id="Pricing"></a>Preços e ofertas especiais da Twilio
A informações sobre os preços do Twilio estão disponíveis em [Preços do Twilio][twilio_pricing]. Os clientes do Azure recebem uma [oferta especial][special_offer]\: um crédito de 1000 mensagens de texto gratuitas ou 1000 minutos de entrada. Para se inscrever nessa oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][twilio_libraries].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <a id="Verbs"></a>Verbos da Twilio
A API usa os verbos da Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui a Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio.

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

## <a id="create_app"></a>Criar um aplicativo Java
1. Obtenha o JAR do Twilio e adicione-o ao seu caminho de compilação do Java e seu assembly de implantação WAR. Em [https://github.com/twilio/twilio-java][twilio_java], você pode baixar as fontes GitHub e criar seu próprio JAR ou baixar um JAR predefinido (com ou sem dependências).
2. Certifique-se de que o armazenamento de chaves **cacerts** do JDK contém o certificado de Autoridade de Certificação Segura Equifax com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Esse é o certificado da CA (autoridade de certificação) do serviço [https://api.twilio.com][twilio_api_service], que é chamado quando você usa APIs da Twilio. Para obter informações sobre como garantir que seu armazenamento de chaves **cacerts** de JDK contém o certificado de autoridade de certificação correto, consulte [Adicionando um certificado ao repositório de certificados de autoridade de certificação do Java][add_ca_cert].

Instruções detalhadas para usar a biblioteca de cliente do Twilio para Java estão disponíveis em [Como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas Twilio
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

## <a id="howto_make_call"></a>Como fazer uma chamada externa
Abaixo é mostrado como fazer uma chamada externa usando a classe **CallFactory**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta TwiML (Linguagem de Marcação do Twilio). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

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

Para obter mais informações sobre os parâmetros passados para o método **CallFactory.create**, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta de TwiML; para obter mais informações, consulte [Como fornecer respostas de TwiML em um aplicativo Java no Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como enviar uma mensagem de SMS
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
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Para obter mais informações sobre os parâmetros passados para o método **SmsFactory.create**, consulte [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Como fornecer respostas TwiML de seu próprio site
Quando o aplicativo iniciar uma chamada para a API do Twilio, por exemplo, por meio do método **CallFactory.create**, o Twilio enviará a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL fornecida pela Twilio [http://twimlets.com/message][twimlet_message_url]. (O TwiML destina-se ao uso pelos serviços Web, mas você pode exibir o TwiML no seu navegador. Por exemplo, clique em [http://twimlets.com/message][twimlet_message_url] para ver um elemento **&lt;Response&gt;** vazio; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um elemento **&lt;Response&gt;** que contém um elemento **&lt;Say&gt;**.)

Em vez de contar com a URL fornecida pela Twilio, você pode criar seu próprio site URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP; este tópico pressupõe que você hospedará a URL em uma página JSP.

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

O parâmetro **ApiVersion** está disponível nas solicitações de voz do Twilio (não nas solicitações de SMS). Para ver os parâmetros de solicitação disponíveis para solicitações de SMS e voz do Twilio, consulte <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivamente. A variável de ambiente **RoleName** está disponível como parte de uma implantação do Azure. (Se você deseja adicionar variáveis de ambiente personalizadas para que elas possam ser detectadas de **System.getenv**, consulte a seção variáveis de ambiente em [Diversos parâmetros de configuração de função][misc_role_config_settings].)

Após você definir a página JSP para fornecer respostas TwiML, use a URL da página JSP conforme a URL passada para o método **CallFactory.create**. Por exemplo, se você tiver um aplicativo da Web chamado MyTwiML implantado em um serviço hospedado do Azure e o nome da página JSP for mytwiml.jsp, a URL poderá ser passada para **CallFactory.create** conforme mostrado a seguir:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Outra opção para responder com TwiML é por meio da classe **TwiMLResponse**, que está disponível no pacote **com.twilio.sdk.verbs**.

Para obter informações adicionais sobre como usar Twilio no Azure com Java, consulte [Como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Como Usar os Serviços Adicionais do Twilio
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Próximas etapas
Agora que você já conhece os princípios do serviço Twilio, acesse estes links para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Código de Exemplo e Procedimentos do Twilio][twilio_howtos]
* [Tutoriais do Guia de início rápido do Twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o suporte do Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!---HONumber=August15_HO7-->