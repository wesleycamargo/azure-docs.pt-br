<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Aplicativo Web Django Hello World (a página pode estar em inglês)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/pt-br/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

Este tutorial descreve como hospedar um site da web baseado no Django no Windows Azure usando uma máquina virtual do Windows Server. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Depois de concluir este guia, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

-   Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no **Windows Server**, o mesmo também pode ser feito com uma MV Linux hospedada no Azure.
-   Crie um novo aplicativo Django do Windows.

Seguindo este tutorial, você criará um simples aplicativo da web Hello World. O aplicativo será hospedado em uma máquina virtual do Azure.

A seguinte é uma captura de tela do aplicativo concluído:

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][Uma janela do navegador exibindo a página de boas-vindas no Azure.]

[WACOM.INCLUDE [create-account-and-vms-note][create-account-and-vms-note]]

## Crie e configure uma máquina virtual do Azure para hospedar o Django

1.  Siga as instruções fornecidas [aqui][aqui] para criar uma máquina virtual do Azure da distribuição do *Windows Server 2012 Datacenter*.

2.  Instrua o Azure para direcionar o tráfego da porta **80** da web para a porta **80** na máquina virtual:

 -   Navegue até a sua máquina virtual recém-criada no Portal do Azure e clique na guia *PONTOS DE EXTREMIDADE*.
 -   Clique em *ADICIONAR PONTO DE EXTREMIDADE* na parte inferior da tela.
    ![adicionar ponto de extremidade][adicionar ponto de extremidade]

 -   Abra a *PORTA PÚBLICA 80* do protocolo *TCP* como *PORTA PRIVADA 80*
    ![][].

1.  Use a *Área de Trabalho Remota* do Windows para fazer logon remotamente na máquina virtual recém-criada do Azure.

**Observação importante:** Todas as instruções abaixo pressupõem que você se conectou corretamente à máquina virtual e está emitindo comandos lá, em vez de sua máquina local!

## <span id="setup"></span> </a>Configurar o ambiente de desenvolvimento

Para configurar os ambientes de Python e Django, consulte o [Guia de instalação][Guia de instalação] para obter mais informações.

**Observação 1:** Você *somente* precisa instalar o produto **Django** do instalador WebPI do Windows na máquina virtual Azure para obter *este* exclusivo tutorial operacional.

**Observação 2:** Para baixar o instalador do WebPI, talvez você precise definir as configurações do IE ESC (Iniciar/Ferramentas administrativas/Gerenciador de servidores e clicar em **Configurar IE ESC**, definindo-o para Desativado).

## Configurando o IIS com FastCGI

1.  Instale o IIS com o suporte FastCGI

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  Instale o manipulador Fast CGI Python

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  Registre o manipulador para este site

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  Configure o manipulador para executar seu aplicativo Django

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  Configure o PYTHONPATH para que seu aplicativo Django possa ser encontrado pelo interpretador do Python

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    Você deve ver o seguinte:

    ![IIS config1][IIS config1]

6.  Informe o FastCGI ao gateway WSGI qual manipulador WSGI usar:

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  Baixe o wfastcgi.py do [codeplex][codeplex] e salve-o em C:\\Python27\\Scripts. Esse é o local dos comandos anteriores usados para registrar o manipulador FastCGI. Como alternativa, você pode instalá-lo usando o Web Platform Installer. Procure por 'WFastCGI'.

## Criando um novo aplicativo Django

1.  Iniciar cmd.exe

2.  cd para C:\\inetpub\\wwwroot

3.  Digite o seguinte comando para criar um novo projeto de Django:

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![O resultado do comando New-AzureService][O resultado do comando New-AzureService]

	O script **django-admin.py** gera uma estrutura básica para sites da web baseados em Django:

 -   **manage.py** ajuda a iniciar a hospedagem e parar a hospedagem do seu site da web baseado em Django
 -   **DjangoApplication\\settings.py** contém as configurações do Django para o seu aplicativo.
 -   **DjangoApplication\\urls.py** contém o código de mapeamento entre cada url e seu modo de exibição.

1.  Crie um novo arquivo chamado **views.py** no subdiretório *DjangoApplication* do *C:\\inetpub\\wwwroot\\DjangoApplication*, como um irmão do **urls.py**. Isso irá conter o modo de exibição que processa a página "hello world". Inicie o editor e digite o seguinte:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Substitua agora o conteúdo do arquivo **urls.py** pelo seguinte:

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

3.  Por fim, carregue a página da web no seu navegador.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][Uma janela do navegador exibindo a página de boas-vindas no Azure.]

## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.

  [Windows]: /pt-br/develop/python/tutorials/web-app-with-django/ "Windows"
  [Mac/Linux]: /pt-br/develop/python/tutorials/django-hello-world-(maclinux)/ "MacLinux"
  [Uma janela do navegador exibindo a página de boas-vindas no Azure.]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [aqui]: /pt-br/manage/windows/tutorials/virtual-machine-from-gallery/
  [adicionar ponto de extremidade]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  []: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [Guia de instalação]: ../python-how-to-install/
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png
  [codeplex]: http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409
  [O resultado do comando New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
