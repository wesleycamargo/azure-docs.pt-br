<properties urlDisplayName="SendGrid Email Service" pageTitle="Como usar o serviço de email SendGrid (Java) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Java, Azure email Java" description="Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Como enviar emails usando o SendGrid com Java

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Windows Azure. As amostras são gravadas em Java. Os cenários abordados incluem a **construção de e-mails**, o **envio de emails**, a **adição de anexos**, o **uso de filtros** e a**atualização de propriedades**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a seção [Próximas etapas][].

## Sumário

-   [O que é o serviço de email SendGrid?][]
-   [Criar uma conta do SendGrid][]
-   [Como: Use as bibliotecas javax.mail][]
-   [Como: Criar um email][]
-   [Como: Enviar um email][]
-   [Como: Adicionar um anexo][]
-   [Como: Usar filtros para habilitar rodapés, rastreamento e análise][]
-   [Como: Atualizar as propriedades do email][]
-   [Como: Usar serviços adicionais do SendGrid][]
-   [Próximas etapas][]

## <a name="bkmk_WhatIsSendGrid"> </a>O que é o serviço de email SendGrid?

O SendGrid é um [serviço de e-mail baseado em nuvem] que oferece [entrega de e-mail transacional], escalabilidade e análise em tempo real confiáveis com APIsflexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente panfletos eletrônicos e ofertas especiais
-   Coleta de métrica em tempo real para, por exemplo, email bloqueado e capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
- Notificações de email a partir de seu aplicativo

Para obter mais informações, consulte <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Criar uma conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Como: Use as bibliotecas javax.mail

Obtenha as bibliotecas javax.mail, por exemplo, a partir de <http://www.oracle.com/technetwork/java/javamail> e importe-as ao seu código. Em um alto nível, o processo de usar a biblioteca javax.mail para enviar emails usando SMTP é fazer o seguinte:

1.  Especifique os valores de SMTP, incluindo o servidor SMTP, que, para
    SendGrid, é smtp.sendgrid.net.
    
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
	       private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
	       private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
        
		   public static void main(String[] args) throws Exception{
         	  new MyEmailer().SendMail();
           }
        
		   public void SendMail() throws Exception
           {
              Properties properties = new Properties();
           	  properties.put("mail.transport.protocol", "smtp");
           	  properties.put("mail.smtp.host", SMTP_HOST_NAME);
           	  properties.put("mail.smtp.port", 587);
           	  properties.put("mail.smtp.auth", "true");
           	  // ...

2.  Estender a classe <span class="auto-style1">javax.mail.Authenticator</span>     e na implementação do método     <span class="auto-style1">getPasswordAuthentication</span> método,     retornar seu nome de usuário e senha do SendGrid.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Criar uma sessão de email autenticada por meio de um objeto
    <span class="auto-style1">javax.mail.Session</span> .  

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Criar sua mensagem e atribuir os valores de **Para**, **De**, **Assunto** e conteúdo. Isso é mostrado na seção [Como: Criar um email](#bkmk_HowToCreateEmail) .
5.  Enviar a mensagem por meio de um objeto     <span class="auto-style1">javax.mail.Transport</span> . Isso     é mostrado na seção [Como: Enviar um email][How to: Send an Email]     .

## <a name="bkmk_HowToCreateEmail"> </a>Como: Criar um email

O código a seguir mostra como especificar valores para um email.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
		"<p>Hello,</p>
		<p>Your Contoso order has <b>shipped</b>.</p>
		<p>Thank you,<br>John</br></p>",
		"text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="bkmk_HowToSendEmail"> </a>Como: Enviar um email

O código a seguir mostra como enviar um email.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Como: Adicionar um anexo

O código a seguir mostra como adicionar um anexo.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>Como: Usar filtros para habilitar rodapés, rastreamento e análise

O SendGrid fornece a funcionalidade adicional de email por meio do uso de *filtros*. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar rastreamento de cliques, Google analytics, rastreamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte [Configurações de filtro][].

-   A seguir é mostrado como inserir um filtro de rodapé que resulta em
    um texto HTML que aparece na parte inferior do email sendo enviado.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Outro exemplo de um filtro é o rastreamento de cliques. Digamos que o texto do     email contém um hiperlink, como o seguinte, e você deseja     acompanhar a taxa de cliques:

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   To enable the click tracking, use the following code:

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Como: Atualizar as propriedades do email

Algumas propriedades de email podem ser substituídas usando **set*Property*** ou acrescentadas usando **add*Property***.

Por exemplo, para especificar endereços para **ReplyTo**, use o seguinte:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Para adicionar um destinatário a **Cc**, use o seguinte:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Como: Usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na web que você pode usar para aproveitar
a funcionalidade adicional do SendGrid do aplicativo Azure. Para obter mais
detalhes, consulte a [documentação da API do SendGrid][].

## <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você já conhece os princípios do serviço de email do SendGrid, acesse
estes links para obter mais informações.

* Exemplo que demonstra o uso do SendGrid em uma implantação do Azure: [Como enviar email usando o SendGrid do Java em uma implantação do Azure](../store-sendgrid-java-how-to-send-email-example/)
* SDK do Java do SendGrid: <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentação da API do SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial do SendGrid para clientes do Azure: <https://sendgrid.com/windowsazure.html>

  [Próximas etapas]: #bkmk_NextSteps
  [O que é o serviço de email SendGrid?]: #bkmk_WhatIsSendGrid
  [Criar uma conta do SendGrid]: #bkmk_CreateSendGridAcct
  [Como: Use as bibliotecas javax.mail]: #bkmk_HowToUseJavax
  [Como: Criar um email]: #bkmk_HowToCreateEmail
  [Como: Enviar um email]: #bkmk_HowToSendEmail
  [Como: Adicionar um anexo]: #bkmk_HowToAddAttachment
  [Como: Usar filtros para habilitar rodapés, rastreamento e análise]: #bkmk_HowToUseFilters
  [Como: Atualizar as propriedades do email]: #bkmk_HowToUpdateEmail
  [Como: Usar serviços adicionais do SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Configurações de filtro]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [Documentação da API do SendGrid]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email

<!--HONumber=35.2-->
