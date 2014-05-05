<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Envie e-mails usando o SendGrid" pageTitle="Envie e-mails usando o SendGrid – Serviços Móveis no Azure" metaKeywords="SendGrid do Azure, serviço do SendGrid, envio de e-mails no Azure, e-mail dos serviços móveis" description="Saiba como usar o serviço do SendGrid para enviar e-mails de seu Aplicativo de Serviços Móveis no Azure" metaCanonical="" services="" documentationCenter="Mobile" title="Envie e-mails dos Serviços Móveis com o SendGrid" authors="" solutions="" manager="" editor="" />




# Enviar e-mails dos Serviços Móveis com o SendGrid

Este tópico mostra como adicionar a funcionalidade de e-mail ao seu serviço móvel. Nste tópico, você adicionará scripts do lado do servidor para enviar e-mails usando o SendGrid. Ao concluir, seu serviço móvel enviará um e-mail sempre que um registro for inserido.

O SendGrid é um [serviço de e-mail baseado em nuvem] que oferece [entrega de e-mail transacional], escalabilidade e análise em tempo real confiáveis com APIs
flexíveis que facilitam a integração personalizada. Para obter mais informações, consulte <http://sendgrid.com>.

Este tutorial explica as etapas básicas para habilitar a funcionalidade de e-mail:

1. [Criar uma conta SendGrid]
2. [Adicionar um script para enviar e-mails]
3. [Inserir dados para receber e-mails]

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

<h2><a name="sign-up"></a><span class="short-header">Criar uma nova conta do SendGrid</span>Criar uma nova conta do SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="add-script"></a><span class="short-header">Registrar um script</span>Registrar um novo script que envia e-mails</h2>

1. Faça login no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu serviço móvel.

2. No Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItem**. 

	![][1]

3. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
	![][2]

	Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

4. Substitua a função de inserção pelo seguinte código:

        var SendGrid = require('sendgrid').SendGrid;
        
        function insert(item, user, request) {    
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');       
                
                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5. Substitua os espaços reservados no script acima pelos valores corretos:

	- **_username_ e _password_**: as credenciais do SendGrid identificadas em [Criar uma conta SendGrid].

	- **_email address_**: o endereço para o qual os e-mails são enviados. Em um aplicativo do mundo real, você pode usar as tabelas para armazenar e recuperar endereços de e-mail. Ao testar o seu aplicativo, basta usar o seu próprio endereço de e-mail.

	- **_from address_**: o endereço do e-mail de origem. Considere usar um endereço de domínio registrado que pertence à sua organização. 

     <div class="dev-callout"><b>Observação</b>
     <p>Se você não tiver um domínio registrado, você pode usar o domínio de seus Serviços Móveis, no formato <strong>notificações@<i>seu-serviço-móvel</i>.azure-mobile.net</strong>. No entanto, as mensagens enviadas para seu domínio de serviços móveis serão ignoradas.</p>
    </div> 

6. Clique no botão **Salvar**. Você configurou um script para enviar um e-mail sempre que um registro for inserido na tabela **TodoItem**.

<h2><a name="insert-data"></a><span class="short-header">Inserir dados de teste</span>Inserir dados de teste para receber e-mails</h2>

1. No projeto de aplicativo do cliente, execute o aplicativo de início rápido. 

	Este tópico mostra a versão do Windows Store do início rápido,

2. No aplicativo, digite um texto em **Inserir um TodoItem** e clique em **Salvar**.

	![][3]

3. Observe que você recebe um e-mail, como o mostrado na notificação abaixo. 

	![][4]

	Parabéns, você configurou com êxito seu serviço móvel para que ele envie e-mails usando o SendGrid.

## <a name="nextsteps"> </a>Próximas etapas

Agora que você já viu como é fácil usar o serviço de e-mails do SendGrid com os Serviços Móveis, acesse estes links para saber mais sobre o SendGrid.

-   Documentação da API do SendGrid:
    <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial do SendGrid para clientes do Azure:
    <http://sendgrid.com/azure.html>

<!-- Anchors. -->
[Criar uma conta SendGrid]: #sign-up
[Adicionar um script para enviar e-mails]: #add-script
[Inserir dados para receber e-mails]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[página de inscrição]: http://sendgrid.com/azure.html
[página de Credenciais de múltiplos usuários]: http://sendgrid.com/credentials
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[serviço de e-mails baseado em nuvem]: http://sendgrid.com/solutions
[entrega de e-mail transacional]: http://sendgrid.com/transactional-email


