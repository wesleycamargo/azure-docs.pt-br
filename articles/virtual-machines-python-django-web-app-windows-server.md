<properties urlDisplayName="Web with Django (Windows)" pageTitle="Tutorial sobre aplicativo Web com Django - Azure" metaKeywords="Azure Django web app, Azure Django virtual machine" description="Um tutorial que ensina como hospedar um site da web baseado em Django no Azure usando uma m&aacute;quina virtual Windows Server 2012 R2 Datacenter." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Aplicativo Web Django Hello World (a p&aacute;gina pode estar em ingl&ecirc;s)" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Aplicativo Web Django Hello World (a página pode estar em inglês)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/pt-br/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

Este tutorial descreve como hospedar um site da web baseado no Django no Microsoft
Azure usando uma máquina virtual do Windows Server. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Depois de concluir este guia, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

-   Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no **Windows Server**, o mesmo também pode ser feito com uma MV Linux hospedada no Azure.
-   Crie um novo aplicativo Django do Windows.

Seguindo este tutorial, você criará um simples aplicativo da
web Hello World. O aplicativo será hospedado em uma máquina virtual do Azure.

A seguinte é uma captura de tela do aplicativo concluído:

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][Uma janela do navegador exibindo a página de boas-vindas no Azure.]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Crie e configure uma máquina virtual do Azure para hospedar o Django

1.  Siga as instruções fornecidas [aqui][aqui] para criar uma máquina virtual do Azure da distribuição do *Windows Server 2012 R2 Datacenter*.

2.  Instrua o Azure para direcionar o tráfego da porta **80** da web para a porta **80** na máquina virtual:

-   Navegue até a sua máquina virtual recém-criada no Portal do Azure e clique na guia *PONTOS DE EXTREMIDADE*.
-   Clique no botão *ADICIONAR* na parte inferior da tela.
    ![adicionar ponto de extremidade][adicionar ponto de extremidade]

-   Abra a *PORTA PÚBLICA 80* do protocolo *TCP* como *PORTA PRIVADA 80*
    ![][0].

1.  Na guia *PAINEL*, clique em *CONECTAR* para usar a *Área de Trabalho Remota* para fazer logon remotamente na máquina virtual recém-criada do Azure.

**Observação importante:** Todas as instruções abaixo pressupõem que você se conectou corretamente à máquina virtual e está emitindo comandos lá, em vez de sua máquina local!

## <span id="setup"></span> </a>Configurando Python e Django

**Observação:** Para fazer o download usando o Internet Explorer, é necessário definir as configurações do IE ESC (Iniciar/Ferramentas Administrativas/Gerenciador de Servidores/Servidor Local e, em seguida, clique em **Configuração de Segurança Aprimorada do IE**, definindo-a para Desativado).

1.  Instale o [Web Platform Installer][Web Platform Installer].
2.  Instale o Python e o WFastCGI usando o Web Platform Installer. Isso instalará o wfastcgi.py na sua pasta de scripts Python.

    1.  Inicie o Web Platform Installer.
    2.  Digite WFastCGI na barra de pesquisa.
    3.  Selecione a entrada WFactCGI correspondente à versão de Python que você deseja usar (2.7 ou 3.4). Observe que essa ação instalará o Python como uma dependência do WFastCGI.

3.  Se você tiver instalado o Python 2.7, [siga estas instruções para instalar manualmente o pip][siga estas instruções para instalar manualmente o pip] (o Python 3.4 vem com o pip já instalado).
4.  Instale o Django usando o pip.

    Python 2,7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django

## Configurando o IIS com FastCGI

1.  Instale o IIS com o suporte FastCGI. Isso pode levar alguns minutos.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

### Python 2,7

Execute estes comando somente se estiver usando o Python 2,7.

1.  Instale o manipulador Fast CGI Python.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

2.  Registre o manipulador para este site.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Configure o manipulador para executar seu aplicativo Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Configure o PYTHONPATH para que seu aplicativo Django possa ser encontrado pelo interpretador do Python.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Informe o FastCGI ao gateway WSGI qual manipulador WSGI usar.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Você deve ver o seguinte:

    ![IIS config1][IIS config1]

### Python 3.4

Execute estes comando somente se estiver usando o Python 3.4.

1.  Instale o manipulador Fast CGI Python.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"

2.  Registre o manipulador para este site.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Configure o manipulador para executar seu aplicativo Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Configure o PYTHONPATH para que seu aplicativo Django possa ser encontrado pelo interpretador do Python.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Informe o FastCGI ao gateway WSGI qual manipulador WSGI usar.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Você deve ver o seguinte:

    ![IIS config1][1]

## Criando um novo aplicativo Django

1.  Em *C:\\inetpub\\wwwroot*, digite o seguinte comando para criar um novo projeto de Django:

    Python 2,7:

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![O resultado do comando New-AzureService][O resultado do comando New-AzureService]

2.  O comando **django-admin** gera uma estrutura básica para sites da Web baseados em Django:

-   **helloworld\\manage.py** ajuda a iniciar a hospedagem e parar a hospedagem do seu site da Web baseado em Django
-   **helloworld\\helloworld\\settings.py** contém as configurações do Django para o seu aplicativo.
-   **helloworld\\helloworld\\urls.py** contém o código de mapeamento entre cada url e seu modo de exibição.

1.  Crie um novo arquivo chamado **views.py** no diretório *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Isso irá conter o modo de exibição que processa a página "hello world". Inicie o editor e digite o seguinte:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Substitua o conteúdo do arquivo **urls.py** pelo seguinte:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

3.  Por fim, carregue a página da web no seu navegador.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][Uma janela do navegador exibindo a página de boas-vindas no Azure.]

## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.

  [Uma janela do navegador exibindo a página de boas-vindas no Azure.]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [aqui]: /pt-br/manage/windows/tutorials/virtual-machine-from-gallery/
  [adicionar ponto de extremidade]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [siga estas instruções para instalar manualmente o pip]: https://pip.pypa.io/en/latest/installing.html
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png
  [1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png
  [O resultado do comando New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
