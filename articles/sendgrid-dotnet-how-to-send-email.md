<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="carolz" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"></tags>

# Como enviar emails usando o SendGrid com o Azure

Última atualização: 21 de agosto de 2014

Este guia demonstra como executar tarefas comuns de programação com o serviço de e-mail SendGrid no Windows Azure. Os exemplos são escritos em código C# e utilizam a API .NET. Os cenários abordados incluem **cosntrução de email**, **envio de email**, **adição de anexos** e o **uso de filtros**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte aseção [Próximas etapas][Próximas etapas].

## <a name="toc"></a>Sumário

[O que é o Serviço de E-mail SendGrid?][O que é o Serviço de E-mail SendGrid?]
[Criar uma conta de SendGrid][Criar uma conta de SendGrid]
[Referência à biblioteca de classe SendGrid .NET][Referência à biblioteca de classe SendGrid .NET]
[Como: Criar um email][Como: Criar um email]
[Como: Enviar um email][Como: Enviar um email]
[Como: Adicionar um anexo][Como: Adicionar um anexo]
[Como: Usar filtros para habilitar rodapés, rastreamento e análise][Como: Usar filtros para habilitar rodapés, rastreamento e análise]
[Como: Usar serviços adicionais do SendGrid][Como: Usar serviços adicionais do SendGrid]
[Próximas etapas][Próximas etapas]

## <a name="whatis"></a><span class="short-header">O que é o serviço de email SendGrid?</span>

