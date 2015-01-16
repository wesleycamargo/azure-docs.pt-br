<properties urlDisplayName="SendGrid Email Service" pageTitle="Como usar o serviço de email SendGrid (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em c# e usam a API .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="erikre" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/24/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />





# Como enviar emails usando o SendGrid com o Azure

Última atualização: 24 de outubro de 2014

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Azure. Os exemplos são escritos em código C\# e utilizam a API .NET. Os cenários abordados incluem **escrever email**, **enviar email** e **adicionar anexos** e **usar filtros**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a seção [Próximas etapas][].

<h2><a name="toc"></a>Sumário</h2>

[O que é o serviço de email SendGrid?][]   
[Criar uma conta do SendGrid][]   
[Referência à biblioteca de classes do .NET do SendGrid][]   
[Como: Criar um email][]   
[Como: Enviar um email][]   
[Como: Adicionar um anexo][]   
[Como: Usar filtros para habilitar rodapés, acompanhamento e análise][]   
[Como: Usar serviços adicionais do SendGrid][]   
[Próximas etapas][]

<h2><a name="whatis"></a>O que é o serviço de email SendGrid?</h2>

SendGrid é um [serviço de email baseado em nuvem] que fornece
[entrega de email transacional confiável], escalabilidade e análise em tempo real, junto com APIs flexíveis
que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid
incluem:

-   Envio automático de recibos para os clientes.
-   Listas de distribuição de administração para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais.
-   Coleta de métricas em tempo real para email bloqueado e
    capacidade de resposta do cliente, por exemplo.
-   Geração de relatórios para ajudar a identificar as tendências.
-   Encaminhamento de consultas dos clientes.

Para obter mais informações, consulte [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Criar uma conta do SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Referência à biblioteca de classes do .NET do SendGrid</h2>

O [pacote NuGet do SendGrid](https://www.nuget.org/packages/Sendgrid) é a maneira mais fácil de obter a API do SendGrid e configurar seu aplicativo com todas as dependências. O NuGet é uma extensão do Visual Studio incluído com o Microsoft Visual Studio 2012 que facilita a instalação e a atualização de bibliotecas e ferramentas. 

<div class="dev-callout">
<b>Observação</b>
<p>Para
instalar o NuGet se você estiver executando uma versão do Visual Studio anterior ao Visual Studio 2012, visite <a href="http://www.nuget.org">http://www.nuget.org</a> e clique em <b>Instalar
O botão NuGet</b>.</p>
</div>

Para instalar o pacote NuGet do SendGrid no seu aplicativo, faça o seguinte:

1.  No **Gerenciador de Soluções**, clique com o botão direito em **Referências** e, em seguida, clique em
    **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo da caixa de diálogo **Gerenciar Pacotes NuGet**, clique em **Online**.

3.  Procure **SendGrid** e selecione o item **SendGrid** na
    lista de resultados.

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Clique em **Instalar** para concluir a instalação e, em seguida, feche esta
    caixa de diálogo.

A biblioteca de classes do .NET do SendGrid é denominada **SendGridMail**. Ela contém
os namespaces a seguir:

-   **SendGridMail** para criar e trabalhar com itens de email.
-   **SendGridMail.Transport** para enviar email usando o protocolo
    **SMTP** ou o protocolo HTTP 1.1 com **Web/REST**.

Adicione as declarações de namespace de código a seguir à parte superior de qualquer arquivo C\# em que queira acessar o serviço de email SendGrid de forma programática. **System.Net** e **System.Net.Mail** são namespaces do .NET Framework incluídos porque incluem tipos que você usará comumente com as APIs SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Como: Criar um email</h2>

Use o método **SendGrid.GetInstance** estático para criar uma mensagem de email do tipo **SendGrid**. Quando a mensagem for criada, você poderá usar as propriedades e os métodos do **SendGrid** para definir valores incluindo o remetente de email, o destinatário de email e o assunto e o corpo do email.

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

Para obter mais informações sobre todas as propriedades e métodos com suporte do tipo **SendGrid**, consulte [sendgrid-csharp][] no GitHub.

<h2><a name="sendemail"></a>Como: Enviar um email</h2>

Após criar uma mensagem de email, você poderá enviá-la usando a API da Web fornecida pelo SendGrid. Alternativamente, você pode usar a compilação do .NET na biblioteca](https://sendgrid.com/docs/Code_Examples/csharp.html).

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

The following examples show how to send a message using the Web API.

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

<h2><a name="addattachment"></a>Como: Adicionar um anexo</h2>

É possível adicionar anexos a uma mensagem, chamando o método **AddAttachment** e especificando o nome e o caminho do arquivo que você deseja anexar. Você pode incluir vários anexos, chamando esse método uma vez para cada arquivo que quiser anexar. O exemplo a seguir demonstra como adicionar um anexo a uma mensagem:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Você também pode adicionar anexos por meio do **Fluxo**. de dados. Isso pode ser feito ao chamar o mesmo método acima, **AddAttachment**, mas ao passar no Stream dos dados, e o nome do arquivo que você quer mostrar na mensagem.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

<h2><a name="usefilters"></a>Como: Usar filtros para habilitar rodapés, rastreamento e análise</h2>

O SendGrid fornece a funcionalidade adicional de email usando filtros. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar acompanhamento de clique, Googleanalytics, acompanhamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte[Configurações de filtro][].

É possível aplicar filtros a mensagens de email do **SendGrid** usando os métodos implementados como parte da classe **SendGrid**.

Os exemplos a seguir demonstram os filtros de rodapé e de acompanhamento de cliques:

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

<h2><a name="useservices"></a>Como: Usar serviços adicionais do SendGrid</h2>

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][].

<h2><a name="nextsteps"></a>Próximas etapas</h2>

Agora que você já conhece os princípios do serviço de email do SendGrid, acesse estes links para saber mais.

* Repositório da biblioteca C\# do SendGrid: [sendgrid-csharp][]
*   Documentação da API do SendGrid: <https://sendgrid.com/docs>
*   Oferta especial do SendGrid para clientes do Azure: [https://sendgrid.com](https://sendgrid.com)

  [Próximas etapas]: #nextsteps
  [O que é o serviço de email SendGrid?]: #whatis
  [Criar uma conta do SendGrid]: #createaccount
  [Referência à biblioteca de classes do .NET do SendGrid]: #reference
  [Como: Criar um email]: #createemail
  [Como: Enviar um email]: #sendemail
  [Como: Adicionar um anexo]: #addattachment
  [Como: Usar filtros para habilitar rodapés, rastreamento e análise]: #usefilters
  [Como: Usar serviços adicionais do SendGrid]: #useservices
  
  
  [oferta especial]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
