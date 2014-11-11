<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Como enviar e-mails usando o SendGrid do Node.js

Este guia demonstra como executar tarefas comuns de programação com o serviço de e-mail SendGrid no Windows Azure. As amostras são gravadas usando a API do Node.js. Os cenários abordados incluem a **construção de e-mails**, o **envio de e-mails**, a **adição de anexos**, o **uso de filtros** e a **atualização de propriedades**. Para obter mais informações sobre o SendGrid e o envio de e-mails, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é o serviço de email SendGrid?][O que é o serviço de email SendGrid?]
-   [Criar uma conta do SendGrid][Criar uma conta do SendGrid]
-   [Referenciar o módulo do Node.js SendGrid][Referenciar o módulo do Node.js SendGrid]
-   [Como: Criar um email][Como: Criar um email]
-   [Como: Enviar um email][Como: Enviar um email]
-   [Como: Adicionar um anexo][Como: Adicionar um anexo]
-   [Como: Usar filtros para habilitar rodapés, rastreamento e análise][Como: Usar filtros para habilitar rodapés, rastreamento e análise]
-   [Como: Atualizar as propriedades do e-mail][Como: Atualizar as propriedades do e-mail]
-   [Como: Usar serviços adicionais do SendGrid][Como: Usar serviços adicionais do SendGrid]
-   [Próximas etapas][1]

## <a name="whatis"> </a>O que é o serviço de email SendGrid?

