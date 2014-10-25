<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Send Email Using SendGrid" pageTitle="Send email using SendGrid - Azure Mobile Services" metaKeywords="Azure SendGrid, SendGrid service, Azure emailing, mobile services email" description="Learn how to use the SendGrid service to send email from your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"></tags>

# Enviar email dos Serviços Móveis com SendGrid

Este tópico mostra como adicionar a funcionalidade de e-mail ao seu serviço móvel. Nste tópico, você adicionará scripts do lado do servidor para enviar e-mails usando o SendGrid. Ao concluir, seu serviço móvel enviará um e-mail sempre que um registro for inserido.

O SendGrid é um [serviço de e-mail baseado em nuvem][] que oferece [entrega de e-mail transacional][], escalabilidade e análise em tempo real confiáveis com APIsflexíveis que facilitam a integração personalizada. Para obter mais informações, consulte <http://sendgrid.com>.

Este tutorial explica as etapas básicas para habilitar a funcionalidade de e-mail:

1.  [Criar uma conta do SendGrid][]
2.  [Adicionar um script para enviar e-mails][]
3.  [Inserir dados para receber e-mails][]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][].

## <a name="sign-up"></a><span class="short-header">Criar uma nova conta do SendGrid</span>Criar uma nova conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up][]]

## <a name="add-script"></a><span class="short-header">Registrar um script</span>Registrar um novo script que envia e-mails

1.  Faça logon no [Portal de Gerenciamento do Azure][], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

2.  No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][]

3.  Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

    ![][1]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

4.  Substitua a função de inserção pelo seguinte código:

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

5.  Substitua os espaços reservados no script acima pelos valores corretos:

    -   ***nome de usuário* e *senha***: credenciais do SendGrid identificadas em [Criar uma conta SendGrid][Criar uma conta do SendGrid].

    -   ***endereço de e-mail***: o endereço para o qual os e-mails são enviados. Em um aplicativo do mundo real, você pode usar as tabelas para armazenar e recuperar endereços de e-mail. Ao testar o seu aplicativo, basta usar o seu próprio endereço de e-mail.

    -   ***from-address***: o endereço do e-mail de origem. Considere usar um endereço de domínio registrado que pertence à sua organização.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b><br /> <p>Se voc&ecirc; n&atilde;o tiver um dom&iacute;nio registrado, voc&ecirc; pode usar o dom&iacute;nio de seus Servi&ccedil;os M&oacute;veis, no formato <strong>notifica&ccedil;&otilde;es@<i>seu-servi&ccedil;o-m&oacute;vel</i>.azure-mobile.net</strong>. No entanto, as mensagens enviadas para seu dom&iacute;nio de servi&ccedil;os m&oacute;veis ser&atilde;o ignoradas.</p><br /></div>

    </p>
6.  Clique no botão **Salvar**. Você configurou um script para enviar um e-mail sempre que um registro for inserido na tabela **TodoItem**.

## <a name="insert-data"></a><span class="short-header">Inserir dados de teste</span>Inserir dados de teste para receber e-mails

1.  No projeto de aplicativo do cliente, execute o aplicativo de início rápido.

    Este tópico mostra a versão do Windows Store do início rápido,

2.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][2]

3.  Observe que você recebe um e-mail, como o mostrado na notificação abaixo.

    ![][3]

    Parabéns, você configurou com êxito seu serviço móvel para que ele envie e-mails usando o SendGrid.

## <a name="nextsteps"> </a>Próximas etapas

Agora que você já viu como é fácil usar o serviço de e-mails do SendGrid com os Serviços Móveis, acesse
estes links para saber mais sobre o SendGrid.

-   Documentação da API do SendGrid:
    <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial de SendGrid para clientes Azure:
    <http://sendgrid.com/azure.html>

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [serviço de e-mail baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de e-mail transacional]: http://sendgrid.com/transactional-email
  [Criar uma conta do SendGrid]: #sign-up
  [Adicionar um script para enviar e-mails]: #add-script
  [Inserir dados para receber e-mails]: #insert-data
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
  [1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
  [2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
  [3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png
