<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="Como usar o serviço de email SendGrid (PHP) - Azure"metaKeywords ="Azure SendGrid, Azure email service, Azure SendGrid PHP, Azure email PHP" description="Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />

# Como usar serviço de email SendGrid do PHP

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Windows Azure. As amostras são gravadas em PHP.
Os cenários abordados incluem **escrever e-mail**, **enviar e-mail** e **adicionar anexos**. Para obter mais informações sobre o SendGrid e o envio de e-mails, consulte a seção [Próximas etapas][].

## Sumário

-   [O que é o serviço de email SendGrid][]
-   [Criar uma conta do SendGrid][]
-   [Usando SendGrid de seu aplicativo PHP][]
-   [Como: Enviar um email][]
-   [Como: Adicionar um anexo][]
-   [Como: Usar filtros para habilitar rodapés, rastreamento e análise][]
-   [Próximas etapas][]

## <a name="bkmk_WhatIsSendGrid"> </a>O que é o serviço de email SendGrid?

O SendGrid é um [serviço de e-mail baseado em nuvem] que oferece [entrega de e-mail transacional], escalabilidade e análise em tempo real confiáveis com APIsflexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

-   Envio automático de recibos para os clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente panfletos eletrônicos e ofertas especiais
-   Coleta de métrica em tempo real para, por exemplo, email bloqueado e capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar as tendências
-   Encaminhamento de consultas dos clientes
- Notificações de email a partir de seu aplicativo

Para obter mais informações, consulte [https://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Criar uma conta do SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Usando SendGrid de seu aplicativo PHP

O uso do SendGrid em um aplicativo PHP do Azure não exige
codificação ou configuração especial. Como o SendGrid é um serviço, ele pode ser
acessados exatamente da mesma forma de um aplicativo em nuvem ou de
um aplicativo local.

## <a name="bkmk_HowToSendEmail"> </a>Como: Enviar um email

É possível enviar emails usando o SMTP ou a API Web fornecida pelo
Sendgrid.

### API do SMTP

Para enviar email usando a API do SMTP do SendGrid, use o *Swift Mailer*, uma biblioteca baseada em componente para envio de emails de aplicativos PHP. Você pode baixar a biblioteca do *Swift Mailer* em [http://swiftmailer.org/download][] v5.3.0 (use o [Compositor] para instalar o Swift Mailer). O envio de email pela biblioteca envolve a criação de instâncias das classes <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_Mailer</span>e <span class="auto-style2">Swift\_Message</span> , definindo as propriedades adequadas e chamar o método <span class="auto-style2">Swift\_Mailer::send</span> .

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
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

A API Web do SendGrid é muito semelhante a uma API REST, embora ela não seja realmente uma API RESTful já que, na maioria das chamadas, ambos os verbos GET e POST podem ser usados de maneira alternada.

## <a name="bkmk_HowToAddAttachment"> </a>Como: Adicionar um anexo

### API do SMTP

O envio de um anexo usando a API do SMTP envolve uma linha adicional de código para o script de exemplo para enviar um email com Swift Mailer.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

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
          echo "Something went wrong - ";
          print_r($failures);
     }

A linha de código adicional é a seguinte:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

A linha do código chama o método adicionar no objeto <span class="auto-style2">Swift\_Message</span> e usa o método estático <span class="auto-style2">fromPath</span> na classe <span class="auto-style2">Swift\_Attachment</span> para obter e anexar um arquivo a uma mensagem.

### API Web

O envio de um anexo usando a API Web é muito semelhante ao envio de um email usando a API Web. No entanto, observe que, no exemplo a seguir,a matriz de parâmetros deve conter este elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Exemplo:

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

O SendGrid fornece a funcionalidade adicional de email por meio do uso de 'filtros'. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar rastreamento de cliques, Google analytics, rastreamento de assinatura e assim por diante.

Os filtros podem ser aplicados a uma mensagem pela propriedade filtros. Cada filtro é especificado por um hash que possui configurações específicas de filtro. O exemplo a seguir ativa o filtro do rodapé e especifica uma mensagem de texto que será anexada na parte inferior da mensagem de email: Para este exemplo usaremos [biblioteca de sendgrid php]. Use o [Compositor] para instalar a biblioteca:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Exemplo:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Crie o corpo da mensagem (uma com texto simples e uma com versão em HTML). 
     # texto é o seu email de texto sem formatação 
     # html é a versão html do email
     # se o receptor for capaz de visualizar html, então somente o e-mail html
     # será exibido

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você já conhece os princípios do serviço de email do SendGrid, acesse
estes links para obter mais informações.

-   Documentação do SendGrid: <https://sendgrid.com/docs>
-   Biblioteca de SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
-   Oferta especial do SendGrid para clientes do Azure: <https://sendgrid.com/windowsazure.html>

  [Próximas etapas]: #bkmk_NextSteps
  [O que é o serviço de email SendGrid]: #bkmk_WhatIsSendGrid
  [Criar uma conta do SendGrid]: #bkmk_CreateSendGrid
  [Usando SendGrid de seu aplicativo PHP]: #bkmk_UsingSendGridfromPHP
  [Como: Enviar um email]: #bkmk_HowToSendEmail
  [Como: Adicionar um anexo]: #bkmk_HowToAddAttachment
  [Como: Usar filtros para habilitar rodapés, rastreamento e análise]: #bkmk_HowToUseFilters
  [Como: Usar serviços adicionais do SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [oferta especial]: https://www.sendgrid.com/windowsazure.html
  [Empacotamento e implantação de aplicativos do PHP para Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [função de rotação]: http://php.net/curl
  [serviço de e-mail baseado em nuvem]: https://sendgrid.com/email-solutions
  [entrega de e-mail transacional]: https://sendgrid.com/transactional-email
  [biblioteca de sendgrid php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Compositor]: https://getcomposer.org/download/

<!--HONumber=35.2-->
