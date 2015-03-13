<properties 
	pageTitle="Como usar o Twilio para voz e SMS (Ruby) - Azure" 
	description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de códigos escritos em Ruby." 
	services="" 
	documentationCenter="ruby" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="MicrosoftHelp@twilio.com"/>





# Como usar o Twilio para recursos de voz e SMS no Ruby
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar um SMS (Serviço de Mensagem Curta). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## Sumário
* [O que é o Twilio?](#WhatIs)
* [Preços do Twilio](#Pricing)
* [Conceitos](#Concepts)
* [Criar uma conta do Twilio](#CreateAccount)
* [Criar um aplicativo Ruby Sinatra](#create_app)
* [Configurar seu aplicativo para usar as bibliotecas do Twilio](#configure_app)
* [Como: Fazer uma chamada externa](#howto_make_call)
* [Como: Receber uma mensagem SMS](#howto_recieve_sms)
* [Como: Serviços adicionais do Twilio](#additional_services)
* [Próximas etapas](#NextSteps)

## <a id="WhatIs"></a>O que é o Twilio?
Twilio é uma API do serviço Web de telefonia que permite usar os idiomas e as habilidades existentes de Web para criar aplicativos de voz e SMS. O Twilio é um serviço de terceiro (não é um recurso do Azure e não é um produto da Microsoft).

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos façam e recebam mensagens SMS. **Twilio Client** permite que seus aplicativos habilitem a comunicação de voz usando as conexões existentes com a Internet, incluindo conexões para celular.

## <a id="Pricing"></a>Preços e ofertas especiais do Twilio
As informações sobre os preços do Twilio estão disponíveis em [Preços do Twilio] [twilio_pricing]. Clientes Azure recebem uma [oferta especial][special_offer]: um crédito de 1000 mensagens de texto gratuitas e 1000 minutos de entrada. Para se inscrever nesta oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API do Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções em XML que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria o texto **Hello World** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos do TwiML têm `<Response>` como seu elemento raiz. Por meio disso, você usará os verbos do Twilio para definir o comportamento do seu aplicativo.

### <a id="Verbs"></a>Verbos do TwiML
Os verbos do Twilio são marcas XML que dizem ao Twilio o que **fazer**. Por exemplo, o verbo **&lt;Say&gt;** instrui o Twilio a entregar de forma audível uma mensagem em uma chamada. 

Veja, a seguir, uma lista de verbos do Twilio.

* **&lt;Dial&gt;**: Conecta o chamador com outro telefone.
* **&lt;Gather&gt;**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Play&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
* **&lt;Reject&gt;**: Descarta uma chamada recebida para o número do Twilio sem cobrança
* **&lt;Say&gt;**: Converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

Para obter mais informações sobre os verbos do Twilio, seus atributos e o TwiML, consulte [TwiML] [twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [API do Twilio] [twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando você estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio] [try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Ao se inscrever em uma conta do Twilio, você receberá um número de telefone gratuito para o seu aplicativo. Você também receberá uma SID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. O SID da conta e o token de autenticação são visíveis no [página da conta do Twilio][twilio_account], nos campos rotulados **SID DA CONTA** e **TOKEN DE AUTENTICAÇÃO**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Verificar números telefônicos
Além do número fornecido pelo Twilio, você também pode verificar os números que controla (isto é, seu número do telefone celular ou residencial) para uso em seus aplicativos. 

Para obter informações sobre como verificar um número de telefone, consulte [Gerenciar números] [verify_phone].

## <a id="create_app"></a>Criar um aplicativo Ruby
Um aplicativo Ruby que usa o serviço do Twilio e está em execução no Azure não é diferente de nenhum outro aplicativo Ruby que usa o serviço do Twilio. Enquanto os serviços do Twilio são RESTful e podem ser chamados do Ruby de várias maneiras, este artigo concentra-se em como usar esses serviços com a [Biblioteca auxiliar de Twilio para Ruby][twilio_ruby].

Primeiro, [configure uma nova VM Linux Azure][azure_vm_setup] para agir como um host para o novo aplicativo Web do Ruby. Ignore as etapas que envolvem a criação de um aplicativo do Rails, apenas configure a VM. Certifique-se de criar um ponto de extremidade com uma porta externa 80 e uma porta interna 5000.

Nos exemplos a seguir, vamos usar [Sinatra][sinatra], uma estrutura muito simples da web para Ruby. Mas você certamente pode usar a biblioteca do auxiliar do Twilio para Ruby com qualquer outra estrutura da web, incluindo o Ruby on Rails.

Use SSH na sua nova VM e crie um diretório para o seu novo aplicativo. Dentro desse diretório, crie um arquivo denominado Gemfile e copie o código a seguir dentro dele:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comando execute `bundle install`. Isso instalará as dependências acima. Em seguida, crie um arquivo chamado `web.rb`. Ele será a localização do código do seu aplicativo Web. Cole o código a seguir dentro dele:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Neste ponto, já é possível executar o comando `ruby web.rb -p 5000`. Isso vai girar um servidor Web pequeno na porta 5000. Você poderá acessar esse aplicativo em seu navegador, visitando a URL configurada para sua VM do Azure. Depois que conseguir acessar seu aplicativo web no navegador, você estará pronto para iniciar a compilação de um aplicativo do Twilio.

## <a id="configure_app"></a>Configurar seu aplicativo para usar o Twilio
Você pode configurar seu aplicativo web para usar a biblioteca Twilio atualizando o  `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comando, execute `bundle install`. Agora, abra `web.rb` e inclua esta linha na parte superior:

    require 'twilio-ruby'

Você está configurado para usar a biblioteca do auxiliar do Twilio para Ruby em seu aplicativo Web.

## <a id="howto_make_call"></a>Como: Fazer uma chamada externa
A seguir você verá como fazer uma chamada de saída. Os conceitos principais incluem usar a biblioteca do auxiliar do Twilio para o Ruby para fazer chamadas de API REST e renderizar o TwiML. Substitua os valores dos números telefônico **De** e **Para** e verifique o número telefônico **De** de sua conta do Twilio antes de executar o código.

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
    
Se você reabrir `http://yourdomain.cloudapp.net/make_call` em um navegador, isso disparará a chamada de API do Twilio para fazer a chamada telefônica. Os dois primeiros parâmetros em `client.account.calls.create` são auto-explicativos: o número da chamada é `from` e o número de chamada é `to`. 

O terceiro parâmetro (`url`) é a URL solicitada pelo Twilio para obter instruções sobre o que fazer quando a chamada estiver conectada. Nesse caso, configuramos uma URL (`http://yourdomain.cloudapp.net`) que retorna um documento simples de TwiML e usa o verbo `<Say>` verbo para fazer algum texto para fala e dizer "Hello Monkey" à pessoa que recebe a chamada.

## <a id="howto_recieve_sms"></a>Como: Receber uma mensagem SMS
No exemplo anterior, iniciamos uma chamada telefônica de **saída**. Agora, vamos usar o número de telefone que o Twilio nos forneceu durante a inscrição para processar uma mensagem SMS de **entrada**.

Primeiro, faça logon no seu [painel do Twilio][twilio_account]. Clique em "Número" no navegador superior e clique no número do Twilio fornecido. Você verá dois URLs que podem ser configurados. Um URL de solicitação da voz e de SMS. Estes são os URLs que o Twilio chama sempre que uma chamada telefônica é feita ou um SMS é enviado ao seu número. As URLs também são conhecidas como "ganchos da web".

Gostaríamos de processar mensagens SMS de entrada, portanto, vamos atualizar a URL para `http://yourdomain.cloudapp.net/sms_url`. Prossiga e clique em Salvar alterações na parte inferior da página. Agora, de volta em `web.rb` vamos programar o aplicativo para lidar com isso:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Após fazer a alteração, certifique-se de reiniciar o aplicativo web. Agora, remova o telefone e envie um SMS ao número do Twilio. Você deve obter imediatamente uma resposta de SMS que informa "Oi, obrigado pelo ping! O Twilio e o Azure são demais!".

## <a id="additional_services"></a>Como: Usar os serviços adicionais do Twilio
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Próximas etapas
Agora que você já conhece os princípios do serviço Twilio, acesse estes links para aprender mais:

* [Diretrizes de segurança do Twilio] [twilio_security_guidelines]
* [Código de exemplo e procedimentos do Twilio] [twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts] 
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
[azure_vm_setup]: http://azure.microsoft.com/develop/ruby/tutorials/web-app-with-linux-vm/

<!--HONumber=45-->
