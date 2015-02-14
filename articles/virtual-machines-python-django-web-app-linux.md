<properties 
	pageTitle="Tutorial sobre aplicativo Web Python com Django no Mac - Azure" 
	description="Um tutorial que mostra hospedar um site da web baseado no Django no Azure usando uma máquina virtual do Linux." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="huvalo"/>





# Aplicativo da web Django Hello World (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/pt-br/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

Este tutorial descreve como hospedar um site baseado em Django no Windows 
Azure usando uma máquina virtual Linux. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Depois de concluir este guia, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

* Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no **Linux**, o mesmo também pode ser feito com uma MV do Windows Server hospedada no Azure. 
* Crie um novo aplicativo Django do Linux.

Seguindo este tutorial, você criará um aplicativo da Web simples Hello World.
. O aplicativo será hospedado em uma máquina virtual do Azure.

A seguinte é uma captura de tela do aplicativo concluído:

![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Crie e configure uma máquina virtual do Azure para hospedar o Django

1. Siga as instruções fornecidas [aqui][portal-vm] para criar uma máquina virtual do Azure da *distribuição * do Ubuntu Server 14.04 LTS*.

 **Observe:**você *somente* precisa criar a máquina virtual.  Pare na seção intitulada  *Como fazer logon na máquina virtual depois de criá-la*.

1. Instrua o Azure para direcionar o tráfego da porta **80** da web para a porta **80** na máquina virtual:
	* Navegue até a sua máquina virtual recém-criada no Portal do Azure e clique na guia *PONTOS DE EXTREMIDADE*.
	* Clique no botão *ADICIONAR* na parte inferior da tela.
	![add endpoint](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
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

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    O script **django-admin.py** gera uma estrutura básica para sites da web baseados em Django:
    -   **helloworld/manage.py** ajuda a iniciar a hospedagem e parar a hospedagem do seu site da web baseado em Django
    -   **helloworld/helloworld/settings.py** contém as configurações do Django para o seu aplicativo.
    -   **helloworld/helloworld/urls.py** contém o código de mapeamento entre cada url e seu modo de exibição.

1.  Crie um novo arquivo chamado **views.py** no diretório **/var/www/helloworld/helloworld**. Isso irá conter o modo de exibição que processa a página "hello world". Inicie o editor e digite o seguinte:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Substitua agora o conteúdo do arquivo **urls.py** pelo seguinte:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Configurando o Apache

1.  Crie um arquivo de configuração do host virtual Apache **/etc/apache2/sites-available/helloworld.conf**. Defina o conteúdo como o seguinte e lembre-se de substituir o  *yourVmUrl*pela URL atual da máquina que você está usando (por exemplo *pyubuntu.cloudapp.net*).

		<VirtualHost *:80>
		ServerName yourVmUrl
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Habilite o site com o seguinte comando:

        $ sudo a2ensite helloworld

1.  Reinicie o apache com o seguinte comando:

        $ sudo service apache2 reload

1.  Por fim, carregue a página da web no seu navegador:

	![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.


[portal-vm]: /pt-br/manage/linux/tutorials/virtual-machine-from-gallery/
<!--HONumber=42-->
