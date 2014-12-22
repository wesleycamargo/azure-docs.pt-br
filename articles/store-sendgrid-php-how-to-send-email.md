<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="Como usar o serviço de email SendGrid (PHP) - Azure" metaKeywords ="SendGrid do Azure, serviço de email do Azure, PHP SendGrid do Azure, email do Azure PHP" description="Learn how send email with the SendGrid email service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />

# Como usar serviço de email SendGrid do PHP

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Microsoft Azure. As amostras são gravadas em PHP.
Os cenários abordados incluem **escrever email**, **enviar email** e **adicionar anexos**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a seção [Próximas etapas][].

## Sumário

-   [O que é o Serviço de E-mail SendGrid][]
-   [Criar uma conta do SendGrid][]
-   [Usando SendGrid de seu aplicativo PHP][]
-   [Como: Enviar um email][]
-   [Como: Adicionar um anexo][]
-   [Como: Usar filtros para habilitar rodapés, rastreamento e análise][]
-   [Próximas etapas][]

## <a name="bkmk_WhatIsSendGrid"> </a>O que é o serviço de email SendGrid?

SendGrid é um [serviço de email baseado em nuvem] que fornece confiável
[entrega de email transacional], escalabilidade e análise em tempo real com APIs flexíveis
que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid
incluem:

-   Envio automático de recibos para os clientes
-   Distribuição de administração de listas para enviar aos clientes mensalmente
    panfletos eletrônicos e ofertas especiais
-   Coleta de métricas em tempo real para email bloqueado e
    resposta ao cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
- Notificações de email a partir de seu aplicativo

Para obter mais informações, consulte [http://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Criar uma conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Usando SendGrid de seu aplicativo PHP

Usando o SendGrid em um aplicativo PHP do Azure não requer 
codificação ou configuração especial. Como o SendGrid é um serviço, ele pode ser
acessado exatamente da mesma forma de um aplicativo em nuvem como ele pode de
um aplicativo local.

## <a name="bkmk_HowToSendEmail"> </a>Como: Enviar um email

Você pode enviar emails usando o SMTP ou a API da Web fornecida pelo
SendGrid.

### API do SMTP

Para enviar email usando a API de SMTP do SendGrid, use *Swift Mailer*, uma
biblioteca baseada em componente para envio de emails de aplicativos PHP. Você
pode baixar a biblioteca *Swift Mailer* de 
[http://swiftmailer.org/download][]. Enviar email com a biblioteca
envolve a criação de instâncias de 
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> e
classes de <span class="auto-style2">Swift\_Message</span>, definindo as
propriedades adequadas e chamando
método <span class="auto-style2">Swift\_Mailer::Send</span>.

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

Use a [função de rotação][] do PHP para enviar email usando a API Web do SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
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

API de Web do SendGrid é muito semelhante a uma API REST, embora
não seja realmente uma API RESTful já que, na maioria das chamadas, GET e POST verbos
podem ser usados alternadamente.

## <a name="bkmk_HowToAddAttachment"> </a>Como: Adicionar um anexo

### API do SMTP

Enviando um anexo usando a API de SMTP envolve uma linha adicional de
código para o script de exemplo para enviar um email com Swift Mailer.

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

Esta linha de código chama o método anexar ao
objeto <span class="auto-style2">Swift\_Message</span> e usa o método
estático <span class="auto-style2">fromPath</span> na
classe <span class="auto-style2">Swift\_Attachment</span> para obter e
anexar um arquivo a uma mensagem.

### API Web

Enviando um anexo usando a API da Web é muito similar ao envio de um
email usando a API da Web. No entanto, observe que no exemplo a seguir,
a matriz de parâmetros deve conter este elemento:

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

    <?php

     $url = 'https://api.sendgrid.com/';
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
         'from' => 'anna@contoso.com',
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
'filtros'. Essas são as configurações que podem ser adicionadas a uma mensagem de email para
habilitar funcionalidades específicas como habilitar acompanhamento de clique, Google
analytics, acompanhamento de assinatura e assim por diante.

Os filtros podem ser aplicados a uma mensagem pela propriedade filters. Cada
filtro é especificado por um hash que possui configurações específicas de filtro. O
exemplo a seguir ativa o filtro do rodapé e especifica uma mensagem de texto
que será acrescentado ao final da mensagem de email:

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
     $from = array('someone@example.com' => 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=>'Personal Name Of Recipient');
     
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
     // SendGrid's asJSON function escapes characters. If you are using Swift Mailer's
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

## <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do serviço de SendGrid Email, acesse
estes links para obter mais informações.

-   Documentação do SendGrid: <https://sendgrid.com/docs>
-   Oferta especial do SendGrid para clientes do Azure: <https://sendgrid.com/windowsazure.html>

  [Próximas etapas]: #bkmk_NextSteps
  [O que é o Serviço de E-mail SendGrid]: #bkmk_WhatIsSendGrid
  [Criar uma conta do SendGrid]: #bkmk_CreateSendGrid
  [Usando SendGrid de seu aplicativo PHP]: #bkmk_UsingSendGridfromPHP
  [Como: Enviar um email]: #bkmk_HowToSendEmail
  [Como: Adicionar um anexo]: #bkmk_HowToAddAttachment
  [Como: Usar filtros para habilitar rodapés, rastreamento e análise]: #bkmk_HowToUseFilters
  [Como: Usar serviços adicionais do SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [oferta especial]: https://www.sendgrid.com/windowsazure.html
  [Empacotamento e implantação de aplicativos do PHP para Microsoft Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [função de rotação]: http://php.net/curl
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
