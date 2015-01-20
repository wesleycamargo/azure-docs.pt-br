<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Como usar o Twilio para Voz e SMS (Ruby) - Azure" metaKeywords ="Azure Ruby Twilio, chamadas telefônicas Azure, chamadas telefônicas Azure, Azure twilio, Azure SMS, Azure SMS, chamadas de voz Azure, chamadas de voz do Azure, mensagens de texto do Azure, mensagens de texto do Azure" description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escritos no Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/25/2014" ms.author="MicrosoftHelp@twilio.com" />





# Como usar o Twilio para recursos de voz e SMS no Ruby
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas].(#NextSteps) .

## Sumário
* [O que é Twilio?](#WhatIs)
* [Preços do Twilio](#Pricing)
* [Conceitos](#Concepts)
* [Criar uma conta do Twilio](#CreateAccount)
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
As informações sobre os preços do Twilio estão disponíveis em [Preços do Twilio] [twilio_pricing]. Os clientes do Azure recebem uma [oferta especial][special_offer]: um crédito de 1.000 mensagens de texto gratuitas ou 1.000 minutos de entrada. Para se inscrever nessa oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API do Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções em XML que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos do TwiML têm "<Response>" como seu elemento raiz. A partir disso, você usará os verbos do Twilio para definir o comportamento do seu aplicativo.

### <a id="Verbs"></a>Verbos do TwiML
Os verbos do Twilio são marcas XML que dizem ao Twilio o que **fazer**. Por exemplo, o verbo **<Say>** instrui o Twilio a entregar de forma audível uma mensagem em uma chamada. 

A seguir está uma lista de verbos do Twilio.

* **<Dial>**: Conecta o chamador com outro telefone.
* **<Gather>**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **<Hangup>**: Termina uma chamada.
* **<Play>**: Reproduz um arquivo de áudio.
* **<Pause>**: Espera silenciosamente por um número especificado de segundos.
* **<Record>**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **<Redirect>**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
* **<Reject>**: Descarta uma chamada recebida para o número do Twilio sem cobrança
* **<Say>**: Converte o texto em fala que é executada em uma chamada.
* **<Sms>**: Envia uma mensagem SMS.

Para obter mais informações sobre os verbos do Twilio, seus atributos e o TwiML, consulte [TwiML] [twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [API do Twilio] [twilio_api].

## <a id="CreateAccount"></a>Criar uma conta no Twilio
Quando você estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio] [try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Ao se inscrever em uma conta do Twilio, você receberá um número de telefone gratuito para o seu aplicativo. Você também receberá uma SID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. Sua SID de conta e o token de autenticação poderão ser exibidos na [página da conta do Twilio][twilio_account], nos campos rotulados como **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Verificar números telefônicos
Além do número fornecido pelo Twilio, você também pode verificar os números que controla (isto é, seu número do telefone celular ou residencial) para uso em seus aplicativos. 

Para obter informações sobre como verificar um número de telefone, consulte [Gerenciar números] [verify_phone].

## <a id="create_app"></a>Criar um aplicativo Ruby
Um aplicativo Ruby que usa o serviço do Twilio e está em execução no Azure não é diferente de nenhum outro aplicativo Ruby que usa o serviço do Twilio. Embora os serviços do Twilio sejam RESTful e possam ser chamados do Ruby de várias maneiras, este artigo se concentrará em como usar esses serviços com a [biblioteca do auxiliar do Twilio para Ruby][twilio_ruby].

Primeiro, [configure uma nova VM (máquina virtual) do Linux no Azure][azure_vm_setup] para funcionar como um host do seu novo aplicativo web do Ruby. Ignore as etapas que envolvem a criação de um aplicativo do Rails, apenas configure a VM. Certifique-se de criar um ponto de extremidade com uma porta externa 80 e uma porta interna 5000.

Nos exemplos a seguir, usaremos o [Sinatra][sinatra], uma estrutura da web muito simples para Ruby. Mas você certamente pode usar a biblioteca do auxiliar do Twilio para Ruby com qualquer outra estrutura da web, incluindo o Ruby on Rails.

Use SSH na sua nova VM e crie um diretório para o seu novo aplicativo. Dentro desse diretório, crie um arquivo denominado Gemfile e copie o código a seguir dentro dele:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comando, execute "bundle install". Isso instalará as dependências acima. Em seguida, crie uma arquivo denominado "web.rb". Ele será a localização do código do seu aplicativo Web. Cole o código a seguir dentro dele:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Neste ponto, já é possível executar o comando "ruby web.rb -p 5000". Isso irá girar um servidor web pequeno na porta 5000. Você poderá acessar esse aplicativo em seu navegador, visitando a URL configurada para sua VM do Azure. Depois que conseguir acessar seu aplicativo web no navegador, você estará pronto para iniciar a compilação de um aplicativo do Twilio.

## <a id="configure_app"></a>Configurar seu aplicativo para usar o Twilio
Você pode configurar seu aplicativo web para usar a biblioteca do Twilio, atualizando seu "Gemfile" para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comando, execute "bundle install". Agora, abra "web.rb" e inclua esta linha no início:

    require 'twilio-ruby'

Você está configurado para usar a biblioteca do auxiliar do Twilio para Ruby em seu aplicativo Web.

## <a id="howto_make_call"></a>Como: Fazer uma chamada externa
A seguir você verá como fazer uma chamada de saída. Os conceitos principais incluem usar a biblioteca do auxiliar do Twilio para o Ruby para fazer chamadas de API REST e renderizar o TwiML. Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

Adicione esta função a "web.md":

    # Defina seu ID de conta e o token de autenticação.
	sid = "your_twilio_account_sid";
	token = "your_twilio_authentication_token";

	# O número do telefone para iniciar a chamada.
    # Este deve ser um número do Twilio ou um número que você verificou
	from = "NNNNNNNNNNN";

	# O número do telefone para recebimento da chamada.
	to = "NNNNNNNNNNN";

	# Use o site fornecido pelo Twilio para a resposta do TwiML.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
	  # Create the call client.
	  client = Twilio::REST::Client.new(sid, token);
      
      # Faça a chamada
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Se você abrir "http://yourdomain.cloudapp.net/make_call" em um navegador, isso disparará a chamada para a API do Twilio para fazer a chamada telefônica. Os dois primeiros parâmetros em "client.account.calls.create" são autoexplicativos: o número da chamada é "de" e o número da chamada é "para". 

O terceiro parâmetro ("URL") é a URL solicitada pelo Twilio para obter instruções sobre o que fazer quando a chamada estiver conectada. Nesse caso, configuramos uma URL ("http://yourdomain.cloudapp.net") que retorna um documento simples de TwiML e usa o verbo "<Say>" para fazer uma conversão de texto em fala e dizer "Olá" à pessoa que estiver recebendo a chamada.

## <a id="howto_recieve_sms"></a>Como: Receber uma mensagem SMS
No exemplo anterior, iniciamos uma chamada telefônica de **saída**. Agora, vamos usar o número de telefone que o Twilio nos forneceu durante a inscrição para processar uma mensagem SMS de **entrada**.

Primeiro, faça logon no [painel do Twilio][twilio_account]. Clique em "Número" no navegador superior e clique no número do Twilio fornecido. Você verá dois URLs que podem ser configurados. Um URL de solicitação da voz e de SMS. Estes são os URLs que o Twilio chama sempre que uma chamada telefônica é feita ou um SMS é enviado ao seu número. As URLs também são conhecidas como "ganchos da web".

Gostaríamos de processar as mensagens SMS de entrada, portanto, vamos atualizar a URL para "http://yourdomain.cloudapp.net/sms_url". Prossiga e clique em Salvar alterações na parte inferior da página. Agora, de volta para "web.rb", vamos programar o aplicativo para tratar do seguinte:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Após fazer a alteração, certifique-se de reiniciar o aplicativo web. Agora, remova o telefone e envie um SMS ao número do Twilio. Você deve obter imediatamente uma resposta de SMS que informa "Oi, obrigado pelo ping! O Twilio e o Azure são demais!".

## <a id="additional_services"></a>Como: Use os serviços adicionais do Twilio
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [documentação da API do Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Próximas etapas
Agora que você já conhece os princípios do serviço Twilio, acesse estes links para aprender mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Código de exemplo e procedimentos do Twilio] [twilio_howtos]
* [Tutoriais de Início rápido do Twilio][twilio_quickstarts] 
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

<!--HONumber=35.2-->
