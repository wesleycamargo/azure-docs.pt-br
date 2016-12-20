---
title: "Como usar o serviço de email SendGrid (.NET) | Microsoft Docs"
description: "Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em c# e usam a API .NET."
services: app-service\web
documentationcenter: .net
author: thinkingserious
manager: dwrede
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/14/2016
ms.author: team-pi@sendgrid.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae05e89346fe6bb85408cd560505dab3c03837cc


---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Como enviar emails usando o SendGrid com o Azure
## <a name="overview"></a>Visão geral
Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Windows Azure. Os exemplos são escritos em C\#
e usam a API .NET. Os cenários abordados incluem **construir o email**, **enviar o email**, **adicionar anexos** e **usar filtros**. Para obter mais informações sobre o SendGrid e o envio de email, consulte a seção [Próximas etapas][Próximas etapas].

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de email SendGrid?
SendGrid é um [serviço de email baseado em nuvem] que fornece uma [entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

* Envio automático de recibos para os clientes.
* Administração de listas de distribuição para envio mensal de panfletos eletrônicos e ofertas especiais aos clientes.
* Coleta de métricas em tempo real para, por exemplo, email bloqueado e capacidade de resposta do cliente.
* Geração de relatórios para ajudar a identificar as tendências.
* Encaminhamento de consultas dos clientes.
* Processamento de emails de entrada.

Para obter mais informações, consulte [https://sendgrid.com](https://sendgrid.com) ou a nossa [biblioteca C#][sendgrid-csharp]

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referência à biblioteca de classes do .NET do SendGrid
O [pacote NuGet do SendGrid](https://www.nuget.org/packages/Sendgrid) é a maneira mais fácil de obter a API do SendGrid e para configurar seu aplicativo com todas as dependências. O NuGet é uma extensão do Visual Studio incluída no Microsoft Visual Studio 2015, que facilita a instalação e a atualização de bibliotecas e ferramentas. 

> [!NOTE]
> Para instalar o NuGet se você estiver executando uma versão do Visual Studio anterior ao Visual Studio 2015, visite [http://www.nuget.org](http://www.nuget.org)e clique no botão **Instalar NuGet** .
> 
> 

Para instalar o pacote NuGet do SendGrid no seu aplicativo, faça o seguinte:

1. Crie um novo projeto.
   
   ![Criar um novo projeto][create-new-project]
2. Selecione um modelo.
   
   ![Selecione um modelo][select-a-template]
3. No **Gerenciador de Soluções**, clique com botão direito em **Referências**, em seguida, clique em **Gerenciar Pacotes NuGet**.
4. Procure **SendGrid** e selecione o item **SendGrid** na lista de resultados.
   
   ![pacote NuGet do SendGrid][SendGrid-NuGet-package]
5. Clique em **Instalar** para concluir a instalação e, em seguida, feche essa caixa de diálogo.

A biblioteca de classes do .NET do SendGrid é denominada **SendGridMail**. Ela contém os seguintes namespaces:

* **SendGridMail** para criar e trabalhar com itens de email.
* **SendGridMail.Transport** para enviar email usando o protocolo **SMTP** ou o protocolo HTTP 1.1 com **Web/REST**.

Adicione as seguintes declarações de namespace do código à parte superior de qualquer arquivo C\# no qual você deseja acessar programaticamente o serviço de email SendGrid.
**System.Net** e **System.Net.Mail** são os namespaces do .NET Framework incluídos porque eles incluem tipos que você normalmente usará com as APIs do SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Como criar um email
Use o objeto **SendGridMessage** para criar uma mensagem de email. Quando o objeto de mensagem for criado, você poderá definir as propriedades e os métodos, incluindo o remetente do email, o destinatário do email e o assunto e o corpo do email.

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

## <a name="how-to-send-an-email"></a>Como: enviar um email
Após criar uma mensagem de email, você poderá enviá-la usando a API da Web fornecida pelo SendGrid. Alternativamente, você pode [usar a compilação do .NET na biblioteca](https://sendgrid.com/docs/Code_Examples/csharp.html).

O envio de email requer que você forneça suas credenciais de conta do SendGrid (nome de usuário e senha) ou a chave da API do SendGrid. A chave da API é o método preferencial. Se precisar de detalhes sobre como configurar as chaves da API, veja nossa [documentação](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Você pode armazenar essas credenciais por meio do Portal do Azure ao clicar em CONFIGURAR e adicionar os pares chave/valor em "configurações do aplicativo".

 ![Configurações do aplicativo do Azure][azure_app_settings]

 Em seguida, você pode acessá-las da seguinte forma: 

    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Usando as credenciais:

    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Usando a chave da API:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


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

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo
É possível adicionar anexos a uma mensagem, chamando o método **AddAttachment** e especificando o nome e o caminho do arquivo que você deseja anexar.
Você pode incluir vários anexos, chamando esse método uma vez para cada arquivo que quiser anexar. O exemplo a seguir demonstra como adicionar um anexo a uma mensagem:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Você também pode adicionar anexos a partir do **Stream**de dados. Isso pode ser feito ao chamar o mesmo método acima, **AddAttachment**, mas ao passar no Stream dos dados, e o nome do arquivo que você quer mostrar na mensagem. Nesse caso, você precisará adicionar a biblioteca System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Como usar aplicativos para habilitar rodapés, acompanhamento e análise
O SendGrid fornece a funcionalidade adicional de email por meio do uso de aplicativos. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar acompanhamento de clique, Googleanalytics, acompanhamento de assinatura e assim por diante. Para obter uma lista completa de aplicativos, consulte [Configurações do aplicativo][Configurações do aplicativo].

Os aplicativos podem ser aplicados nas mensagens de email do **SendGrid** usando os métodos implementados como parte da classe **SendGrid**.

Os exemplos a seguir demonstram os filtros de rodapé e de acompanhamento de cliques:

### <a name="footer"></a>Rodapé
    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Rastreamento de cliques
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

## <a name="how-to-use-additional-sendgrid-services"></a>Como: usar serviços adicionais do SendGrid
O SendGrid oferece API e Ganchos da web baseados na web, que você pode usar para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [Documentação da API do SendGrid][Documentação da API do SendGrid].

## <a name="next-steps"></a>Próximas etapas
Agora que você já conhece as noções básicas do serviço de email SendGrid, siga estes links para saber mais.

* Repositório de bibliotecas C do\# SendGrid: [sendgrid-csharp][sendgrid-csharp]
* Documentação da API do SendGrid: <https://sendgrid.com/docs>
* Oferta especial de SendGrid para clientes Azure: [https://sendgrid.com](https://sendgrid.com)

[Próximas etapas]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[Como: Usar Filtros para Habilitar Rodapés, Acompanhamento e Análise]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[oferta especial]: https://www.sendgrid.com/windowsazure.html

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
[select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. API da Web]: https://sendgrid.com/docs/Integrate/index.html
[Configurações do aplicativo]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[Documentação da API do SendGrid]: https://sendgrid.com/docs

[serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de email transacional]: https://sendgrid.com/transactional-email




<!--HONumber=Nov16_HO3-->


