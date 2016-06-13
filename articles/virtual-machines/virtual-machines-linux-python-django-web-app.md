<properties 
	pageTitle="Aplicativo Web Python com Django no Linux | Microsoft Azure" 
	description="Saiba como hospedar um aplicativo Web baseado em Django no Azure usando uma máquina virtual do Linux." 
	services="virtual-machines-linux" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="huvalo"/>
	
# Aplicativo Web Django Hello World em uma VM do Linux

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

Este tutorial descreve como hospedar um site da web baseado no Django no Microsoft Azure usando uma máquina virtual do Linux. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Depois de concluir este guia, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

* Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no **Linux**, o mesmo também pode ser feito com uma MV do Windows Server hospedada no Azure. 
* Crie um novo aplicativo Django do Linux.

Ao seguir este tutorial, você criará um aplicativo Web Hello World simples. O aplicativo será hospedado em uma máquina virtual do Azure.

Abaixo, uma captura de tela do aplicativo concluído:

![Uma janela do navegador exibindo a página de boas-vindas no Azure.](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Crie e configure uma máquina virtual do Azure para hospedar o Django

1. Siga as instruções fornecidas [aqui](virtual-machines-linux-portal-create.md) para criar uma máquina virtual do Azure da distribuição do *Server Ubuntu 14.04 LTS*. Se preferir, pode escolher a autenticação de senha em vez de chave pública SSH.

1. Editar o grupo de segurança de rede para permitir o tráfego http de entrada para a porta 80 usando as instruções [aqui](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Por padrão, a nova máquina virtual não tem um nome de domínio totalmente qualificado (FQDN). Você pode criar um seguindo as instruções [aqui](virtual-machines-linux-portal-create-fqdn.md). Essa etapa é opcional para concluir este tutorial.

## <a id="setup"> </a>Configurando o ambiente de desenvolvimento

**Observação:** se precisar instalar o Python ou se quiser usar as Bibliotecas do Cliente, confira o [Guia de instalação do Python](../python-how-to-install.md).

A MV do Linux Ubuntu já vem com o Python 2.7 pré-instalado, mas ela não tem o Apache ou o django instalados. Siga estas etapas para conectar a sua MV e instalar o Apache e o django.

1.  Inicie uma nova janela **Terminal**.
    
1.  Digite o seguinte comando para conectar-se à sua MV do Azure. Se não tiver criado um FQDN, você pode se conectar usando o endereço IP público exibido no resumo da máquina virtual no portal clássico do Azure.

		$ ssh yourusername@yourVmUrl

1.  Digite os seguintes comandos para instalar o django:

		$ sudo apt-get install python-setuptools python-pip
		$ sudo pip install django

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

1.  Crie um arquivo de configuração do host virtual Apache **/etc/apache2/sites-available/helloworld.conf**. Defina o conteúdo como o seguinte e substitua *yourVmName* pelo nome real do computador que você está usando (por exemplo *pyubuntu*).

		<VirtualHost *:80>
		ServerName yourVmName
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Habilite o site com o seguinte comando:

        $ sudo a2ensite helloworld

1.  Reinicie o apache com o seguinte comando:

        $ sudo service apache2 reload

1.  Por fim, carregue a página da Web no seu navegador:

	![Uma janela do navegador exibindo a página de boas-vindas no Azure.](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.

<!---HONumber=AcomDC_0601_2016-->