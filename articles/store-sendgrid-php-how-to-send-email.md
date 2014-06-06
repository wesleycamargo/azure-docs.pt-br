<properties title="Como usar o serviço de e-mail SendGrid (PHP) - Azure" pageTitle="como usar o serviço de e-mail SendGrid (PHP) - Azure" metaKeywords="SendGrid do Azure, serviço de e-mail do Azure, SendGrid PHP do Azure, e-mail PHP do Azure" description="Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Amostras de código gravadas no PHP." documentationCenter="PHP" services="" />

# Como usar o serviço de e-mail SendGrid do PHP

Este guia demonstra como executar tarefas comuns de programação com o serviço de e-mail SendGrid no Azure. As amostras são gravadas em PHP.
Os cenários abordados incluem **escrever e-mail**, **enviar e-mail** e **adicionar anexos**. Para obter mais informações sobre o SendGrid e o envio de e-mails, consulte a seção [Próximas etapas][].

## Sumário

-   [O que é o Serviço de E-mail SendGrid][]
-   [Criar uma conta SendGrid][]
-   [Usando SendGrid de seu aplicativo PHP][]
-   [Como: enviar um e-mail][]
-   [Como: adicionar um anexo][]
-   [Como: usar filtros para permitir rodapés, controle e análises][]
-   [Como: usar serviços adicionais do SendGrid][]
-   [Próximas etapas][]

## <a name="bkmk_WhatIsSendGrid"> </a>O que é o Serviço de E-mail SendGrid?

O SendGrid é um [serviço de e-mail baseado em nuvem] que oferece
[entrega de e-mail transacional], escalabilidade e análises em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Cenários comuns de uso SendGrid
incluem:

-   Automaticamente enviar recibos para clientes
-   Administração de listas de distribuição para enviar aos clientes mensalmente
    e-panfletos e ofertas especiais
-   Coleta de métricas em tempo real para coisas como e-mail bloqueado e
    capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar tendências
-   Encaminhamento de consultas dos clientes
-   Notificações de e-mail a partir de seu aplicativo

Para obter mais informações, consulte [http://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Criar uma conta SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Usando SendGrid de seu aplicativo PHP

O uso do SendGrid em um aplicativo PHP do Azure não exige
configuração ou codificação especial. Como o SendGrid é um serviço, ele pode ser
acessado exatamente da mesma maneira de um aplicativo em nuvem ou de
um aplicativo local.

Depois de adicionar o suporte de e-mail ao seu aplicativo, você pode empacotar e implantar seu aplicativo seguindo os métodos descritos aqui:
[Empacotamento e implantação de aplicativos PHP para Azure][].

## <a name="bkmk_HowToSendEmail"> </a>Como: enviar um e-mail

Você pode enviar e-mails usando o SMTP ou a API da Web fornecida pelo
SendGrid. Para obter detalhes sobre os benefícios e as diferenças de cada API, consulte
[SMTP x API da Web][] na documentação do SendGrid.

### API do SMTP

Para enviar e-mail usando a API do SMTP do SendGrid, use o *Swift Mailer*, uma biblioteca baseada em componente para envio de e-mails de aplicativos PHP. Você
pode baixar a biblioteca do *Swift Mailer* em
[http://swiftmailer.org/download][]. O envio de e-mail pela biblioteca
envolve a criação de instâncias do
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> e classes do
<span class="auto-style2">Swift\_Message</span>, definição de
propriedades adequadas e a chamada do método do
<span class="auto-style2">Swift\_Mailer::send</span>.

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

**Observação:**o script de exemplo acima foi obtido na documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/][].

Para obter mais informações sobre a API de SMTP e o cabeçalho X-SMTPAPI, consulte o guia de desenvolvedor da API do SMTP API na documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/][].
Para obter mais exemplos de uso da API do SMTP com o PHP, consulte a documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/api/smtp-api/php-example/][].

### API da Web

Use a [função de rotação][] dos PHPs para enviar e-mail usando a API da Web do SendGrid.

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

