<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="Serviço de email SendGrid" pageTitle="Como usar o serviço de email SendGrid (. NET) - Azure" metaKeywords="SendGrid do Azure, serviço de email do Azure, .NET do SendGrid do Azure, .NET de email do Azure, C# do SendGrid do Azure, C# de email do Azure" description="Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em C# e que usam a API do .NET." metaCanonical="" services="" documentationCenter=".NET" title="Como enviar email usando o SendGrid com o Azure" authors=""  solutions="" writer="" manager="" editor=""  />





# Como enviar emails usando o SendGrid com o Azure

Este guia demonstra como executar tarefas comuns de programação com o
serviço de email SendGrid no Azure. As amostras são escritas em C\#
e usam a API do .NET. Os cenários abordados incluem **construção de
email**, **envio de email**, **adição de anexos** e **uso de
filtros**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a
seção [Próximas etapas][].

<h2><a name="toc"></a>Sumário</h2>

[O que é o serviço de email SendGrid?][]   
[Criar uma conta do SendGrid][]   
[Referência à biblioteca de classes do .NET do SendGrid][]   
[Como: criar um email][]   
[Como: enviar um email][]   
[Como: adicionar um anexo][]   
[Como: usar filtros para habilitar rodapés, rastreamento e análise][]   
[Como: usar serviços adicionais do SendGrid][]   
[Próximas etapas][]

<h2><a name="whatis"></a><span  class="short-header">O que é o serviço de email SendGrid?</span>o que é o serviço de email SendGrid?</h2>

