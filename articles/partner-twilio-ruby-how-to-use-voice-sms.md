<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Como usar o Twilio para recursos de voz e SMS no Ruby

Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é Twilio?][O que é Twilio?]
-   [Preços do Twilio][Preços do Twilio]
-   [Conceitos][Conceitos]
-   [Criar uma conta no Twilio][Criar uma conta no Twilio]
-   [Criar um aplicativo Ruby Sinatra][Criar um aplicativo Ruby Sinatra]
-   [Configurar seu aplicativo para usar as bibliotecas do Twilio][Configurar seu aplicativo para usar as bibliotecas do Twilio]
-   [Como: Fazer uma chamada externa][Como: Fazer uma chamada externa]
-   [Como: Receber uma mensagem SMS][Como: Receber uma mensagem SMS]
-   [Como: Serviços adicionais do Twilio][Como: Serviços adicionais do Twilio]
-   [Próximas etapas][Próximas etapas]

## <span id="WhatIs"></span></a>O que é Twilio?

Twilio é uma API do serviço Web de telefonia que permite usar os idiomas e habilidades existentes de Web para criar aplicativos de voz e de SMS. O Twilio é um serviço de terceiro (não é um recurso do Azure e não é um produto da Microsoft).

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos façam e recebam mensagens SMS. **Twilio Cliente** permite que seus aplicativos habilitem a comunicação de voz usando as conexões existentes com a Internet, incluindo conexões para celular.

## <span id="Pricing"></span></a>Preços e ofertas especiais do Twilio

A informações sobre os preços do Twilio estão disponíveis em [Preços do Twilio][1]. Os clientes do Azure recebem uma [oferta especial][oferta especial]: um crédito de 1000 mensagens de texto gratuitas e 1000 minutos de entrada. Para se inscrever nessa oferta ou obter mais informações, visite [][oferta especial]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Conceitos

A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][Bibliotecas de API da Twilio].

### <span id="TwiML"></span></a>TwiML

TwiML é um conjunto de instruções em XML que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos do TwiML têm `<Response>` como elemento raiz. A partir disso, você usará os verbos do Twilio para definir o comportamento do seu aplicativo.

### <span id="Verbs"></span></a>Verbos do TwiML

Os verbos do Twilio são marcas XML que dizem ao Twilio o que **fazer**. Por exemplo, o verbo **\<Say\>** instrui o Twilio a entregar de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio.

-   **\<Dial\>**: Conecta o chamador com outro telefone.
-   **\<Gather\>**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
-   **\<Hangup\>**: Termina uma chamada.
-   **\<Play\>**: Reproduz um arquivo de áudio.
-   **\<Pause\>**: Espera silenciosamente por um número especificado de segundos.
-   **\<Record\>**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
-   **\<Redirect\>**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
-   **\<Reject\>**: Descarta uma chamada recebida para o número do Twilio sem cobrança
-   **\<Say\>**: Converte o texto em fala que é executada em uma chamada.
-   **\<Sms\>**: Envia uma mensagem SMS.

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][TwiML]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][API da Twilio].

