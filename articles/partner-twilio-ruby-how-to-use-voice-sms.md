<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Como usar o Twilio para voz e SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, chamadas telefônicas do Azure, Azure twilio, Azure SMS, chamadas de voz do Azure, chamadas de voz do azure, mensagens de texto do Azure" description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de códigos escritos em Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />





# Como usar o Twilio para recursos de voz e SMS no Ruby
Este guia demonstra como executar tarefas comuns de programação com o serviço de API da Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## Sumário
* [O que é Twilio?](#WhatIs)
* [Preços do Twilio](#Pricing)
* [Conceitos](#Concepts)
* [Criar uma conta no Twilio](#CreateAccount)
* [Criar um aplicativo Ruby Sinatra](#create_app)
* [Configurar seu aplicativo para usar as bibliotecas do Twilio](#configure_app)
* [Como: Fazer uma chamada externa](#howto_make_call)
* [Como: Receber uma mensagem SMS](#howto_recieve_sms)
* [Como: Serviços adicionais do Twilio](#additional_services)
* [Próximas etapas](#NextSteps)

## <a id="WhatIs"></a>O que é Twilio?
Twilio é uma API do serviço Web de telefonia que permite usar os idiomas e habilidades existentes de Web para criar aplicativos de voz e de SMS. O Twilio é um serviço de terceiro (não é um recurso do Azure e não é um produto da Microsoft).

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos façam e recebam mensagens SMS. **Twilio Cliente** permite que seus aplicativos habilitem a comunicação de voz usando as conexões existentes com a Internet, incluindo conexões para celular.

## <a id="Pricing"></a>Preços e ofertas especiais do Twilio
A informações sobre os preços do Twilio estão disponíveis em [Preços do Twilio] [twilio_pricing]. Os clientes do Azure recebem uma [oferta especial][special_offer]: um crédito de 1000 mensagens de texto gratuitas e 1000 minutos de entrada. Para se inscrever nessa oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas do cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas da API do Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções em XML que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** para fala.

    ?xml version="1.0" encoding="UTF-8" ?
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos do TwiML têm `<Response>` como elemento raiz. A partir disso, você usa verbos do Twilio para definir o comportamento de seu aplicativo.

### <a id="Verbs"></a>Verbos do TwiML
Os verbos do Twilio são marcas de XML que dizem ao Twilio o que **fazer**. Por exemplo, o verbo **&lt;Say&gt;** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada. 

A seguir está uma lista de verbos do Twilio.

* **&lt;Dial&gt;**: Conecta o chamador com outro telefone.
* **&lt;Gather&gt;**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Play&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para TwiML em um URL diferente.
* **&lt;Reject&gt;**: Descarta uma chamada recebida para o número do Twilio sem cobrar você
* **&lt;Say&gt;**: Converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

Para obter mais informações sobre os verbos do Twilio, seus atributos e o TwiML, consulte [TwiML] [twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [API do Twilio] [twilio_api].

## <a id="CreateAccount"></a>Criar uma conta no Twilio
Quando você estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio] [try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever em uma conta do Twilio, obterá um número telefônico gratuito para seu aplicativo. Você também receberá uma SID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. Sua conta SID e o token de autenticação poderão ser exibidos [na página da conta do Twilio][twilio_account], nos campos rotulados **CONTA SID** e **TOKEN DE AUTENTICAÇÃO**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Verificar números telefônicos
Além do número fornecido pelo Twilio, você também pode verificar os números que controla (isto é, seu número do telefone celular ou residencial) para uso em seus aplicativos. 

Para obter informações sobre como verificar um número telefônico, consulte [Gerenciar números] [verify_phone].

## <a id="create_app"></a>Criar um aplicativo Ruby
Um aplicativo Ruby que usa o serviço do Twilio e está em execução no Azure não é diferente de nenhum outro aplicativo Ruby que usa o serviço do Twilio. Quando os serviços do Twilio são RESTful e podem ser chamados no Ruby de várias maneiras. Este artigo manterá o foco em como usar os serviços do Twilio com [a biblioteca do auxiliar de Twilio para o Ruby][twilio_ruby].

Primeiro, [configure uma nova VM do Linux no Azure][azure_vm_setup] para atuar como um host em seu novo aplicativo Web do Ruby. Ignore as etapas que envolvem a criação de um aplicativo do Rails, apenas configure a VM. Certifique-se de criar um ponto de extremidade externo de 80 e uma porta interna de 5000.

Nos exemplos a seguir, usaremos o [Sinatra][sinatra], uma estrutura muito simples da Web para o Ruby. Mas você certamente pode usar a biblioteca do auxiliar de Twilio para o Ruby com qualquer outra estrutura da Web, incluindo o Ruby on Rails.

Use SSH em sua nova VM e crie um diretório para seu novo aplicativo. Dentro desse diretório, crie um arquivo chamado Gemfile e copie o seguinte código nele:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comando, execute a `instalação em pacote`. Isso instalará as dependências acima. Em seguida, crie uma arquivo denominado `web.rb`. Ele será a localização do código do seu aplicativo Web. Cole o seguinte código nele:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Nesse ponto, já é possível executar o comando `ruby web.rb -p 5000`. Isso formará um servidor Web pequeno na porta 5000. Você poderá acessar esse aplicativo no navegador ao visitar o URL que configurou para a sua VM do Azure. Depois que você puder acessar seu aplicativo Web no navegador, você estará pronto para iniciar compilar um aplicativo do Twilio.

## <a id="configure_app"></a>Configurar seu aplicativo para usar o Twilio
Você pode configurar seu aplicativo Web para usar a biblioteca do Twilio atualizando seu `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comando, execute a `instalação em pacote`. Abra o `web.rb` agora e inclua esta linha na parte superior:

    require 'twilio-ruby'

Você está configurado para usar a biblioteca do auxiliar do Twilio para Ruby em seu aplicativo Web.

## <a id="howto_make_call"></a>Como: Fazer uma chamada externa
O seguinte mostra como fazer uma chamada externa. Os conceitos principais incluem usar a biblioteca do auxiliar do Twilio para o Ruby para fazer chamadas de API REST e renderizar o TwiML. Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

Adicione esta função em `web.md`:

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
    
Se você abrir `http://yourdomain.cloudapp.net/make_call` em um navegador, isso disparará a chamada à API do Twilio para fazer a chamada telefônica. Os primeiros dois parâmetros em `client.account.calls.create` são bastante evidentes: o número de `origem` da chamada e o número de `destino`. 

O terceiro parâmetro (`url`) é o URL que o Twilio solicita para obter instruções sobre o que fazer quando a chamada estiver conectada. Nesse caso, configuramos um URL (`http://yourdomain.cloudapp.net`) que retorna um documento simples de TwiML e usa o verbo `<Say>` para fazer tornar texto em fala e dizer "Olá" à pessoa que recebe a chamada.

## <a id="howto_recieve_sms"></a>Como: Receber uma mensagem SMS
No exemplo anterior, iniciamos uma chamada telefônica **externa**. Agora, vamos usar o número telefônico que o Twilio nos forneceu durante a inscrição para processar uma **mensagem SMS** de entrada.

Primeiro, faça logon no [painel do Twilio][twilio_account]. Clique em "Número" no navegador superior e clique no número do Twilio fornecido. Você verá dois URLs que podem ser configurados. Um URL de solicitação da voz e de SMS. Estes são os URLs que o Twilio chama sempre que uma chamada telefônica é feita ou um SMS é enviado ao seu número. Os URLs também são conhecidos como "ganchos da Web".

Nós gostaríamos de processar mensagens SMS de entrada, portanto, vamos atualizar o URL para `http://yourdomain.cloudapp.net/sms_url`. Prossiga e clique em Salvar alterações na parte inferior da página. Agora, de volta em `web.rb`, vamos programar o aplicativo para gerenciar o seguinte:

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Depois da alteração, certifique-se de reiniciar o aplicativo Web. Agora, remova o telefone e envie um SMS ao número do Twilio. Você deve obter imediatamente uma resposta de SMS que informa "Oi, obrigado pelo ping! O Twilio e Azure são demais!".

## <a id="additional_services"></a>Como: Use os serviços adicionais do Twilio
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [documentação da API do Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Próximas etapas
Agora que você já conhece os princípios do serviço Twilio, acesse estes links para saber mais:

* [Diretrizes da segurança do Twilio] [twilio_security_guidelines]
* [Código de exemplo e HowTo do Twilio] [twilio_howtos]
* [Tutoriais Guia de início rápido do Twilio][twilio_quickstarts] 
* [Twilio no GitHub] [twilio_on_github]
* [Fale com o suporte do Twilio] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/pt-br/develop/ruby/tutorials/web-app-with-linux-vm/
