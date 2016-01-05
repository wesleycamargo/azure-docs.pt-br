<properties
	pageTitle="Hospedar um site Ruby on Rails em uma VM do Linux | Microsoft Azure"
	description="Configurar e hospedar um site da Web baseado no Ruby on Rails no Azure usando uma máquina virtual do Linux."
	services="virtual-machines"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="11/20/2015"
	ms.author="robmcm"/>





#Aplicativo Web Ruby on Rails Web em uma VM do Azure

Esse tutorial descreve como hospedar um site Ruby on Rails no Azure usando uma máquina virtual do Linux.

Este tutorial foi validado usando o Ubuntu Server 14.04 LTS. Se você usar uma distribuição Linux diferente, talvez seja necessário modificar as etapas para instalar o Rails.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


## Criar uma VM do Azure

Comece criando uma VM do Azure com uma imagem do Linux.

Para criar a VM, você pode usar o portal clássico do Azure ou a CLI (interface de linha de comando) do Azure.

### Portal de Gerenciamento do Azure

1. Entre no [portal clássico do Azure](http://manage.windowsazure.com)
2. Clique em **Novo** > **Calcular** > **Máquina Virtual** > **Criação Rápida**. Selecione uma imagem do Linux.
3. Digite uma senha.

Depois que a VM for provisionada, clique no nome da VM e clique em **Painel**. Localize o ponto de extremidade do SSH, listado sob **detalhes de SSH**.

### CLI do Azure

Siga as etapas em [Criar uma máquina virtual que execute o Linux][vm-instructions].

Depois que a VM for provisionada, você pode obter o ponto de extremidade do SSH executando o seguinte comando:

	azure vm endpoint list <vm-name>  

## Instalar o Ruby on Rails

1. Use SSH para conectar-se à VM.

2. Na sessão de SSH, use os seguintes comandos para instalar o Ruby na VM:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	A instalação pode levar alguns minutos. Quando for concluído, use o seguinte comando para verificar se o Ruby está instalado:

		ruby -v

	Isso retorna a versão do Ruby que foi instalada.

3. Use o comando a seguir para instalar o Rails:

		sudo gem install rails --no-rdoc --no-ri

	Use-sinalizadores --no-rdoc e --no-ri para instalar a documentação, o que é mais rápido.

## Criar e executar um aplicativo

Enquanto ainda estiver conectado via SSH, execute os seguintes comandos:

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

O comando [new](http://guides.rubyonrails.org/command_line.html#rails-new) cria um novo aplicativo Rails. O comando [server](http://guides.rubyonrails.org/command_line.html#rails-server) inicia o servidor Web WEBrick que acompanha o Rails. (Para uso em produção, você provavelmente desejaria usar um servidor diferente, como Unicorn ou Passenger.)

Você deve ver saídas semelhantes às seguintes.

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000


## Adicionar um ponto de extremidade

1. Vá para o [portal clássico do Azure][management-portal] e selecione sua VM.

	![lista da máquina virtual][vmlist]

2. Selecione **PONTOS DE EXTREMIDADE** na parte superior da página e clique em **+ ADICIONAR PONTO DE EXTREMIDADE** na parte inferior da página.

	![página de pontos de extremidade][endpoints]

3. Na caixa de diálogo **ADICIONAR PONTO DE EXTREMIDADE**, selecione "Adicionar um ponto de extremidade autônomo" e clique na seta **Avançar**.

	![caixa de diálogo do novo ponde de extremidade][new-endpoint1]

3. Na próxima página de diálogo, insira as seguintes informações:

	* **NOME**: HTTP

	* **PROTOCOLO**: TCP

	* **PORTA PÚBLICA**: 80

	* **PORTA PRIVADA**: 3000

	Isso criará uma porta pública de 80 que roteará o tráfego para a porta privada de 3000, na qual o servidor Rails está escutando.

	![caixa de diálogo do novo ponde de extremidade][new-endpoint]

4. Clique na marca de seleção para salvar o ponto de extremidade.

5. Deve aparecer uma mensagem que indica **ATUALIZAÇÃO EM ANDAMENTO**. Depois que a mensagem desaparecer, o ponto de extremidade estará ativo. Você já pode testar seu aplicativo navegando até o nome DNS de sua máquina virtual. O site deve aparecer ao seguinte:

	![página padrão de rails][default-rails-cloud]


##<a id="next"></a>Próximas etapas

Neste tutorial, você realizou a maior parte das etapas manualmente. Em um ambiente de produção, você gravaria seu aplicativo em um computador de desenvolvimento e o implantaria em VM do Azure. Além disso, a maioria dos ambientes de produção hospeda o aplicativo Rails em conjunto com outro processo do servidor como Apache ou NginX, que trata o roteamento da solicitação para várias instâncias do aplicativo Rails e atende recursos estáticos. Para obter mais informações, consulte http://rubyonrails.org/deploy/.

Para saber mais sobre o Ruby on Rails, visite os [Guias do Ruby on Rails][rails-guides].

Para usar os serviços do Azure de seu aplicativo Ruby, consulte:

* [Armazenar dados desestruturados usando blobs][blobs]

* [Armazenar pares de chave/valor usando tabelas][tables]

* [Fornecer conteúdo de alta largura de banda com a Rede de Distribuição de Conteúdo][cdn-howto]



<!-- WA.com links -->
[blobs]: ../storage-ruby-how-to-use-blob-storage.md

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[vm-instructions]: virtual-machines-linux-tutorial.md


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png

[new-endpoint1]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint1.png

<!---HONumber=AcomDC_1203_2015-->