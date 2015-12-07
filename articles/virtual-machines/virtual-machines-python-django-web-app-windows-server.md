<properties
	pageTitle="Aplicativo Web Python com Django | Microsoft Azure"
	description="Este tutorial ensina como hospedar um site da web baseado em Django no Azure usando uma máquina virtual do Windows Server 2012 R2 Datacenter usando o modelo clássico de implantação."
	services="virtual-machines"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="huvalo"/>


# Aplicativo Web Django Hello World em uma VM do Windows Server

> [AZURE.SELECTOR]
- [Windows](virtual-machines-python-django-web-app-windows-server.md)
- [Mac/Linux](virtual-machines-python-django-web-app-linux.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.
 

Este tutorial descreve como hospedar um site da web baseado no Django no Microsoft Azure usando uma máquina virtual do Windows Server. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Após concluir este tutorial, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

* Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no Windows Server, o mesmo também pode ser feito com uma MV Linux hospedada no Azure.
* Crie um novo aplicativo Django do Windows.

Ao seguir este tutorial, você criará um aplicativo Web Hello World simples. O aplicativo será hospedado em uma máquina virtual do Azure.

Uma captura de tela do aplicativo concluído é exibida em seguida.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Crie e configure uma máquina virtual do Azure para hospedar o Django

1. Siga as instruções fornecidas [aqui](virtual-machines-windows-tutorial-classic-portal.md) para criar uma máquina virtual do Azure da distribuição do Windows Server 2012 R2 Datacenter.

1. Instrua o Azure para direcionar o tráfego da porta 80 da web para a porta 80 na máquina virtual:
 - Navegue até sua máquina virtual recém-criada no portal do Azure e clique na aba **PONTOS DE EXTREMIDADE**.
 - Clique no botão **ADICIONAR** na parte inferior da tela.![adicionar ponto de extremidade](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Abra o **TCP** do protocolo **PORTA PÚBLICA 80** como **PORTA PRIVADA 80**. ![][port80]
1. Na guia **PAINEL**, clique em **CONECTAR** para usar a **Área de Trabalho Remota** para fazer logon remotamente na máquina virtual recém-criada do Azure.  

**Observação importante:** todas as instruções abaixo pressupõem que você fez o logon corretamente na máquina virtual e está emitindo comandos lá em vez de sua máquina local.

## <a id="setup"> </a>Instalando Python, Django, WFastCGI

**Observação:** para fazer downloads usando o Internet Explorer, talvez você precise configurar IE ESC (Iniciar/Ferramentas Administrativas/Gerenciador de Servidores/Servidor Local, em seguida, clique em **Configuração de Segurança Reforçada do IE**, defina como Desabilitado).

1. Instalar o Python 2.7 ou 3.4 mais recente do [python.org][].
1. Instale os pacotes wfastcgi e django usando pip.

    No Python 2.7, use o comando a seguir.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    No Python 3.4, use o comando a seguir.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## Instalando o IIS com FastCGI

1. Instale o IIS com o suporte FastCGI. Isso pode levar alguns minutos.

		start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## Criando um novo aplicativo Django

1.  Em *C:\\inetpub\\wwwroot*, digite o seguinte comando para criar um novo projeto de Django:

    No Python 2.7, use o comando a seguir.

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    No Python 3.4, use o comando a seguir.

		C:\Python34\Scripts\django-admin.exe startproject helloworld

	![O resultado do comando New-AzureService](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.  O comando **django-admin** gera uma estrutura básica para sites da Web baseados em Django:

  -   **helloworld\\manage.py** ajuda a iniciar a hospedagem e parar a hospedagem do seu site da Web baseado em Django
  -   **helloworld\\helloworld\\settings.py** contém as configurações do Django para o seu aplicativo.
  -   **helloworld\\helloworld\\urls.py** contém o código de mapeamento entre cada url e seu modo de exibição.

1.  Crie um novo arquivo chamado **views.py** no diretório *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Isso irá conter o modo de exibição que processa a página "hello world". Inicie o editor e digite o seguinte:

		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Substitua o conteúdo do arquivo urls.py pelo seguinte.

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

## Configurando o IIS

1. Desbloqueie a seção de manipuladores no applicationHost global. Isso habilitará o uso do manipulador python no web.config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Habilite o WFastCGI. Isso adicionará um aplicativo ao applicationhost.config global que se refere ao seu interpretador de Python executável e ao script wfastcgi.py.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Crie um arquivo web.config em *C:\\inetpub\\wwwroot\\helloworld*. O valor do atributo `scriptProcessor` deve corresponder à saída da etapa anterior. Consulte a página de [wfastcgi][] em pypi para obter mais sobre configurações de wfastcgi.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Atualize o local do Site da Web padrão IIS para apontar para a pasta do projeto django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Por fim, carregue a página da Web no seu navegador.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][1]


## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

<!---HONumber=AcomDC_1125_2015-->