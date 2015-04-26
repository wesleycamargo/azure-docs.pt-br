<properties 
	pageTitle="Enviar email usando o SendGrid - serviços móveis do Azure" 
	description="Aprenda a usar o serviço SendGrid para enviar emails de seu aplicativo de serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>


# Enviar email dos Serviços Móveis com SendGrid

Este tópico mostra como adicionar a funcionalidade de email ao seu serviço móvel. Nste tópico, você adicionará scripts do lado do servidor para enviar e-mails usando o SendGrid. Ao concluir, seu serviço móvel enviará um e-mail sempre que um registro for inserido.

O SendGrid é um [serviço de e-mail baseado em nuvem] que oferece [entrega de e-mail transacional], escalabilidade e análise em tempo real confiáveis com APIsflexíveis que facilitam a integração personalizada. Para obter mais informações, consulte <http://sendgrid.com>.

Este tutorial explica as etapas básicas para habilitar a funcionalidade de e-mail:

1. [Criar uma conta do SendGrid]
2. [Adicionar um script para enviar e-mails]
3. [Inserir dados para receber e-mails]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

<h2><a name="sign-up"></a>Criar uma nova conta do SendGrid</h2>

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="add-script"></a>Registrar um novo script que envia emails</h2>

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

2. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

	![][1]

3. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
	![][2]

	Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

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

     > [AZURE.NOTE] Se você não tiver um domínio registrado, você pode usar o domínio de seus Serviços Móveis, no formato *notifications@_your-mobile-service_.azure-mobile.net*. No entanto, as mensagens enviadas para seu domínio de serviços móveis serão ignoradas.

6. Clique no botão **Salvar**. Você configurou um script para enviar um e-mail sempre que um registro for inserido na tabela **TodoItem**.

<h2><a name="insert-data"></a>Inserir dados para receber emails</h2>

1. No projeto de aplicativo do cliente, execute o aplicativo de início rápido. 

	Este tópico mostra a versão do Windows Store do início rápido,

2. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

	![][3]

3. Observe que você recebe um e-mail, como o mostrado na notificação abaixo. 

	![][4]

	Parabéns, você configurou com êxito seu serviço móvel para que ele envie e-mails usando o SendGrid.

## <a name="nextsteps"></a>Próximas etapas

Agora que você viu como é fácil usar o serviço de email SendGrid com os serviços móveis, siga
estes links para saber mais sobre o SendGrid.

-   Documentação da API do SendGrid:
    <https://sendgrid.com/docs>
-   Oferta especial do SendGrid para clientes do Azure:
    <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Criar uma conta do SendGrid]: #sign-up
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
[página de inscrição]: https://sendgrid.com/windowsazure.html
[Página de credenciais de usuário vários]: https://sendgrid.com/credentials
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[serviço de e-mail baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email




<!--HONumber=42-->