O SendGrid é um [serviço de email baseado em nuvem] que oferece
[entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs
flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid
incluem:

-   Envio automático de recibos para os clientes.
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais.
-   Coleta de métricas em tempo real para email bloqueado e
    capacidade de resposta do cliente, por exemplo.
-   Geração de relatórios para ajudar a identificar tendências.
-   Encaminhamento de consultas dos clientes.

Para obter mais informações, consulte [http://sendgrid.com](http://sendgrid.com).

<h2><a name="createaccount"></a><span  class="short-header">Criar uma conta do SendGrid</span>Criar uma conta do SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a><span  class="short-header">Referência à biblioteca de classes do .NET do SendGrid</span>Referência à biblioteca de classes do .NET SendGrid</h2>

O pacote NuGet do SendGrid é a maneira mais fácil
de obter a API do SendGrid e configurar seu aplicativo com todas as dependências. O NuGet é uma
extensão do Visual Studio incluído com o Microsoft Visual Studio 2012 que facilita a instalação e a atualização
de bibliotecas e ferramentas. 

<div class="dev-callout">
<b>Observação</b>
<p>Para
instalar o NuGet se você estiver executando uma versão do Visual Studio anterior ao Visual Studio 2012, visite <a href="http://www.nuget.org">http://www.nuget.org</a> e clique no botão <b>Instalar
o NuGet</b>.</p>
</div>

Para instalar o pacote NuGet do SendGrid no seu aplicativo, faça o seguinte:

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e, em seguida, clique em
    **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo da caixa de diálogo **Gerenciar Pacotes NuGet**, clique em **Online**.

3.  Procure **SendGrid** e selecione o item **SendGrid**
na lista de resultados.

    ![Pacote NuGet do SendGrid][SendGrid-NuGet-package]

4.  Clique em **Instalar** para concluir a
instalação e, em seguida, feche essa
    caixa de diálogo.

A biblioteca de classes do .NET do SendGrid é denominada **SendGridMail**. Ela contém
os seguintes namespaces:

-   **SendGridMail** para criar e trabalhar com itens de email.
-   **SendGridMail.Transport** para enviar email usando o protocolo
    **SMTP** ou o protocolo HTTP 1.1 com **Web/REST**.

Adicione as declarações de namespace de código a seguir à parte superior de qualquer arquivo C\#
em que queira acessar o serviço de email SendGrid de forma programática.
**System.Net** e **System.Net.Mail** são namespaces do .NET Framework
incluídos, pois conterem tipos que você usará com frequência com as APIs do SendGrid.

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

<h2><a name="createemail"></a><span  class="short-header">Como: criar um email</span>Como: criar um email</h2>

Use o método **SendGrid.GetInstance** estático para criar uma mensagem de email
do tipo **SendGrid**. Quando a mensagem for criada, você
poderá usar as propriedades e os métodos do **SendGrid** para definir valores incluindo o
remetente do email, o destinatário do email e o assunto e o corpo do
email.

O exemplo a seguir demonstra como criar um objeto de email
totalmente preenchido:

    // Configure as propriedades de email.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // Crie um email, passando as oito propriedades como argumentos.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

O exemplo a seguir demonstra como criar um objeto de email vazio:

    // Crie o objeto de email primeiro e, em seguida, adicione as propriedades.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Adicione as propriedades da mensagem.
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // Adicione vários endereços ao campo Para.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // Adicione um corpo de mensagem no formato HTML.
    myMessage.Html = "<p>Hello World!</p>";

    // Adiciona o assunto.
    myMessage.Subject = "Testing the SendGrid Library";

Para obter mais informações sobre todas as propriedades e métodos com suporte do tipo
**SendGrid**, consulte [sendgrid-csharp][] no GitHub.

<h2><a name="sendemail"></a><span  class="short-header">Como: enviar um email</span>Como: enviar um email</h2>

Após criar uma mensagem de email, você poderá enviá-la usando SMTP ou a API Web fornecida pelo SendGrid. Para obter detalhes sobre os benefícios e
os problemas de cada API, consulte [SMTP x API Web][] na documentação do
SendGrid.

O envio de email com um dos protocolos requer que você forneça suas
credenciais de conta do SendGrid (nome de usuário e senha). O código a seguir
demonstra como encapsular suas credenciais em um objeto **NetworkCredential**:

    // Crie credenciais de rede para acessar sua conta do SendGrid.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

Para enviar uma mensagem de email, use o método **Deliver** na classe
**SMTP**, que usa o protocolo SMTP, ou na classe **REST**, que chama a API Web do SendGrid. Os exemplos a seguir mostram como
enviar uma mensagem usando SMTP e a API Web.

### SMTP

    // Crie o objeto de email primeiro e, em seguida, adicione as propriedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Crie credenciais, especificando seu nome de usuário e a senha.
    var credentials = new NetworkCredential("username", "password");

    // Crie um transporte SMTP para enviar email.
    var transportSMTP = SMTP.GetInstance(credentials);

    // Envie o email.
    transportSMTP.Deliver(myMessage);

### API da Web

    // Crie o objeto de email primeiro e, em seguida, adicione as propriedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Crie credenciais, especificando seu nome de usuário e a senha.
    var credentials = new NetworkCredential("username", "password");

    // Crie um transporte REST para enviar email.
    var transportREST = Web.GetInstance(credentials);

    // Envie o email.
    transportREST.Deliver(myMessage);

<h2><a name="addattachment"></a><span  class="short-header">Como: adicionar um anexo</span>Como: adicionar um anexo</h2>

É possível adicionar anexos a uma mensagem, chamando o método **AddAttachment**
e especificando o nome e o caminho do arquivo que você deseja anexar.
Você pode incluir vários anexos, chamando esse método uma vez para
cada arquivo que quiser anexar. O exemplo a seguir demonstra como adicionar
um anexo a uma mensagem:

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

<h2><a name="usefilters"></a><span  class="short-header">Como: usar filtros para habilitar rodapés, rastreamento e análise</span>Como: usar filtros para habilitar rodapés, rastreamento e análise</h2>

O SendGrid fornece uma funcionalidade de email adicional com o uso de filtros. Estas são as configurações que podem ser adicionadas a uma mensagem de email para
habilitar uma funcionalidade específica, como rastreamento de cliques, análise do Google, acompanhamento de assinatura etc. Para obter uma lista completa de filtros, consulte
[Configurações de filtro][].

É possível aplicar filtros a mensagens de email do **SendGrid** usando os métodos
implementados como parte da classe do **SendGrid**. Antes de habilitar filtros em uma mensagem de email, você deve primeiro inicializar a lista de
filtros disponíveis, chamando o método **InitalizeFilters**.

Os exemplos a seguir demonstram os filtros de rodapé e de
rastreamento de cliques:

### Rodapé

    // Crie o objeto de email primeiro e, em seguida, adicione as propriedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Adicione um rodapé à mensagem.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Rastreamento de cliques

    // Crie o objeto de email primeiro e, em seguida, adicione as propriedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // true indica que os links nas partes do email de texto sem formatação
    // também devem ser substituídos para fins de acompanhamento de links. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a><span  class="short-header">Como: usar serviços adicionais do SendGrid</span>Como: usar serviços adicionais do SendGrid</h2>

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][].

<h2><a name="nextsteps"></a><span  class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora que você já conhece as noções básicas do serviço de email SendGrid, siga
estes links para saber mais.

* Repositório de biblioteca C\# do SendGrid: [sendgrid-csharp][]
*   Documentação da API do SendGrid: <http://docs.sendgrid.com/documentation/api/>
*   Oferta especial do SendGrid para clientes do Azure: [http://sendgrid.com](http://sendgrid.com)

  [Próximas etapas]: #nextsteps
  [O que é o serviço de email SendGrid?]: #whatis
  [Criar uma conta do SendGrid]: #createaccount
  [Referência à biblioteca de classes do .NET do SendGrid]: #reference
  [Como: criar um email]: #createemail
  [Como: enviar um email]: #sendemail
  [Como: adicionar um anexo]: #addattachment
  [Como: usar filtros para habilitar rodapés, rastreamento e análise]: #usefilters
  [Como: usar serviços adicionais do SendGrid]: #useservices
  
  
  [oferta especial]: http://www.sendgrid.com/azure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP x API Web]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [Configurações de filtro]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [Documentação da API do SendGrid]: http://docs.sendgrid.com/documentation/api/
  
  [serviço de email baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de email transacional]: http://sendgrid.com/transactional-email

