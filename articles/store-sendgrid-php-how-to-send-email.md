<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="How to use the SendGrid email service (PHP) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid PHP, Azure email PHP" description="Learn how send email with the SendGrid email service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Como usar serviço de email SendGrid do PHP

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Windows Azure. As amostras são gravadas em PHP.
Os cenários abordados incluem **email em construção**, **enviar email** e **adicionar anexos**. Para obter mais informações sobre o SendGrid e o envio de e-mails, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é o Serviço de E-mail SendGrid][O que é o Serviço de E-mail SendGrid]
-   [Criar uma conta do SendGrid][Criar uma conta do SendGrid]
-   [Usando SendGrid de seu aplicativo PHP][Usando SendGrid de seu aplicativo PHP]
-   [Como: Enviar um email][Como: Enviar um email]
-   [Como: Adicionar um anexo][Como: Adicionar um anexo]
-   [Como: Usar filtros para habilitar rodapés, rastreamento e análise][Como: Usar filtros para habilitar rodapés, rastreamento e análise]
-   [Próximas etapas][Próximas etapas]

## <a name="bkmk_WhatIsSendGrid"> </a>O que é o serviço de email SendGrid?

O SendGrid é um [serviço de email baseado em nuvem][serviço de email baseado em nuvem] que oferece
[entrega de email transacional][entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis
que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid
incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais
-   Coleta de métrica em tempo real para, por exemplo, email bloqueado e
    capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
-   Notificações de email a partir de seu aplicativo

Para obter mais informações, consulte [][]<http://sendgrid.com></a>.

## <a name="bkmk_CreateSendGrid"> </a>Criar uma conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up][sendgrid-sign-up]]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Usando o SendGrid de seu aplicativo PHP

O uso do SendGrid em um aplicativo PHP Azure não exige
configuração ou codificação especial. Como o SendGrid é um serviço, ele pode ser
acessado exatamente da mesma maneira de um aplicativo em nuvem ou de
um aplicativo local.

## <a name="bkmk_HowToSendEmail"> </a>Como: Enviar um email

É possível enviar emails usando o SMTP ou a API Web fornecida pelo
SendGrid.

### API do SMTP

Para enviar email usando a API do SMTP do SendGrid, use o *Swift Mailer*, uma
biblioteca baseada em componente para envio de emails de aplicativos PHP. É
possível baixar a biblioteca do *Swift Mailer* em
[][1]<http://swiftmailer.org/download></a>. O envio de email pela biblioteca
envolve a criação de instâncias do
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> e classes do
<span class="auto-style2">Swift\_Message</span>, definição das
propriedades adequadas e a chamada do método do
<span class="auto-style2">SwiftMailer::send</span>.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### API Web

Use a [função de rotação][função de rotação] do PHP para enviar email usando a API Web do SendGrid.

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@sendgrid.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

A API Web do SendGrid é muito semelhante a uma API REST, embora ela não seja
realmente uma API RESTful já que, na maioria das chamadas, ambos os verbos GET e POST
podem ser usados de maneira alternada.

## <a name="bkmk_HowToAddAttachment"> </a>Como: Adicionar um anexo

### API do SMTP

O envio de um anexo usando a API do SMTP envolve uma linha adicional de
código para o script função Web para enviar um email com Swift Mailer.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - "
          print_r($failures);
     }

A linha de código adicional é a seguinte:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

A linha de código chama o método adicionar no objeto
<span class="auto-style2">Swift\_Message</span> e usa o método
estático <span class="auto-style2">fromPath</span> na classe
<span class="auto-style2">SwiftAttachment</span> para obter e
anexar um arquivo a uma mensagem.

### API Web

O envio de um anexo usando a API Web é muito semelhante ao envio de um
email usando a API Web. No entanto, observe que, no exemplo a seguir,
a matriz de parâmetros deve conter este elemento:

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@sendgrid.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="bkmk_HowToUseFilters"> </a>Como: Usar filtros para habilitar rodapés, rastreamento e análise

O SendGrid fornece a funcionalidade adicional de email por meio do uso de
‘filtros’. Essas são as configurações que podem ser adicionadas a uma mensagem de email para
habilitar uma funcionalidade específica, como habilitar rastreamento de clique, Google
analytics, rastreamento de assinatura e assim por diante.

Os filtros podem ser aplicados a uma mensagem pela propriedade filtros. Cada
filtro é especificado por um hash que possui configurações específicas de filtro. O
exemplo a seguir ativa o filtro do rodapé e especifica uma mensagem de texto
que será anexada na parte inferior da mensagem de email:

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from. For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters. If you are using Swift 
     Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong =(
     else
     {
     echo "Something went wrong - ";
     print_r($failures);
     }

## <a name="bkmk_NextSteps"> </a> Próximas etapas

Agora que você já conhece os princípios do serviço de email do SendGrid, acesse
estes links para saber mais.

-   Documentação do SendGrid: <https://sendgrid.com/docs>
-   Oferta especial do SendGrid para clientes do Azure: <http://sendgrid.com/azure.html>

  [Próximas etapas]: #bkmk_NextSteps
  [O que é o Serviço de E-mail SendGrid]: #bkmk_WhatIsSendGrid
  [Criar uma conta do SendGrid]: #bkmk_CreateSendGrid
  [Usando SendGrid de seu aplicativo PHP]: #bkmk_UsingSendGridfromPHP
  [Como: Enviar um email]: #bkmk_HowToSendEmail
  [Como: Adicionar um anexo]: #bkmk_HowToAddAttachment
  [Como: Usar filtros para habilitar rodapés, rastreamento e análise]: #bkmk_HowToUseFilters
  [serviço de email baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de email transacional]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [1]: http://swiftmailer.org/download
  [função de rotação]: http://php.net/curl
