<properties 
	pageTitle="Como usar o serviço de email SendGrid (Node.js) | Microsoft Azure" 
	description="Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos usando a API do Node. js." 
	services="" 
	documentationCenter="nodejs" 
	authors="erikre" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="erikre"/>
# Como enviar emails usando o SendGrid do Node.js

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Microsoft Azure. As amostras são gravadas usando a API do Node.js. Os cenários abordados incluem a **construção de emails**, o **envio de emails**, a **adição de anexos**, o **uso de filtros** e a **atualização de propriedades**. Para obter mais informações sobre o SendGrid e o envio de e-mails, consulte a seção [Próximas etapas](#next-steps).

## O que é o serviço de email SendGrid?

O SendGrid é um [serviço de email baseado em nuvem](https://sendgrid.com/email-solutions) que oferece [entrega de email transacional](https://sendgrid.com/transactional-email), escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente panfletos eletrônicos e ofertas especiais
-   Coleta de métrica em tempo real para, por exemplo, email bloqueado e capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
-   Notificações de email de seu aplicativo

Para obter mais informações, consulte [https://sendgrid.com](https://sendgrid.com).

## Criar uma conta do SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## Referenciar o módulo do Node.js SendGrid

O módulo de SendGrid para o Node.js pode ser instalado através do gerenciador de pacotes do nó (npm) usando o seguinte comando:

    npm install sendgrid

Após a instalação, você pode exigir o módulo em seu aplicativo, usando o código a seguir:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

O módulo de SendGrid exporta as funções **SendGrid** e **Email**. **SendGrid** é responsável por enviar email por meio da API da Web, enquanto **Email** encapsula uma mensagem de email.

## Como: criar um email

Criar uma mensagem de e-mail usando o módulo SendGrid envolve criar primeiro uma mensagem de e-mail usando a função de E-mail e, em seguida, enviá-la usando a função SendGrid. Este é um exemplo da criação de uma nova mensagem usando a função de Email:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Você também pode especificar uma mensagem de HTML a clientes que dão suporte a ela, definindo a propriedade de html. Por exemplo:

    html: This is a sample <b>HTML<b> email message.

Definir propriedades de texto e de html fornece retorno normal ao conteúdo de texto para clientes que não podem dar suporte a mensagens em HTML.

Para obter mais informações sobre todas as propriedades compatíveis com a função de Email, consulte [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

## Como: enviar um email

Depois de criar uma mensagem de email usando a função Email, você pode enviá-la usando a API da Web fornecida pelo SendGrid.

### API Web

    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [AZURE.NOTE]Enquanto os exemplos acima mostram a passagem em uma função de retorno de chamada e de objeto de email, você pode invocar diretamente as funções de envio especificando diretamente as propriedades do email. Por exemplo:
>
>`````
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
`````

## Como: adicionar um anexo

Os anexos podem ser adicionados a uma mensagem, especificando o(s) nome(s) e caminho(s) dos arquivos na propriedade **arquivos**. O exemplo a seguir demonstra como enviar um anexo:

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [AZURE.NOTE]Ao usar a propriedade **arquivos**, o arquivo deve estar acessível por [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se o arquivo que você deseja anexar é hospedado no armazenamento do Azure, como um contêiner de Blob, você deve primeiro copiar o arquivo para o armazenamento local ou para uma unidade do Azure antes de ser enviado como um anexo usando a propriedade **arquivos**.

## Como usar filtros para habilitar rodapés e acompanhamento

O SendGrid fornece a funcionalidade adicional de email por meio do uso de filtros. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar rastreamento de cliques, Google analytics, rastreamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte [Configurações de filtro](https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html).

Os filtros podem ser aplicados a uma mensagem pela propriedade **filtros**. Cada filtro é especificado por um hash que tem configurações específicas de filtro. Os exemplos a seguir demonstram os filtros de rodapé e de acompanhamento de cliques:

### Rodapé

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### Acompanhamento de cliques

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });
    
    sendgrid.send(email);

## Como atualizar as propriedades do e-mail

Algumas propriedades de email podem ser substituídas usando **set*Property*** ou acrescentadas usando **add*Property***. Por exemplo, você pode adicionar destinatários adicionais ao usar

    email.addTo('jeff@contoso.com');

ou definir um filtro usando

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Para obter mais informações, consulte [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

## Como: usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [Documentação da API do SendGrid](https://sendgrid.com/docs).

## Próximas etapas

Agora que você já conhece as noções básicas do serviço de email SendGrid, siga estes links para saber mais.

-   Repositório do módulo SendGrid Node.js: [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)
-   Documentação da API do SendGrid: <https://sendgrid.com/docs>
-   Oferta especial do SendGrid para clientes do Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)
  [oferta especial]: https://sendgrid.com/windowsazure.html
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [configurações de filtro]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [documentação da API do SendGrid]: https://sendgrid.com/docs
  [serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
  [entrega de email transacional]: https://sendgrid.com/transactional-email

<!---HONumber=AcomDC_0107_2016-->