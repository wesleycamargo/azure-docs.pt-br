<properties linkid="dev-java-how-to-access-control" urlDisplayName="Serviço de email SendGrid" pageTitle="Como usar o serviço de email SendGrid (Java) - Azure" metaKeywords="SendGrid para Azure, serviço de email do Azure, Java de SendGrid do Azure, Java para email do Azure" description="Saiba como enviar emails com o serviço de email SendGrid no Azure. Exemplos de código escritos em Java." metaCanonical="" services="" documentationCenter="Java" title="Como enviar emails usando o SendGrid com Java" authors="waltpo" solutions="" manager="" editor="mollybos" />





# Como enviar emails usando o SendGrid com Java

Este guia demonstra como executar tarefas comuns de programação com o
serviço de e-mail SendGrid no Azure. Os exemplos estão escritos em Java. Os cenários abordados incluem a **construção de emails**, o **envio de emails**, a **adição de anexos**, o **uso de filtros** e a **atualização de propriedades**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a seção [Próximas etapas][].

## Sumário

-   [O que é o serviço de email SendGrid?][]
-   [Criar uma conta do SendGrid][]
-   [Como usar as bibliotecas javax.mail][]
-   [Como criar um email][]
-   [Como enviar um email][]
-   [Como adicionar um anexo][]
-   [Como usar filtros para permitir rodapés, controles e análises][]
-   [Como atualizar as propriedades do email][]
-   [Como usar serviços adicionais do SendGrid][]
-   [Próximas etapas][]

## <a name="bkmk_WhatIsSendGrid"> </a>O que é o Serviço de E-mail do SendGrid?

O SendGrid é um [serviço de email com base na nuvem] que oferece [entrega de email transacional], escalabilidade e análise em tempo real confiáveis juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid
incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais
-   Coleta de métricas em tempo real para e-mail bloqueado e
    capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar tendências
-   Encaminhamento de consultas dos clientes
- Notificações por email de seu aplicativo

Para obter mais informações, consulte <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Criar uma conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Como usar as bibliotecas javax.mail

Obtenha as bibliotecas javax.mail, por exemplo, em <http://www.oracle.com/technetwork/java/javamail> e importe-as para o seu código. Em nível elevado, o processo para usar a biblioteca javax.mail para enviar emails usando SMTP é para fazer o seguinte:

1.  Especificar os valores de SMTP, incluindo o servidor SMTP, que, para SendGrid, é smtp.sendgrid.net.

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
           	  // â€¦

2.  Estender a classe <span class="auto-style1">javax.mail.Authenticator</span>
    e, na implementação do método
    <span class="auto-style1">getPasswordAuthentication</span>,
    retornar seu nome de usuário e sua senha do SendGrid.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Criar uma sessão de email autenticada por meio de um
    objeto <span class="auto-style1">javax.mail.Session</span>.  

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Criar sua mensagem e atribuir os valores de **Para**, **De**, **Assunto** e conteúdo. Isso é mostrado na seção [Como criar um email](#bkmk_HowToCreateEmail).
5.  Enviar a mensagem por meio de um objeto
    <span class="auto-style1">javax.mail.Transport</span>. Isso é mostrado na seção [Como enviar um email][How to: Send an Email]
    .

## <a name="bkmk_HowToCreateEmail"> </a>Como criar um email

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

## <a name="bkmk_HowToSendEmail"> </a>Como enviar um email

O código a seguir mostra como enviar um email.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Como adicionar um anexo

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

## <a name="bkmk_HowToUseFilters"> </a>Como usar filtros para permitir rodapés, controles e análises

O SendGrid fornece a funcionalidade adicional de email por meio do uso de
*filtros*. Essas são as configurações que podem ser adicionadas a uma mensagem de e-mail para habilitar uma funcionalidade específica, como habilitar rastreamento de clique, Google analytics, rastreamento de assinatura e assim por diante. Para obter a lista completa de filtros,
consulte [Configurações do filtro][].

-   O código a seguir mostra como inserir um filtro de rodapés que faça com que o texto HTML exibido na parte inferior do email seja enviado.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Outro exemplo de um filtro é o rastreamento de cliques. Vamos dizer que o texto de seu email contém um hiperlink, como o seguinte, e você deseja acompanhar a taxa de cliques:

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   Para habilitar o rastreamento de cliques, use o código a seguir:

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Como atualizar as propriedades do email

Algumas propriedades de e-mail podem ser substituídas usando **definir*propriedade*** ou anexadas usando **adicionar*propriedade***.

Por exemplo, para especificar endereços para **ReplyTo**, use o seguinte:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Para adicionar um destinatário a **Cc**, use o seguinte:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Como usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][].

## <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você já conhece as noções básicas do serviço de email SendGrid, acesse estes links para saber mais.

* Exemplo que demonstra o uso do SendGrid em uma implantação do Azure: [Como enviar emails usando o SendGrid com Java em uma implantação do Azure (a página pode estar em inglês)](/pt-br/develop/java/how-to-guides/sendgrid-sample/)
* Informações de Java do SendGrid: <http://sendgrid.com/docs/Code_Examples/java.html>
* Documentação da API do SendGrid: <http://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial do SendGrid para clientes do Azure: <http://sendgrid.com/azure.html>

  [Próximas etapas]: #bkmk_NextSteps
  [O que é o serviço de email SendGrid?]: #bkmk_WhatIsSendGrid
  [Criar uma conta do SendGrid]: #bkmk_CreateSendGridAcct
  [Como usar as bibliotecas javax.mail]: #bkmk_HowToUseJavax
  [Como criar um email]: #bkmk_HowToCreateEmail
  [Como enviar um email]: #bkmk_HowToSendEmail
  [Como adicionar um anexo]: #bkmk_HowToAddAttachment
  [Como usar filtros para permitir rodapés, controles e análises]: #bkmk_HowToUseFilters
  [Como atualizar as propriedades do email]: #bkmk_HowToUpdateEmail
  [Como usar serviços adicionais do SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: http://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: http://www.sendgrid.com/azure.html
  [http://sendgrid.com/features]: http://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Configurações do filtro]: http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [Documentação da API do SendGrid]: http://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: http://sendgrid.com/azure.html
  [serviço de email com base na nuvem]: http://sendgrid.com/solutions
  [entrega de email transacional]: http://sendgrid.com/transactional-email