## <span id="CreateAccount"></span></a>Criar uma conta no Twilio

Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][Avaliar a Twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Ao se inscrever em uma conta do Twilio, você receberá um número de telefone gratuito para o seu aplicativo. Você também receberá uma SID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. Sua SID de conta e o token de autenticação poderão ser exibidos na [página da conta do Twilio][página da conta do Twilio], nos campos rotulados como **SID DE CONTA** e **TOKEN DE AUTENTICAÇÃO**, respectivamente.

### <span id="VerifyPhoneNumbers"></span></a>Verificar números telefônicos

Além do número fornecido pelo Twilio, você também pode verificar os números que controla (isto é, seu número do telefone celular ou residencial) para uso em seus aplicativos.

Para obter informações sobre como verificar um número de telefone, consulte [Gerenciar números][Gerenciar números].

## <span id="create_app"></span></a>Criar um aplicativo Ruby

Um aplicativo Ruby que usa o serviço do Twilio e está em execução no Azure não é diferente de nenhum outro aplicativo Ruby que usa o serviço do Twilio. Embora os serviços do Twilio sejam RESTful e possam ser chamados do Ruby de várias maneiras, este artigo se concentrará em como usar esses serviços com a [biblioteca do auxiliar do Twilio para Ruby][biblioteca do auxiliar do Twilio para Ruby].

Primeiro, [configure uma nova VM do Linux no Azure][configure uma nova VM do Linux no Azure] para funcionar como um host do seu novo aplicativo web do Ruby. Ignore as etapas que envolvem a criação de um aplicativo do Rails, apenas configure a VM. Certifique-se de criar um ponto de extremidade com uma porta externa 80 e uma porta interna 5000.

Nos exemplos a seguir, usaremos o [Sinatra][Sinatra], uma estrutura da web muito simples para Ruby. Mas você certamente pode usar a biblioteca do auxiliar do Twilio para Ruby com qualquer outra estrutura da web, incluindo o Ruby on Rails.

Use SSH na sua nova VM e crie um diretório para o seu novo aplicativo. Dentro desse diretório, crie um arquivo denominado Gemfile e copie o código a seguir dentro dele:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comando executar `bundle install`. Isso instalará as dependências acima. Em seguida, crie uma arquivo denominado `web.rb`. Ele será a localização do código do seu aplicativo Web. Cole o código a seguir dentro dele:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

A esta altura você deve ser capaz de executar o comando `ruby web.rb -p 5000`. Isso formará um servidor Web pequeno na porta 5000. Você poderá acessar esse aplicativo no navegador ao visitar o URL que configurou para a sua VM do Azure. Depois que conseguir acessar seu aplicativo web no navegador, você estará pronto para iniciar a compilação de um aplicativo do Twilio.

## <span id="configure_app"></span></a>Configurar seu aplicativo para usar o Twilio

Você pode configurar seu aplicativo web para usar a biblioteca do Twilio, atualizando seu `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comando executar `bundle install`. Agora, abra `web.rb` e inclua esta linha na parte superior:

    require 'twilio-ruby'

Você está configurado para usar a biblioteca do auxiliar do Twilio para Ruby em seu aplicativo Web.

## <span id="howto_make_call"></span></a>Como: Fazer uma chamada externa

A seguir você verá como fazer uma chamada de saída. Os conceitos principais incluem usar a biblioteca do auxiliar do Twilio para o Ruby para fazer chamadas de API REST e renderizar o TwiML. Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

Adicione esta função a `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Se você abrir `http://yourdomain.cloudapp.net/make_call` em um navegador, isso disparará a chamada para a API do Twilio para fazer a chamada telefônica. Os primeiros dois parâmetros em `client.account.calls.create` são bastante evidentes: o número de `from` da chamada e o número de `to`.

O terceiro parâmetro (`url`) é a URL solicitada pelo Twilio para obter instruções sobre o que fazer quando a chamada estiver conectada. Nesse caso, configuramos uma URL (`http://yourdomain.cloudapp.net`) que retorna um documento simples de TwiML e usa o verbo `<Say>` para fazer uma conversão de texto em fala e dizer "Olá" à pessoa que estiver recebendo a chamada.

## <span id="howto_recieve_sms"></span></a>Como: Receber uma mensagem SMS

No exemplo anterior, iniciamos uma chamada telefônica de **saída**. Agora, vamos usar o número de telefone que o Twilio nos forneceu durante a inscrição para processar uma mensagem SMS de **entrada**.

Primeiro, faça logon no [painel do Twilio][página da conta do Twilio]. Clique em "Número" no navegador superior e clique no número do Twilio fornecido. Você verá dois URLs que podem ser configurados. Um URL de solicitação da voz e de SMS. Estes são os URLs que o Twilio chama sempre que uma chamada telefônica é feita ou um SMS é enviado ao seu número. As URLs também são conhecidas como "ganchos da web".

Gostaríamos de processar as mensagens SMS de entrada, portanto, vamos atualizar a URL para `http://yourdomain.cloudapp.net/sms_url`. Prossiga e clique em Salvar alterações na parte inferior da página. Agora, de volta para `web.rb`, vamos programar o aplicativo para tratar do seguinte:

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Após fazer a alteração, certifique-se de reiniciar o aplicativo web. Agora, remova o telefone e envie um SMS ao número do Twilio. Você deve obter imediatamente uma resposta de SMS que informa "Oi, obrigado pelo ping! O Twilio e o Azure são demais!".

## <span id="additional_services"></span></a>Como: Use os serviços adicionais do Twilio

Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio][API da Twilio].

### <span id="NextSteps"></span></a>Próximas etapas

Agora que você já conhece os princípios do serviço Twilio, acesse estes links para saber mais:

-   [Diretrizes da segurança do Twilio][Diretrizes da segurança do Twilio]
-   [Código de exemplo e HowTo do Twilio][Código de exemplo e HowTo do Twilio]
-   [Tutoriais de início rápido do Twilio][Tutoriais de início rápido do Twilio]
-   [Twilio no GitHub][Twilio no GitHub]
-   [Fale com o suporte do Twilio][Fale com o suporte do Twilio]

  [Próximas etapas]: #NextSteps
  [Preços do Twilio]: #Pricing
  [Conceitos]: #Concepts
  [Criar uma conta no Twilio]: #CreateAccount
  [Criar um aplicativo Ruby Sinatra]: #create_app
  [Configurar seu aplicativo para usar as bibliotecas do Twilio]: #configure_app
  [Como: Fazer uma chamada externa]: #howto_make_call
  [Como: Receber uma mensagem SMS]: #howto_recieve_sms
  [Como: Serviços adicionais do Twilio]: #additional_services
  [1]: http://www.twilio.com/pricing
  [oferta especial]: http://ahoy.twilio.com/azure
  [Bibliotecas de API da Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API da Twilio]: http://www.twilio.com/api
  [Avaliar a Twilio]: https://www.twilio.com/try-twilio
  [página da conta do Twilio]: https://www.twilio.com/user/account
  [Gerenciar números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [biblioteca do auxiliar do Twilio para Ruby]: https://www.twilio.com/docs/ruby/install
  [configure uma nova VM do Linux no Azure]: http://www.windowsazure.com/pt-br/develop/ruby/tutorials/web-app-with-linux-vm/
  [Sinatra]: http://www.sinatrarb.com/
  [Diretrizes da segurança do Twilio]: http://www.twilio.com/docs/security
  [Código de exemplo e HowTo do Twilio]: http://www.twilio.com/docs/howto
  [Tutoriais de início rápido do Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio no GitHub]: https://github.com/twilio
  [Fale com o suporte do Twilio]: http://www.twilio.com/help/contact