O SendGrid é um [serviço de email baseado em nuvem][serviço de email baseado em nuvem] que oferece [entrega de email transacional][entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

-   Envio automático de recibos para os clientes.
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais.
-   Coleta de métrica em tempo real para, por exemplo, email bloqueado e
    capacidade de resposta do cliente.
-   Geração de relatórios para ajudar a identificar as tendências.
-   Encaminhamento de consultas dos clientes.

Para obter mais informações, consulte [][]<http://sendgrid.com></a>.

## <a name="createaccount"></a><span class="short-header">Criar uma conta do SendGrid</span>

[WACOM.INCLUDE [sendgrid-sign-up][sendgrid-sign-up]]

## <a name="reference"></a><span class="short-header">Referência à biblioteca de classes do .NET do SendGrid</span>Referência à biblioteca de classes do .NET SendGrid

O [pacote NuGet do SendGrid][pacote NuGet do SendGrid] é a maneira mais fácil de obter a API do SendGrid e configurar seu aplicativo com todas as dependências. O NuGet é uma extensão do Visual Studio incluído com o Microsoft Visual Studio 2012 que facilita a instalação e a atualização de bibliotecas e ferramentas.

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>Para
instalar o NuGet se voc&ecirc; estiver executando uma vers&atilde;o do Visual Studio anterior ao Visual Studio 2012, visite <a href="http://www.nuget.org">http://www.nuget.org</a> e clique em <b>Instalar
o NuGet</b>.</p>
</div>

Para instalar o pacote NuGet do SendGrid no seu aplicativo, faça o seguinte:

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e, em seguida, clique em
    **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo da caixa de diálogo **Gerenciar Pacotes NuGet**, clique em **Online**.

3.  Procure **SendGrid** e selecione o item **SendGrid** na
    lista de resultados.

    ![Pacote NuGet do SendGrid][Pacote NuGet do SendGrid]

4.  Clique em **Instalar** para concluir ainstalação e, em seguida, feche essa caixa de diálogo.

A biblioteca de classes do .NET do SendGrid é denominada **SendGridMail**. Ele contém
os seguintes namespaces:

-   **SendGridMail** para criar e trabalhar com itens de email.
-   **SendGridMail.Transport** para enviar email usando o protocolo
    **SMTP** ou o protocolo HTTP 1.1 com **Web/REST**.

Adicione as declarações de namespace de código a seguir à parte superior de qualquer arquivo C# em que queira acessar o serviço de email SendGrid de forma programática. O **System.Net** e o **System.Net.Mail** são namespaces do .NET Framework que estão incluídos pois eles incluem tipos que você comumente usa com as APIs do SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="createemail"></a><span class="short-header">Como: Criar um email</span>Como: Criar um email

Use o método **SendGrid.GetInstance** estático para criar uma mensagem de email do tipo **SendGrid**. Quando a mensagem for criada, você poderá usar as propriedades e os métodos do **SendGrid** para definir valores incluindo o remetente do email, o destinatário do email e o assunto e o corpo do email.

O exemplo a seguir demonstra como criar um objeto de email totalmente preenchido:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Para obter mais informações sobre todas as propriedades e métodos com suporte do tipo **SendGrid**, consulte [sendgrid-csharp][sendgrid-csharp] no GitHub.

## <a name="sendemail"></a><span class="short-header">Como: Enviar um email</span>

Após criar uma mensagem de email, você poderá enviá-la usando o API da Web fornecida pelo SendGrid. Alternativamente, você pode [usar a compilação do .NET na biblioteca][usar a compilação do .NET na biblioteca].

O envio de email requer que você forneça suas credenciais de conta do SendGrid (nome de usuário e senha). O código a seguir demonstra como encapsular suas credenciais em um objeto **NetworkCredential**:

    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

Os exemplos a seguir mostram como enviar umamensagem usando a API da Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

## <a name="addattachment"></a><span class="short-header">Como: Adicionar um anexo</span>Como: Adicionar um anexo

É possível adicionar anexos a uma mensagem, chamando o método **AddAttachment** e especificando o nome e o caminho do arquivo que você deseja anexar. Você pode incluir vários anexos, chamando esse método uma vez para cada arquivo que quiser anexar. O exemplo a seguir demonstra como adicionar um anexo a uma mensagem:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Você também pode adicionar anexos a partir do **Stream** de dados. Isso pode ser feito ao chamar o mesmo método acima, **AddAttachment**, mas ao passar no Stream dos dados, e o nome do arquivo que você quer mostrar na mensagem.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

## <a name="usefilters"></a><span class="short-header">Como: Usar filtros para habilitar rodapés, rastreamento e análise</span>

O SendGrid fornece a funcionalidade adicional de e-mail por meio do uso de filtros. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar rastreamento de clique, Google analytics, rastreamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte [Configurações de filtro][Configurações de filtro].

É possível aplicar filtros a mensagens de email do **SendGrid** usando os métodos implementados como parte da classe do **SendGrid**.

Os exemplos a seguir demonstram os filtros de rodapé e de rastreamento de cliques:

### Rodapé

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Rastreamento de cliques

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="useservices"></a><span class="short-header">Como: Usar serviços adicionais do SendGrid</span>

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][documentação da API do SendGrid].

## <a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas

Agora que você já conhece os princípios do serviço de email do SendGrid, acesse estes links para saber mais.

-   Repositório da biblioteca C# do SendGrid: [sendgrid-csharp][sendgrid-csharp]
-   Documentação da API do SendGrid: <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial do SendGrid para clientes do Azure: [][]<http://sendgrid.com></a>

  [Próximas etapas]: #nextsteps
  [O que é o Serviço de E-mail SendGrid?]: #whatis
  [Criar uma conta de SendGrid]: #createaccount
  [Referência à biblioteca de classe SendGrid .NET]: #reference
  [Como: Criar um email]: #createemail
  [Como: Enviar um email]: #sendemail
  [Como: Adicionar um anexo]: #addattachment
  [Como: Usar filtros para habilitar rodapés, rastreamento e análise]: #usefilters
  [Como: Usar serviços adicionais do SendGrid]: #useservices
  [serviço de email baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de email transacional]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [pacote NuGet do SendGrid]: https://www.nuget.org/packages/Sendgrid
  [Pacote NuGet do SendGrid]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [usar a compilação do .NET na biblioteca]: https://sendgrid.com/docs/Code_Examples/csharp.html
  [Configurações de filtro]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [documentação da API do SendGrid]: http://docs.sendgrid.com/documentation/api/