**Observação:**o script de exemplo acima foi obtido na documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/][].

**Observação:**a API da Web do SendGrid é muito semelhante a uma API REST, embora ela não seja realmente uma API RESTful já que, na maioria das chamadas, ambos os verbos GET e POST podem ser usados de maneira alternada.

Para obter uma visão geral da API da Web, consulte a documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/api/web-api/][].

Para obter uma lista completa dos parâmetros e exemplos genéricos para API de envio de e-mail, consulte [http://docs.sendgrid.com/documentation/api/web-api/mail/][].

## <a name="bkmk_HowToAddAttachment"> </a>Como: adicionar um anexo

### API do SMTP

O envio de um anexo usando a API de SMTP envolve uma linha adicional de código para o script de exemplo para enviar um e-mail com Swift Mailer.

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

**Observação:**o script de exemplo acima foi obtido na documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/][].

A linha de código adicional é a seguinte:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName(ï¿½file_nameï¿½));

A linha do código chama o método adicionar no objeto
<span class="auto-style2">Swift\_Message</span> e usa o método
estático <span class="auto-style2">fromPath</span> na classe
<span class="auto-style2">Swift\_Attachment</span> para obter e
anexar um arquivo a uma mensagem.

### API da Web

O envio de um anexo usando a API da Web é muito semelhante ao envio de um e-mail usando a API da Web. No entanto, observe que, no exemplo a seguir, a matriz de parâmetros deve conter este elemento:

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

**Observação:**o script de exemplo acima foi obtido na documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/][].

## <a name="bkmk_HowToUseFilters"> </a>Como: usar filtros para permitir rodapés, controle e análises

SendGrid fornece a funcionalidade adicional de e-mail pelo uso de ï¿½filtersï¿½. Essas são as configurações que podem ser adicionadas a uma mensagem de e-mail para habilitar uma funcionalidade específica, como habilitar rastreamento de clique, Google analytics, rastreamento de assinatura e assim por diante. Para obter a lista completa de filtros,
consulte [Configurações do filtro][].

Os filtros podem ser aplicados a uma mensagem pela propriedade filtros. Cada
filtro é especificado por um hash que possui configurações específicas de filtro. O
exemplo a seguir ativa o filtro do rodapé e especifica uma mensagem de texto
que será anexada à parte inferior da mensagem de e-mail:

    <?php
     /*
      * Este exemplo é usado para Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // A lista de endereços para qual esta mensagem deve ser enviada
     // [Esta lista é usada para enviar vários e-mails usando apenas UMA solicitação para o SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Especificar os nomes dos destinatários
     $nameList = array('Name 1', 'Name 2');
     
     // Usado como um exemplo de substituição de variáveis
     $timeList = array('4 PM', '5 PM');
     
     // Definir todas as variáveis acima
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Especificar que esta é uma mensagem de contato inicial
     $hdr->setCategory("initial");
     
     // Opcionalmente, você pode configurar filtros individuais aqui, neste exemplo, habilitamos o filtro do rodapé
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Obrigado por seu negócio");
     
     // O assunto do e-mail
     $subject = 'Exemplo de e-mail SendGrid';
     
     // De onde essa mensagem é proveniente. Por exemplo, essa mensagem pode ser de support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Nome da sua empresa');
     
     // Se você não especificar uma lista de remetentes acima, você pode especificar aqui o usuário. Se 
     / / uma lista de remetente IS for especificada acima, esse endereço de e-mail se torna irrelevante.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Crie o corpo da mensagem (uma com texto simples e uma com versão em HTML). 
     # texto é o seu e-mail de texto sem formatação 
     # html é a versão html do e-mail
     # se o receptor for capaz de visualizar html, então somente o e-mail html
     # será exibido
     
     /*
     * Observe a substituição de variável aqui =)
     */
     $text = <<<EOM 
     Olá - nome-,
     Obrigado por seu interesse em nossos produtos. Nós combinamos de ligar às - hora - EST para discutir suas necessidades detalhadamente.
     Atenciosamente,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Olá -nome-,<br>
     Obrigado por seu interesse em nossos produtos. Nós combinamos de
     ligar às - hora - EST para discutir suas necessidades detalhadamente.
     
     Atenciosamente,
     
     Fred, como vai?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Suas credenciais da conta do SendGrid
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Criar nova conexão rápida e autenticar
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Criar uma mensagem (assunto)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****Observação importante*****
     // Caracteres de escape da função asJSON do SendGrid. Se você estiver usando as Funções de mensageiro do PHP do Swift Mailer,
     // a função getTextHeader também terá escape de caracteres.
     // Isso pode interromper o filtro.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // anexar o corpo do e-mail
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // enviar mensagem
     if ($recipients = $swift->send($message, $failures))
     {
     // Isso nos permitirá saber quantos usuários receberam esta mensagem
     // Se especificamos os nomes no cabeçalho X-SMTPAPI, isso sempre será 1.
     echo 'Mensagem enviada para '.$recipients.' users';
     }

     // algo deu errado =(
     else
     {
     echo "Algo deu errado - ";
     print_r($failures);
     }