O SendGrid é um [serviço de email baseado em nuvem][serviço de email baseado em nuvem] que oferece [entrega de email transacional][entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais
-   Coleta de métrica em tempo real para, por exemplo, email bloqueado e
    capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
-   Notificações de email a partir de seu aplicativo

Para obter mais informações, consulte [][]<http://sendgrid.com></a>.

## <a name="createaccount"> </a>Criar uma conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Referenciar o módulo do Node.js SendGrid

O módulo de SendGrid para o Node.js pode ser instalado através do gerenciador de pacotes do nó (npm) usando o seguinte comando:

    npm install sendgrid

Após a instalação, você pode exigir o módulo em seu aplicativo, usando o código a seguir:

    var SendGrid = require('sendgrid')

O módulo SendGrid exporta as funções **SendGrid** e **Email**. **SendGrid** é responsável pelo envio de e-mails através de SMTP ou Web API, enquanto o **E-mail** encapsula uma mensagem de e-mail.

## <a name="createemail"> </a>Como: Criar um email

Criar uma mensagem de e-mail usando o módulo SendGrid envolve criar primeiro uma mensagem de e-mail usando a função de E-mail e, em seguida, enviá-la usando a função SendGrid. Este é um exemplo da criação de uma nova mensagem usando a função de E-mail:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Você também pode especificar uma mensagem de HTML para clientes que a suportam, definindo a propriedade de html. Por exemplo:

    html: This is a sample <b>HTML<b> email message.

Definir propriedades de texto e de html fornece retorno normal ao conteúdo de texto para clientes que não suportam mensagens em HTML.

Para obter mais informações sobre todas as propriedades suportadas pela função de E-mail, consulte [sendgrid-nodejs][sendgrid-nodejs].

## <a name="sendemail"> </a>Como: Enviar um email

Após criar uma mensagem de e-mail, utilizando a função de E-mail, você poderá enviá-la usando SMTP ou a API Web fornecida pelo SendGrid. Para obter detalhes sobre os benefícios e as diferenças de cada API, consulte[SMTP x API da Web][SMTP x API da Web] na documentação do SendGrid.

Usando a API de SMTP ou a API da Web requer que você inicialize primeiro a função de SendGrid usando o usuário e a chave da sua conta de SendGrid da seguinte forma:

    var sender = new SendGrid('user','key');

A mensagem pode ser enviada agora usando a API da Web ou SMTP. As chamadas são praticamente idênticas, passando a mensagem de e-mail e uma função de retorno de chamada opcional; o retorno de chamada é usado para determinar o êxito ou a falha da operação. Os exemplos a seguir mostram como enviar uma mensagem usando SMTP e a API da Web.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### API Web

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Enquanto os exemplos acima mostram a passagem em uma fun&ccedil;&atilde;o de objeto de e-mail e de retorno de chamada, voc&ecirc; pode chamar diretamente as fun&ccedil;&otilde;es de envio e smtp especificando diretamente as propriedades de e-mail. Por exemplo:</p>
<pre class="prettyprint">sender.send({
    para: 'john@contoso.com',
    de: 'anna@contoso.com',
    assunto: 'teste&rsquo;,
    texto: 'Este &eacute; um exemplo.'
});
</pre>
</div>

## <a name="addattachment"> </a>Como: Adicionar um anexo

Os anexos podem ser adicionados a uma mensagem, especificando o(s) nome(s) dos arquivos e caminho(s) na propriedade **arquivos**. O exemplo a seguir demonstra como enviar um anexo:

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Ao usar a propriedade <strong>arquivos</strong>, o arquivo deve estar acess&iacute;vel por meio de <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Se o arquivo que voc&ecirc; deseja anexar &eacute; hospedado no armazenamento do Azure, como um cont&ecirc;iner de Blob, voc&ecirc; deve primeiro copiar o arquivo para o armazenamento local ou para uma unidade do Azure antes de ser enviado como um anexo usando a propriedade <strong>arquivos</strong>.</p>
</div>

## <a name="usefilters"> </a>Como: Usar filtros para habilitar rodapés, rastreamento e Twitter

O SendGrid fornece a funcionalidade adicional de e-mail por meio do uso de filtros. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar rastreamento de clique, Google analytics, rastreamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte[Configurações de filtro][Configurações de filtro].

Os filtros podem ser aplicados a uma mensagem pela propriedade **filtros**. Cada filtro é especificado por um hash que possui configurações específicas de filtro. Os exemplos a seguir mostram os filtros de rodapé, o rastreamento de cliques e Twitter:

### Rodapé

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### Rastreamento de cliques

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

## <a name="updateproperties"> </a>Como: Atualizar as propriedades do e-mail

Algumas propriedades de e-mail podem ser substituídas usando **definir*propriedade*** ou anexadas usando **adicionar*propriedade***. Por exemplo, você pode adicionar destinatários adicionais ao usar

    email.addTo('jeff@contoso.com');

ou definir um filtro usando

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

Para obter mais informações, consulte [sendgrid-nodejs][sendgrid-nodejs].

## <a name="useservices"> </a>Como: Usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][documentação da API do SendGrid].

## <a name="nextsteps"> </a>Próximas etapas

Agora que você já conhece os princípios do serviço de email do SendGrid, acesse estes links para saber mais.

-   Repositório do módulo SendGrid Node.js: [sendgrid-nodejs][sendgrid-nodejs]
-   Documentação da API do SendGrid:
    <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial de SendGrid para clientes Azure:
    [][2]<http://sendgrid.com/azure.html></a>

  [Próximas etapas]: http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Criar uma conta do SendGrid]: #createaccount
  [Referenciar o módulo do Node.js SendGrid]: #reference
  [Como: Criar um email]: #createemail
  [Como: Enviar um email]: #sendemail
  [Como: Adicionar um anexo]: #addattachment
  [Como: Usar filtros para habilitar rodapés, rastreamento e análise]: #usefilters
  [Como: Atualizar as propriedades do e-mail]: #updateproperties
  [Como: Usar serviços adicionais do SendGrid]: #useservices
  [1]: #nextsteps
  [serviço de email baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de email transacional]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP x API da Web]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [Configurações de filtro]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [documentação da API do SendGrid]: http://docs.sendgrid.com/documentation/api/
  [2]: http://sendgrid.com/azure.html
