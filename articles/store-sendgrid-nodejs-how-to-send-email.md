<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="Serviço de e-mail SendGrid" pageTitle="Como usar o serviço de e-mail SendGrid (Node.js) - Azure" metaKeywords="SendGrid do Azure, serviço de e-mail do Azure, SendGrid Node.js do Azure, e-mail Node.js do Azure" description="Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Os exemplos de código são escritos usando a API do Node.js." metaCanonical="" services="" documentationCenter="Node.js" title="Como enviar e-mail usando o SendGrid do Node.js" authors="" solutions="" manager="" editor="" />





# Como enviar e-mails usando o SendGrid do Node.js

Este guia demonstra como executar tarefas comuns de programação com o
serviço de e-mail SendGrid no Azure. As amostras são escritas usando a API do Node.js. Os cenários abordados incluem **construir e-mail**,
**enviar e-mail**, **adicionar anexos**, **usar filtros** e
**atualizar propriedades**. Para obter mais informações sobre o SendGrid e o envio de e-mails, consulte a seção [Próximas etapas][].

## Sumário

* [O que é o serviço de email SendGrid?][]   
* [Criar uma conta SendGrid][]   
* [Referenciar o módulo do Node.js SendGrid][]   
* [Como criar um e-mail][]   
* [Como enviar um e-mail][]   
* [Como adicionar um anexo][]   
* [Como usar filtros para habilitar rodapés, controle e análises][]   
* [Como atualizar as propriedades do e-mail][]   
* [Como usar serviços adicionais do SendGrid][]   
* [Próximas etapas][1]

## <a name="whatis"> </a>O que é o Serviço de E-mail do SendGrid?

O SendGrid é um [serviço de e-mail baseado em nuvem] que oferece
[entrega de e-mail transacional], escalabilidade e análise em tempo real confiáveis com APIs
flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid
incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais
-   Coleta de métricas em tempo real para e-mail bloqueado e
    capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar tendências
-   Encaminhamento de consultas dos clientes
-   Notificações de e-mail do seu aplicativo

Para obter mais informações, consulte [http://sendgrid.com](http://sendgrid.com).

## <a name="createaccount"> </a>Criar uma conta SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Referenciar o módulo do Node.js SendGrid

O módulo de SendGrid para o Node.js pode ser instalado através do gerenciador de pacotes do nó (npm) usando o seguinte comando:

    npm install sendgrid

Após a instalação, você pode exigir o módulo em seu aplicativo, usando o código a seguir:

    var SendGrid = require('sendgrid')

O módulo de SendGrid exporta as funções **SendGrid** e **E-mail**.
**SendGrid** é responsável pelo envio de e-mails através de SMTP ou Web API, enquanto o **E-mail** encapsula uma mensagem de e-mail.

## <a name="createemail"> </a>Como criar um e-mail

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

Para obter mais informações sobre todas as propriedades suportadas pela função de E-mail, consulte [sendgrid-nodejs][].

## <a name="sendemail"> </a>Como enviar um e-mail

Após criar uma mensagem de e-mail, utilizando a função de e-mail, você poderá enviá-la usando SMTP ou a API Web fornecida pelo SendGrid. Para obter detalhes sobre os benefícios e as diferenças de cada API, consulte [SMTP x API da Web][]
na documentação do SendGrid.

Usando a API de SMTP ou a API da Web requer que você inicialize primeiro a função de SendGrid usando o usuário e a chave da sua conta de SendGrid da seguinte forma:

    var sender = new SendGrid.SendGrid('user','key');

A mensagem pode ser enviada agora usando a API da Web ou SMTP. As chamadas são praticamente idênticas, passando a mensagem de e-mail e uma função de retorno de chamada opcional; O retorno de chamada é usado para determinar o êxito ou a falha da operação. Os exemplos a seguir mostram como
enviar uma mensagem usando SMTP e a API da Web.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### API da Web

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

<div class="dev-callout">
<strong>Observação</strong>
<p>Enquanto os exemplos acima mostram a passagem em uma função de retorno de chamada e de objeto de e-mail, você pode chamar diretamente as funções de envio e smtp especificando diretamente as propriedades do e-mail. Por exemplo:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Como adicionar um anexo

Os anexos podem ser adicionados a uma mensagem, especificando os nomes dos arquivos e caminhos na propriedade **arquivos**. O exemplo a seguir demonstra como enviar
um anexo:

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
<p>Ao usar a propriedade <strong>arquivos</strong>, o arquivo deve ser acessível por meio de <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Se o arquivo que você deseja anexar é hospedado no armazenamento do Azure, como um contêiner de Blob, você deve primeiro copiar o arquivo para o armazenamento local ou para uma unidade do Azure antes de ser enviado como um anexo usando a propriedade <strong>arquivos</strong>.</p>
</div>

## <a name="usefilters"> </a>Como usar filtros para habilitar rodapés, rastreamento e Twitter

O SendGrid fornece a funcionalidade adicional de e-mail pelo uso de filtros. Essas são as configurações que podem ser adicionadas a uma mensagem de e-mail para habilitar uma funcionalidade específica, como habilitar rastreamento de clique, Google analytics, rastreamento de assinatura e assim por diante. Para obter a lista completa de filtros,
consulte [Configurações de filtro][].

Os filtros podem ser aplicados a uma mensagem usando a propriedade **filtros**.
Cada filtro é especificado por um hash que possui configurações específicas de filtro.
Os exemplos a seguir demonstram os filtros do Twitter, de rodapé e de rastreamento de cliques:

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

## <a name="updateproperties"> </a>Como atualizar as propriedades do e-mail

Algumas propriedades de e-mail podem ser substituídas usando **definir*propriedade*** ou anexadas usando **adicionar*propriedade***. Por exemplo, você pode adicionar destinatários adicionais usando

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

## <a name="useservices"> </a>Como usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][].

## <a name="nextsteps"> </a>Próximas etapas

Agora que você já conhece as noções básicas do serviço de e-mail SendGrid, siga
estes links para saber mais.

-   Repositório do módulo SendGrid Node.js: [sendgrid-nodejs][]
-   Documentação da API do SendGrid:
    <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial de SendGrid para clientes Azure:
    [http://sendgrid.com/azure.html](http://sendgrid.com/azure.html)

  [Próximas etapas]: http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [O que é o serviço de email SendGrid?]: #whatis
  [Criar uma conta SendGrid]: #createaccount
  [Referenciar o módulo do Node.js SendGrid]: #reference
  [Como criar um e-mail]: #createemail
  [Como enviar um e-mail]: #sendemail
  [Como adicionar um anexo]: #addattachment
  [Como usar filtros para habilitar rodapés, controle e análises]: #usefilters
  [Como atualizar as propriedades do e-mail]: #updateproperties
  [Como usar serviços adicionais do SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [oferta especial]: http://www.sendgrid.com/azure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP x API da Web]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  
  [Configurações de filtro]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [Documentação da API do SendGrid]: http://docs.sendgrid.com/documentation/api/
  [serviço de e-mail baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de e-mail transacional]: http://sendgrid.com/transactional-email

