<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web com Django" pageTitle="App da web Python com Django no Mac - tutorial Azure" metaKeywords="" description="Um tutorial que mostra como hospedar um site com base em Django no Azure usando uma máquina virtual Linux." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Aplicativo da web Django Hello World (mac-linux)" authors="" solutions="" manager="" editor="" />





# Aplicativo da web Django Hello World (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/pt-br/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

Este tutorial descreve como hospedar um site da web baseado no Django no Azure usando uma máquina virtual do Linux. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Depois de concluir este guia, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

* Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no **Linux**, o mesmo também pode ser feito com uma MV do Windows Server hospedada no Azure. 
* Crie um novo aplicativo Django do Linux.

Seguindo este tutorial, você criará um simples aplicativo da web Hello World. O aplicativo será hospedado em uma máquina virtual do Azure.

A seguinte é uma captura de tela do aplicativo concluído:

![Uma janela do navegador exibindo a página de boas-vindas no Azure.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Crie e configure uma máquina virtual do Azure para hospedar o Django

1. Siga as instruções fornecidas [aqui][portal-vm] para criar uma máquina virtual do Azure da distribuição do *Server Ubuntu 12.04*.

  **Observe:** você precisa *somente* criar a máquina virtual. Pare na seção intitulada *Como fazer logon na máquina virtual depois de criá-la*.

1. Instrua o Azure para direcionar o tráfego da porta **80** da web para a porta **80** na máquina virtual:
	* Navegue até a sua máquina virtual recém-criada no Portal do Azure e clique na guia *PONTOS DE EXTREMIDADE*.
	* Clique no botão *ADICIONAR PONTO DE EXTREMIDADE* na parte inferior da tela.
	![adicionar ponto de extremidade](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* Abra a *PORTA PÚBLICA 80* do protocolo *TCP* como *PORTA PRIVADA 80*.
	![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"> </a>Configurar o ambiente de desenvolvimento

**Observe:** se você precisar instalar o Python ou quiser usar as bibliotecas de cliente, consulte o [Guia de instalação do Python](../python-how-to-install/).

A MV do Linux Ubuntu já vem com o Python 2.7 pré-instalado, mas ela não tem o Apache ou o django instalados.  Siga estas etapas para conectar a sua MV e instalar o Apache e o django.

1.  Inicie uma nova janela **Terminal**.
    
1.  Digite o seguinte comando para conectar-se à sua MV do Azure.

		$ ssh yourusername@yourVmUrl

1.  Digite os seguintes comandos para instalar o django:

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  Digite o seguinte comando para instalar o Apache com o mod-wsgi:

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Criando um novo aplicativo Django

1.  Abra a janela **Terminal** que você usou na seção anterior para ssh na sua MV.
    
1.  Digite os seguintes comandos para criar um novo projeto de Django:

    ![O resultado do comando admin do django](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-dir.png)

    O script **django-admin.py** gera uma estrutura básica para sites baseados em Django:
    -   **manage.py** ajuda você a iniciar a hospedagem e parar a hospedagem do seu site baseado em Django
    -   **helloworld\settings.py** contém as configurações do Django para o seu aplicativo.
    -   **helloworld\urls.py** contém o código de mapeamento entre cada url e seu modo de exibição.

1.  Crie um novo arquivo chamado **views.py** no subdiretório *helloworld* do *django\helloworld*, como um irmão do **urls.py**. Isso irá conter o modo de exibição que processa a página "hello world". Inicie o editor e digite o seguinte:
		
		from django.http import HttpResponse
		def hello(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Substitua agora o conteúdo do arquivo **urls.py** pelo seguinte:

		from django.conf.urls.defaults import patterns, include, url
		from helloworld.views import hello
		urlpatterns = patterns('',
			(r'^$',hello),
		)


## Implantando e executando seu site do Django

1.  Edite o arquivo de configuração do apache **/etc/apache2/httpd.conf** e adicione o seguinte, substituindo o *nome de usuário* pelo nome de usuário que você especificou durante a criação da MV:

		WSGIScriptAlias / /home/*username*/django/helloworld/helloworld/wsgi.py
		WSGIPythonPath /home/*username*/django/helloworld

		<Directory /home/*username*/django/helloworld/helloworld>
		<Files wsgi.py>
		Order deny,allow
		Allow from all
		</Files>
		</Directory>

1.  Reinicie o apache com o seguinte comando:

        $ sudo apachectl restart

1.  Por fim, carregue a página da web no seu navegador:

	![Uma janela do navegador exibindo a página de boas-vindas no Azure.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.


[portal da mv]: /pt-br/manage/linux/tutorials/virtual-machine-from-gallery/

