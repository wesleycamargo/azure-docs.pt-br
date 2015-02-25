<properties urlDisplayName="SendGrid Email Service" pageTitle="Como usar o serviço de email SendGrid (Node. js) - Azure" metaKeywords ="SendGrid do Azure, serviço de email do Azure, Node. js SendGrid do Azure, email do Azure Node. js" description="Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos usando a API do Node. js." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />





# Como enviar e-mails usando o SendGrid do Node.js

Este guia demonstra como executar tarefas comuns de programação com o
Serviço de email SendGrid no Azure. Os exemplos são escritos usando a API do Node.js. Os cenários abordados incluem a **construção de e-mails**, o**envio de e-mails**, a **adição de anexos**, o **uso de filtros** e a**atualização de propriedades**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a seção [Próximas etapas][].

## Sumário

* [O que é o serviço de email SendGrid?][]   
* [Criar uma conta do SendGrid][]   
* [Referenciar o módulo do Node.js SendGrid][]   
* [Como: Criar um email][]   
* [Como: Enviar um email][]   
* [Como: Adicionar um anexo][]   
* [Como: Usar filtros para habilitar rodapés, acompanhamento e análise][]   
* [Como: Atualizar as propriedades do email][]   
* [Como: Usar serviços adicionais do SendGrid][]   
* [Próximas etapas][1]

## <a name="whatis"> </a>O que é o serviço de email SendGrid?

O SendGrid é um [serviço de email baseado em nuvem] que oferece [entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

-   Envio automático de recibos para os clientes
-   Distribuição de administração de listas para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais
-   Coleta de métricas em tempo real para email bloqueado e
    resposta ao cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
-   Notificações de email a partir de seu aplicativo

Para obter mais informações, consulte [http://sendgrid.com](https://sendgrid.com).

## <a name="createaccount"> </a>Criar uma conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Referenciar o módulo do Node.js SendGrid

O módulo de SendGrid para o Node.js pode ser instalado através do gerenciador de pacotes do nó (npm) usando o seguinte comando:

    npm install sendgrid

Após a instalação, você pode exigir o módulo em seu aplicativo, usando o código a seguir:

    var SendGrid = require('sendgrid')

O módulo de SendGrid exporta as funções **SendGrid** e **Email**. **SendGrid** é responsável pelo envio de emails através de SMTP ou Web API, enquanto o **Email** encapsula uma mensagem de email.

## <a name="createemail"> </a>Como: Criar um email

A criação de uma mensagem de email usando o módulo SendGrid envolve primeiro
criar uma mensagem de email usando a função de Email e, em seguida, enviá-la
usando a função SendGrid. A seguir está um exemplo de como criar uma
nova mensagem usando a função de Email:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Você também pode especificar uma mensagem de HTML a clientes que dão suporte a ela, definindo a propriedade de html. Por exemplo:

    html: This is a sample <b>HTML<b> email message.

Definir propriedades de texto e de html fornece retorno normal ao conteúdo de texto para clientes que não podem dar suporte a mensagens em HTML.

Para obter mais informações sobre todas as propriedades com suporte pela função de Email, consulte [sendgrid-nodejs][].

## <a name="sendemail"> </a>Como: Enviar um email

Após criar uma mensagem de email, utilizando a função de Email, você poderá enviá-la usando SMTP ou a API Web fornecida pelo SendGrid. Para obter detalhes sobre os benefícios e as diferenças de cada API, consulte [SMTP vs. API da Web][] na documentação do SendGrid.

Usando a API de SMTP ou a API da Web requer que você inicialize primeiro a função de SendGrid usando o usuário e a chave da sua conta de SendGrid da seguinte forma:

    var sender = new SendGrid('user','key');

A mensagem pode ser enviada agora usando a API da Web ou SMTP. As chamadas são praticamente idênticas, passando a mensagem de email e uma função de retorno de chamada opcional; o retorno de chamada é usado para determinar o êxito ou a falha da operação. Os exemplos a seguir mostram como enviar uma mensagem usando SMTP e a API da Web.

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
<strong>Observação</strong>
<p>Enquanto os exemplos acima mostram a passagem em uma função de retorno de chamada e de objeto de email, você pode invocar diretamente as funções de envio e smtp especificando diretamente as propriedades do email. Por exemplo:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
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
<strong>Observação</strong>
<p>Ao usar a propriedade <strong>arquivos</strong>, o arquivo deve ser acessível
por meio de <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Se o arquivo que você deseja anexar é hospedado no armazenamento do Azure, como um contêiner de Blob, você deve primeiro copiar o arquivo para o armazenamento local ou para uma unidade do Azure antes de ser enviado como um anexo usando a propriedade <strong>arquivos</strong>.</p>
</div>

## <a name="usefilters"> </a>Como: Usar filtros para habilitar rodapés, acompanhamento e Twitter

O SendGrid fornece a funcionalidade adicional de email por meio do uso de filtros. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar acompanhamento de clique, Google analytics, acompanhamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte [Configurações de filtro][].

Os filtros podem ser aplicados a uma mensagem pela propriedade **filters**. Cada filtro é especificado por um hash que possui configurações específicas de filtro. Os exemplos a seguir demonstram os filtros do Twitter, de rodapé e de acompanhamento de cliques:

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

Algumas propriedades de email podem ser substituídas usando **set*Property*** ou adicionar usando **add*Property***. Por exemplo, você pode adicionar destinatários adicionais ao usar

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

Para obter mais informações, consulte [sendgrid-nodejs][].

## <a name="useservices"> </a>Como: Usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][].

## <a name="nextsteps"> </a>Próximas etapas

Agora que você já conhece as noções básicas do serviço de email SendGrid, siga estes links para saber mais.

-   Repositório do módulo SendGrid Node.js: [sendgrid-nodejs][]
-   Documentação da API do SendGrid:
    <https://sendgrid.com/docs>
-   Oferta especial do SendGrid para clientes do Azure:
    [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [Próximas etapas]: http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [O que é o serviço de email SendGrid?]: #whatis
  [Criar uma conta do SendGrid]: #createaccount
  [Referenciar o módulo do Node.js SendGrid]: #reference
  [Como: Criar um email]: #createemail
  [Como: Enviar um email]: #sendemail
  [Como: Adicionar um anexo]: #addattachment
  [Como: Usar filtros para habilitar rodapés, acompanhamento e análise]: #usefilters
  [Como: Atualizar as propriedades do email]: #updateproperties
  [Como: Usar serviços adicionais do SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [oferta especial]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP vs. API da Web]: https://sendgrid.com/docs/Integrate/index.html
  
  [Configurações de filtro]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [documentação da API do SendGrid]: https://sendgrid.com/docs
  [serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
  [entrega de email transacional]: https://sendgrid.com/transactional-email
