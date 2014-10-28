<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure

O exemplo a seguir mostra como você pode usar a Twilio para fazer uma chamada de uma página da web PHP hospedada no Azure. O aplicativo resultante solicitará valores de chamada telefônica ao usuário, conforme mostrado na seguinte captura de tela.

![Formulário de chamada do Azure usando a Twilio e o PHP][Formulário de chamada do Azure usando a Twilio e o PHP]

Você precisará fazer o seguinte para usar o código deste tópico:

1.  Obter uma conta e um token de autenticação da Twilio. Para uma introdução à Twilio, avalie os preços em [][]<http://www.twilio.com/pricing></a>. Você pode se inscrever para uma conta de avaliação em [][1]<https://www.twilio.com/try-twilio></a>. Para obter informações sobre a API fornecida pela Twilio, consulte [][2]<http://www.twilio.com/api></a>.
2.  Verifique seu número de telefone como uma ID de chamador de saída com a Twilio. Para obter informações sobre como verificar o número do seu telefone, consulte [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Como alternativa ao uso de um número existente, você pode adquirir um número de telefone da Twilio.
    Para o objetivo deste exemplo, use o número de telefone verificado como o valor de **From** de **callform.php** (descrito posteriormente).
3.  Obtenha a biblioteca da Twilio para PHP. Você pode baixá-la no Github ([][4]<https://github.com/twilio/twilio-php></a>) ou instalá-la como um pacote PEAR. Para obter mais informações, consulte [][5]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.
4.  Instale o SDK do Azure para PHP. Para obter uma visão geral do SDK e obter instruções sobre como instalá-lo, consulte [Configurar o SDK do Azure para PHP][Configurar o SDK do Azure para PHP].

## Criar um formulário da web para fazer uma chamada

O código HTML a seguir mostra como criar uma página da web (**callform.html**) que recupera dados do usuário para fazer uma chamada:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## Como criar o código para fazer a chamada

O seguinte código mostra como compilar uma página web (**makecall.php**) que é chamada quando o usuário envia o formulário exibido pelo **callform.html**. O código abaixo cria a mensagem da chamada e gera a chamada. (Use sua conta e o token de autenticação da Twilio em vez dos valores de espaço reservado atribuídos a **$sid** e **$token** no código a seguir.)

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Além de fazer a chamada, o **makecall.php** exibe alguns metadados da chamada (exemplo mostrado na captura de tela abaixo). Para obter mais informações sobre chamar metadados, consulte [][6]<https://www.twilio.com/docs/api/rest/call#instance-properties></a>.

![Resposta de chamada do Azure usando a Twilio e o PHP][Resposta de chamada do Azure usando a Twilio e o PHP]

## Executar o aplicativo

A próxima etapa é implantar seu aplicativo a Sites do Azure. Os artigos a seguir contêm as informações para criar um site e implantar seu código com Git, FTP ou WebMatrix (embora nem todas as informações em cada artigo sejam relevantes):

-   [Criar um Site do Azure PHP-MySQL e implantar usando o Git][Criar um Site do Azure PHP-MySQL e implantar usando o Git]
-   [Criar um Site do Azure PHP-MySQL e implantar usando FTP][Criar um Site do Azure PHP-MySQL e implantar usando FTP]
-   [Criar e implantar um Site do Azure PHP-MySQL usando o WebMatrix][Criar e implantar um Site do Azure PHP-MySQL usando o WebMatrix]

## Próximas etapas

Esse código foi fornecido para mostrar a funcionalidade básica usando a Twilio no PHP no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erros ou outros recursos. Por exemplo:

-   Em vez de usar um formulário da web, você pode usar os blobs de armazenamento ou o Banco de Dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter informações sobre como usar os blobs do armazenamento do Azure no PHP, consulte [Usando o Armazenamento do Azure com aplicativos PHP][Usando o Armazenamento do Azure com aplicativos PHP]. Para obter informações sobre como usar o Banco de Dados SQL no PHP, consulte [Usando o Banco de Dados SQL com aplicativos PHP][Usando o Banco de Dados SQL com aplicativos PHP].
-   O código **makecall.php** usa a URL fornecida pela Twilio ([][7]<http://twimlets.com/message></a>) para fornecer uma resposta (TwiML) que informa a Twilio sobre como proceder com a chamada. Por exemplo, a TwiML que é retornada pode conter um verbo `<Say>` que resulta em texto que está sendo falado para o destinatário da chamada. Em vez de usar a URL fornecida pela Twilio, você pode criar seu próprio serviço para responder à solicitação da Twilio. Para obter mais informações, consulte [Como usar a Twilio para obter recursos de voz e SMS em PHP][Como usar a Twilio para obter recursos de voz e SMS em PHP]. Mais informações sobre a TwiML podem ser encontradas em [][8]<http://www.twilio.com/docs/api/twiml></a> e mais informações sobre `<Say>` e outros verbos da Twilio podem ser encontradas em [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Leia as diretrizes de segurança da Twilio em [][10]<https://www.twilio.com/docs/security></a>.

Para obter informações adicionais sobre a Twilio, consulte [][11]<https://www.twilio.com/docs></a>.

## Consulte também

-   [Como usar o Twilio para a voz e os recursos de SMS no PHP][Como usar a Twilio para obter recursos de voz e SMS em PHP]

  [Formulário de chamada do Azure usando a Twilio e o PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: https://github.com/twilio/twilio-php
  [5]: https://github.com/twilio/twilio-php/blob/master/README.md
  [Configurar o SDK do Azure para PHP]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
  [6]: https://www.twilio.com/docs/api/rest/call#instance-properties
  [Resposta de chamada do Azure usando a Twilio e o PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
  [Criar um Site do Azure PHP-MySQL e implantar usando o Git]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-git/
  [Criar um Site do Azure PHP-MySQL e implantar usando FTP]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-ftp/
  [Criar e implantar um Site do Azure PHP-MySQL usando o WebMatrix]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-webmatrix/
  [Usando o Armazenamento do Azure com aplicativos PHP]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh674502(v=vs.103).aspx
  [Usando o Banco de Dados SQL com aplicativos PHP]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh674500(v=vs.103).aspx
  [7]: http://twimlets.com/message
  [Como usar a Twilio para obter recursos de voz e SMS em PHP]: ../partner-twilio-php-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
