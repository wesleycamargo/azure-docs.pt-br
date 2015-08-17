<properties
	pageTitle="Tutorial sobre aplicativo Web com Django - Azure"
	description="Um tutorial que ensina como hospedar um site da web baseado em Django no Azure usando uma máquina virtual Windows Server 2012 R2 Datacenter."
	services="virtual-machines"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>


<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="python"
	ms.topic="article"
	ms.date="05/20/2015"
	ms.author="huvalo"/>




# Aplicativo Web Django Hello World (a página pode estar em inglês)

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

Este tutorial descreve como hospedar um site da web baseado no Django no Microsoft Azure usando uma máquina virtual do Windows Server. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Após concluir este tutorial, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

* Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no Windows Server, o mesmo também pode ser feito com uma MV Linux hospedada no Azure.
* Crie um novo aplicativo Django do Windows.

Ao seguir este tutorial, você criará um aplicativo Web Hello World simples. O aplicativo será hospedado em uma máquina virtual do Azure.

Uma captura de tela do aplicativo concluído é exibida em seguida.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Crie e configure uma máquina virtual do Azure para hospedar o Django

1. Siga as instruções fornecidas [aqui][portal-vm] para criar uma máquina virtual do Azure da distribuição do Windows Server 2012 R2 Datacenter.

1. Instrua o Azure para direcionar o tráfego da porta 80 da web para a porta 80 na máquina virtual:
 - Navegue até a sua máquina virtual recém-criada no Portal do Azure e clique na guia **PONTOS DE EXTREMIDADE**.
 - Clique no botão **ADICIONAR** na parte inferior da tela.![adicionar ponto de extremidade](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Abra o **TCP** do protocolo **PORTA PÚBLICA 80** como **PORTA PRIVADA 80**. ![][port80]
1. Na guia **PAINEL**, clique em **CONECTAR** para usar a **Área de Trabalho Remota** para fazer logon remotamente na máquina virtual recém-criada do Azure.  

**Observação importante:** todas as instruções abaixo pressupõem que você fez o logon corretamente na máquina virtual e está emitindo comandos lá em vez de sua máquina local.

## <a id="setup"> </a>Configurando Python e Django

**Observação:** para fazer downloads usando o Internet Explorer, talvez você precise configurar IE ESC (Iniciar/Ferramentas Administrativas/Gerenciador de Servidores/Servidor Local, em seguida, clique em **Configuração de Segurança Reforçada do IE**, defina como Desabilitado).

1. Instale o [Web Platform Installer][].
1. Instale o Python e o WFastCGI usando o Web Platform Installer. Isso instalará o wfastcgi.py na sua pasta de scripts Python.
	1. Inicie o Web Platform Installer.
	1. Digite WFastCGI na barra de pesquisa.
	1. Selecione a entrada WFactCGI correspondente à versão de Python que você deseja usar (2.7 ou 3.4). Observe que essa ação instalará o Python como uma dependência do WFastCGI.
1. Instale o Django usando o pip.

    No Python 2.7, use o comando a seguir.

        c:\python27\scripts\pip install django

    No Python 3.4, use o comando a seguir.

        c:\python34\scripts\pip install django


## Configurando o IIS com FastCGI

1. Instale o IIS com o suporte FastCGI. Isso pode levar alguns minutos.

		start /wait %windir%\System32\\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI


### Python 2,7

Execute estes comando somente se estiver usando o Python 2.7.

1. Instale o manipulador Fast CGI do Python.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"


1. Registre o manipulador para este site.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Configure o manipulador para executar seu aplicativo Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Configure o PYTHONPATH para que seu aplicativo Django possa ser encontrado pelo interpretador do Python.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Informe o FastCGI ao gateway WSGI qual manipulador WSGI usar.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost


1. Você verá a seguinte captura de tela.

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png)

### Python 3.4

Execute estes comando somente se estiver usando o Python 3.4.

1. Instale o manipulador Fast CGI do Python.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"


1. Registre o manipulador para este site.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Configure o manipulador para executar seu aplicativo Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Configure o PYTHONPATH para que seu aplicativo Django possa ser encontrado pelo interpretador do Python.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Informe o FastCGI ao gateway WSGI qual manipulador WSGI usar.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

1. Você verá a seguinte captura de tela.

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png)


## Criando um novo aplicativo Django


1.  Em *C:\\inetpub\\wwwroot*, insira o seguinte comando para criar um novo projeto Django:

    No Python 2.7, use o comando a seguir.

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    No Python 3.4, use o comando a seguir.

		C:\Python34\Scripts\django-admin.exe startproject helloworld

	![O resultado do comando New-AzureService](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.  O comando **django-admin** gera uma estrutura básica para sites da Web baseados em Django:

  -   **helloworld\\manage.py** ajuda a iniciar a hospedagem e parar a hospedagem do seu site da Web baseado em Django
  -   **helloworld\\helloworld\\settings.py** contém as configurações do Django para o seu aplicativo.
  -   **helloworld\\helloworld\\urls.py** contém o código de mapeamento entre cada url e seu modo de exibição.



1.  Crie um novo arquivo chamado **views.py** no diretório *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Isso irá conter o modo de exibição que processa a página "hello world". Inicie o editor e insira o seguinte:

		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Substitua o conteúdo do arquivo urls.py pelo seguinte.

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

1. Por fim, carregue a página da Web no seu navegador.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][1]

## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[portal-vm]: /manage/windows/tutorials/virtual-machine-from-gallery/

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx

<!---HONumber=August15_HO6-->