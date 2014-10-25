<properties linkid="dev-java-how-to-access-control" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Java) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Java, Azure email Java" description="Learn how send email with the SendGrid email service on Azure. Code samples written in Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Como enviar emails usando o SendGrid com Java

Este guia demonstra como executar tarefas comuns de programação com o serviço de
email SendGrid no Windows Azure. As amostras são gravadas em
Java. Os cenários abordados incluem a **construção de emails**, o**envio de
emails**, a **adição de anexos**, o **uso de filtros** e a **atualização de
propriedades**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte
a seção [Próximas etapas][].

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

O SendGrid é um [serviço de email baseado em nuvem][] que oferece
[entrega de email transacional][], escalabilidade e análise em tempo real confiáveis com APIs flexíveis
que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid
incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais
-   Coleta de métrica em tempo real para, por exemplo, email bloqueado e
    capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
-   Notificações de email a partir de seu aplicativo

Para obter mais informações, consulte <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a> Criar uma conta de SendGrid

[WACOM.INCLUDE [sendgrid-sign-up][]]

## <a name="bkmk_HowToUseJavax"> </a>Como: Use as bibliotecas javax.mail

Obtenha as bibliotecas javax.mail, por exemplo, em
<http://www.oracle.com/technetwork/java/javamail> e importe-as para o
seu código. Em nível elevado, o processo para usar a biblioteca javax.mail
para enviar emails usando SMTP é para fazer o seguinte:

1.  Especificar os valores de SMTP, incluindo o servidor SMTP, que, para
    SendGrid, é smtp.sendgrid.net.

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
              // …

2.  Extender a
    classe <span class="auto-style1">javax.mail.Authenticator</span> e, na implementação do método
    <span class="auto-style1">getPasswordAuthentication</span>,
     retornar seu nome de usuário e sua senha do SendGrid.

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Criar uma sessão de email autenticada por meio de um objeto
    <span class="auto-style1">javax.mail.Session</span>.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Criar sua mensagem e atribuir os valores de **Para**, **De**, **Assunto** e
    conteúdo. Isso é mostrado na seção [Como: Criar um email][].
5.  Enviar a mensagem por meio de um objeto
    <span class="auto-style1">javax.mail.Transport</span>. Isso é
    mostrado na seção [Como: Enviar um email][]
    .

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
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
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

O SendGrid fornece a funcionalidade adicional de email por meio do uso de
*filtros*. Essas são as configurações que podem ser adicionadas a uma mensagem de email para
habilitar uma funcionalidade específica, como habilitar rastreamento de clique, Google
analytics, rastreamento de assinatura e assim por diante. Para obter uma lista completa de filtros,
consulte[Configurações de filtro][].

-   O código a seguir mostra como inserir um filtro de rodapés que faça com que o texto
    HTML exibido na parte inferior do email seja enviado.

        message.addHeader("X-SMTPAPI", 
            "{\"filters\": 
            {\"footer\": 
            {\"settings\": 
            {\"enable\":1,\"text/html\": 
            \"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Outro exemplo de um filtro é o rastreamento de cliques. Vamos dizer que o
    texto de seu email contém um hiperlink, como o seguinte, e você deseja
    acompanhar a taxa de cliques:

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

## <a name="bkmk_HowToUpdateEmail"> </a>Como: Atualizar as propriedades do email

Algumas propriedades de e-mail podem ser substituídas usando **definir*propriedade*** ou
anexadas usando **adicionar*propriedade***.

Por exemplo, para especificar endereços para **ReplyTo**, use o seguinte:

    InternetAddress addresses[] = 
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Para adicionar um destinatário a **Cc**, use o seguinte:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Como: Usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a
funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter
detalhes completos, consulte a [documentação da API do SendGrid][].

## <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você já conhece os princípios do serviço de email do SendGrid, acesse
estes links para saber mais.

-   Exemplo que demonstra o uso do SendGrid em uma implantação do Azure: [Como enviar email usando o SendGrid do Java em uma implantação do Azure][]
-   Informações de Java do SendGrid: <http://sendgrid.com/docs/Code_Examples/java.html>
-   Documentação da API do SendGrid: <http://sendgrid.com/docs/API_Reference/index.html>
-   Oferta especial do SendGrid para clientes do Azure: <http://sendgrid.com/azure.html>

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
  [serviço de email baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de email transacional]: http://sendgrid.com/transactional-email
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [Configurações de filtro]: http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [documentação da API do SendGrid]: http://sendgrid.com/docs/API_Reference/index.html
  [Como enviar email usando o SendGrid do Java em uma implantação do Azure]: ../store-sendgrid-java-how-to-send-email-example/