**Observação:**o script de exemplo acima foi obtido na documentação do SendGrid aqui:
[http://docs.sendgrid.com/documentation/api/smtp-api/php-example/][].

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Como: usar serviços adicionais do SendGrid

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo Azure. Para obter detalhes completos, consulte a [documentação da API do SendGrid][].

## <a name="bkmk_NextSteps"> </a>Próximas etapas

Agora que você já conhece os princípios do serviço de e-mail SendGrid, acesse estes links para saber mais.

-   Documentação da API do SendGrid: <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial do SendGrid para clientes do Azure:
    [http://sendgrid.com/azure.html][]

  [Próximas etapas]: #bkmk_NextSteps
  [O que é o Serviço de E-mail SendGrid]: #bkmk_WhatIsSendGrid
  [Criar uma conta SendGrid]: #bkmk_CreateSendGrid
  [Usando SendGrid de seu aplicativo PHP]: #bkmk_UsingSendGridfromPHP
  [Como: enviar um e-mail]: #bkmk_HowToSendEmail
  [Como: adicionar um anexo]: #bkmk_HowToAddAttachment
  [Como: usar filtros para permitir rodapés, controle e análises]: #bkmk_HowToUseFilters
  [Como: usar serviços adicionais do SendGrid]: #bkmk_HowToUseAdditionalSvcs

  [http://sendgrid.com]: http://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [oferta especial]: http://www.sendgrid.com/azure.html
  [http://docs.sendgrid.com/documentation/get-started/]: http://docs.sendgrid.com/documentation/get-started/
  [http://sendgrid.com/features]: http://sendgrid.com/features
  [Empacotamento e implantação de aplicativos PHP para Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh674499(v=VS.103).aspx
  [SMTP x API da Web]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-email-example-using-smtp/
  [http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/]: http://docs.sendgrid.com/documentation/api/smtp-api/developers-guide/
  [http://docs.sendgrid.com/documentation/api/smtp-api/php-example/]: http://docs.sendgrid.com/documentation/api/smtp-api/php-example/
  [função de rotação]: http://php.net/curl
  [http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/php-example-using-the-web-api/
  [http://docs.sendgrid.com/documentation/api/web-api/]: http://docs.sendgrid.com/documentation/api/web-api/
  [http://docs.sendgrid.com/documentation/api/web-api/mail/]: http://docs.sendgrid.com/documentation/api/web-api/mail/
  [Configurações do filtro]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [Documentação da API do SendGrid]: http://docs.sendgrid.com/documentation/api/
  [http://sendgrid.com/azure.html]: http://sendgrid.com/azure.html
  [serviço de e-mail baseado em nuvem]: http://sendgrid.com/solutions
  [entrega de e-mail transacional]: http://sendgrid.com/transactional-email

