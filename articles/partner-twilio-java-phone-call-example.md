<properties linkid="develop-java-how-to-twilio-phone-call" urlDisplayName="How to Make a Phone Call from Twilio in Java" pageTitle="How to Make a phone call from Twilio (Java) - Azure" metaKeywords="Azure Twilio call, Twilio call website, Azure Twilio Java" description="Learn how to make a phone call from a web page using Twilio in a Java application on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to Make a Phone Call Using Twilio in a Java Application on Azure" authors="MicrosoftHelp@twilio.com; robmcm" videoId="" scriptId="" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Como fazer uma chamada telefônica usando a Twilio em um aplicativo Java no Azure

O exemplo a seguir mostra como você pode usar a Twilio para fazer uma chamada de uma página da web hospedada no Azure. O aplicativo resultante solicitará valores de chamada telefônica ao usuário, conforme mostrado na seguinte captura de tela.

![Formulário de chamada do Azure usando a Twilio e o Java][Formulário de chamada do Azure usando a Twilio e o Java]

Você precisará fazer o seguinte para usar o código deste tópico:

1.  Obter uma conta e um token de autenticação da Twilio. Para uma introdução à Twilio, avalie os preços em [][]<http://www.twilio.com/pricing></a>. Você pode se inscrever em [][1]<https://www.twilio.com/try-twilio></a>. Para obter informações sobre a API fornecida pela Twilio, consulte [][2]<http://www.twilio.com/api></a>.
2.  Verifique seu número de telefone como uma ID de chamador de saída com a Twilio. Para obter informações sobre como verificar o número do seu telefone, consulte [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Como alternativa ao uso de um número existente, você pode adquirir um número de telefone da Twilio.
    Para o objetivo deste exemplo, use o número de telefone verificado como o valor de **From** do callform.jsp (descrito posteriormente).
3.  Obter o JAR da Twilio. Em [][4]<https://github.com/twilio/twilio-java></a>, você pode baixar as fontes GitHub e criar seu próprio JAR ou baixar um JAR predefinido (com ou sem dependências).
    O código deste tópico foi escrito usando o JAR TwilioJava 3.3.8 pré-criado com dependências.
4.  Adicione o JAR a seu caminho de compilação de Java.
5.  Se estiver usando o Eclipse para criar esse aplicativo Java, inclua o JAR da Twilio em seu arquivo de implantação de aplicativo (WAR) usando o recurso do assembly de implantação do Eclipse. Se não estiver usando o Eclipse para criar esse aplicativo Java, verifique se o JAR da Twilio está incluído na mesma função do Azure que seu aplicativo Java e adicionado ao caminho de classe de seu aplicativo.
6.  Verifique se seu keystore de cacerts contém o certificado da Equifax Secure Certificate Authority com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Esse é o certificado da CA (autoridade de certificação) do serviço [][5]<https://api.twilio.com></a>, que é chamado quando você usa APIs da Twilio. Para obter informações sobre como adicionar esse certificado da CA a seu repositório de cacerts de JDK, consulte [Adicionando um certificado ao repositório de certificados da CA do Java][Adicionando um certificado ao repositório de certificados da CA do Java].

Além disso, familiaridade com as informações em [Criando um aplicativo Hello World usando o plug-in do Azure para Eclipse com o Java (da Microsoft Open Technologies)][Criando um aplicativo Hello World usando o plug-in do Azure para Eclipse com o Java (da Microsoft Open Technologies)] ou com outras técnicas de hospedagem de aplicativos Java no Azure, se você não estiver usando o Eclipse, é altamente recomendável.

## Criar um formulário da web para fazer uma chamada

O código a seguir mostra como criar um formulário da web para recuperar dados do usuário para fazer uma chamada. Para o objetivo deste exemplo, um novo projeto web dinâmico, chamado **TwilioCloud**, foi criado, e **callform.jsp** foi adicionado como um arquivo JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## Como criar o código para fazer a chamada

O código a seguir, que é chamado quando o usuário preenche o formulário mostrado por callform.jsp, cria a mensagem de chamada e gera a chamada. Para o objetivo deste exemplo, o arquivo JSP é chamado **makecall.jsp** e foi adicionado ao projeto **TwilioCloud**. (Use sua conta e o token de autenticação da Twilio em vez dos valores de espaço reservado atribuídos a **accountSID** e **authToken** no código a seguir.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Além de fazer a chamada, o makecall.jsp exibe o ponto de extremidade da Twilio, a versão da API e o status da chamada. A captura de tela a seguir é um exemplo:

![Resposta de chamada do Azure usando a Twilio e o Java][Resposta de chamada do Azure usando a Twilio e o Java]

## Executar o aplicativo

As seguintes são as etapas de alto nível para executar seu aplicativo. Os detalhes dessas etapas podem ser localizados em [Criando um aplicativo Hello World usando o plug-in do Azure para Eclipse com o Java (da Microsoft Open Technologies)][Criando um aplicativo Hello World usando o plug-in do Azure para Eclipse com o Java (da Microsoft Open Technologies)].

1.  Exporte o WAR TwilioCloud para a pasta **approot** do Azure.
2.  Modifique o **startup.cmd** para descompactar seu WAR TwilioCloud.
3.  Compile o aplicativo para o emulador de computação.
4.  Inicie a implantação no emulador de computação.
5.  Abra um navegador, e execute o **<http://localhost:8080/TwilioCloud/callform.jsp>**.
6.  Insira os valores no formulário, clique em **Fazer esta chamada** e, em seguida, veja os resultados em makecall.jsp.

Quando estiver pronto para implantar no Azure, recompile para implantação na nuvem, implante no Azure e execute http://[your\_hosted\_name][your\_hosted\_name]*.cloudapp.net/TwilioCloud/callform.jsp no navegador (substitua seu valor por* your\_hosted\_name\*).

## Próximas etapas

Esse código foi fornecido para mostrar a funcionalidade básica usando a Twilio no Java no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erros ou outros recursos. Por exemplo:

-   Em vez de usar um formulário da web, você pode usar os blobs de armazenamento ou o Banco de Dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter informações sobre como usar os blobs de armazenamento do Azure no Java, consulte [Como usar o serviço de armazenamento de blob do Java][Como usar o serviço de armazenamento de blob do Java]. Para obter informações sobre como usar o Banco de Dados SQL no Java, consulte [Usando o Banco de Dados SQL no Java][Usando o Banco de Dados SQL no Java].
-   Você pode usar **RoleEnvironment.getConfigurationSettings** para recuperar a ID da conta e o token de autenticação da Twilio nas definições da configuração da implantação, em vez de embutir valores no código no makecall.jsp. Para obter informações sobre a classe **RoleEnvironment**, consulte [Usando a Biblioteca de Tempo de Execução de Serviço do Azure no JSP][Usando a Biblioteca de Tempo de Execução de Serviço do Azure no JSP] e a documentação do pacote de Tempo de Execução de Serviço do Azure em [][6]<http://dl.windowsazure.com/javadoc></a>.
-   O código makecall.jsp atribui uma URL fornecida pela Twilio, [][7]<http://twimlets.com/message></a>, à variável **Url**. Esta URL fornece uma resposta da linguagem TwiML que informa a Twilio como proceder com a chamada. Por exemplo, a TwiML que é retornada pode conter um verbo **\<Say\>** que resulta em texto que está sendo falado para o destinatário da chamada. Em vez de usar a URL fornecida pela Twilio, você pode criar seu próprio serviço para responder à solicitação da Twilio. Para obter mais informações, consulte [Como usar a Twilio para obter recursos de voz e SMS no Java][Como usar a Twilio para obter recursos de voz e SMS no Java]. Mais informações sobre a TwiML podem ser encontradas em [][8]<http://www.twilio.com/docs/api/twiml></a> e mais informações sobre **\<Say\>** e outros verbos da Twilio podem ser encontradas em [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Leia as diretrizes de segurança da Twilio em [][10]<https://www.twilio.com/docs/security></a>.

Para obter informações adicionais sobre a Twilio, consulte [][11]<https://www.twilio.com/docs></a>.

## Consulte também

-   [Como usar a Twilio para obter recursos de voz e SMS no Java][Como usar a Twilio para obter recursos de voz e SMS no Java]
-   [Adicionando um certificado ao repositório de certificados da CA do Java][Adicionando um certificado ao repositório de certificados da CA do Java]

  [Formulário de chamada do Azure usando a Twilio e o Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: http://github.com/twilio/twilio-java
  [5]: http://api.twilio.com
  [Adicionando um certificado ao repositório de certificados da CA do Java]: ../java-add-certificate-ca-store
  [Resposta de chamada do Azure usando a Twilio e o Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
  [Como usar o serviço de armazenamento de blob do Java]: http://www.windowsazure.com/pt-br/develop/java/how-to-guides/blob-storage/
  [Usando o Banco de Dados SQL no Java]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh749029.aspx
  [Usando a Biblioteca de Tempo de Execução de Serviço do Azure no JSP]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh690948.aspx
  [6]: http://dl.windowsazure.com/javadoc
  [7]: http://twimlets.com/message
  [Como usar a Twilio para obter recursos de voz e SMS no Java]: ../partner-twilio-java-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
