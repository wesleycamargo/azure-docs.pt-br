<properties 
	pageTitle="Enviar email usando o SendGrid – Serviços Móveis do Azure" 
	description="Aprenda a usar o serviço SendGrid para enviar emails de seu aplicativo de serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="Erikre" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="Erikre"/>


# Enviar email dos Serviços Móveis com SendGrid

Este tópico mostra como adicionar a funcionalidade de e-mail ao seu serviço móvel. Nste tópico, você adicionará scripts do lado do servidor para enviar e-mails usando o SendGrid. Ao concluir, seu serviço móvel enviará um e-mail sempre que um registro for inserido.

O SendGrid é um [serviço de e-mail baseado em nuvem] que oferece [entrega de e-mail transacional], escalabilidade e análise em tempo real confiáveis com APIsflexíveis que facilitam a integração personalizada. Para obter mais informações, consulte <http://sendgrid.com>.

Este tutorial explica as etapas básicas para habilitar a funcionalidade de e-mail:

1. [Criar uma conta do SendGrid]
2. [Adicionar um script para enviar emails]
3. [Inserir dados para receber emails]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis].

## <a name="sign-up"></a>Criar uma nova conta do SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>Registrar um novo script que envia emails

1. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

2. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

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

	- **_email-address_**: o endereço para o qual os emails são enviados. Em um aplicativo do mundo real, você pode usar as tabelas para armazenar e recuperar endereços de e-mail. Ao testar o seu aplicativo, basta usar o seu próprio endereço de e-mail.

	- **_from-address_**: o endereço de onde se origina o email. Considere usar um endereço de domínio registrado que pertence à sua organização.

     >[AZURE.NOTE]Se não tiver um domínio registrado, você pode usar o domínio de seus Serviços Móveis, no formato *notifications@_your-mobile-service_.azure-mobile.net*. No entanto, as mensagens enviadas para seu domínio de serviços móveis serão ignoradas.

6. Clique no botão **Salvar**. Você configurou um script para enviar um e-mail sempre que um registro for inserido na tabela **TodoItem**.

## <a name="insert-data"></a>Inserir dados de teste para receber emails

1. No projeto de aplicativo do cliente, execute o aplicativo de início rápido. 

	Este tópico mostra a versão do Windows Store do início rápido,

2. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

	![][3]

3. Observe que você recebe um e-mail, como o mostrado na notificação abaixo.

	![][4]

	Parabéns, você configurou com êxito seu serviço móvel para que ele envie e-mails usando o SendGrid.

## <a name="nextsteps"> </a>Próximas etapas

Agora que você já viu como é fácil usar o serviço de emails do SendGrid com os Serviços Móveis, acesse estes links para saber mais sobre o SendGrid.

-   Documentação da API do SendGrid: <https://sendgrid.com/docs>
-   Oferta especial do SendGrid para clientes do Azure: <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Criar uma conta SendGrid]: #sign-up
[Criar uma conta do SendGrid]: #sign-up
[Adicionar um script para enviar emails]: #add-script
[Inserir dados para receber emails]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[serviço de e-mail baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email

 

<!---HONumber=July15_HO2-->