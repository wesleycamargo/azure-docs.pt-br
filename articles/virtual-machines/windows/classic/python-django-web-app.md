---
title: Criar um aplicativo Django em uma VM do Azure | Microsoft Docs
description: "Este tutorial ensina como hospedar um site da web baseado em Django no Azure usando uma máquina virtual do Windows Server 2012 R2 Datacenter usando o modelo clássico de implantação."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a55a2c069df1717bc49199589abf280cced307af
ms.lasthandoff: 03/27/2017


---
# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Aplicativo Web Django Hello World em uma VM do Windows Server
> [!div class="op_single_selector"]
> * [Windows](python-django-web-app.md)
> * [Mac/Linux](../../virtual-machines-linux-python-django-web-app.md)
> 
> 

<br>

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter um modelo do Resource Manager para implantar o Django, clique [aqui](https://azure.microsoft.com/documentation/templates/django-app/).

Este tutorial descreve como hospedar um site da web baseado no Django no Microsoft Azure usando uma máquina virtual do Windows Server. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Após concluir este tutorial, você terá um aplicativo baseado no Django em funcionamento na nuvem.

Você saberá como:

* Configure uma máquina virtual Azure para hospedar o Django. Enquanto este tutorial explica como fazer isso no Windows Server, o mesmo também pode ser feito com uma MV Linux hospedada no Azure.
* Crie um novo aplicativo Django do Windows.

Ao seguir este tutorial, você criará um aplicativo Web Hello World simples. O aplicativo será hospedado em uma máquina virtual do Azure.

Uma captura de tela do aplicativo concluído é exibida em seguida.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Crie e configure uma máquina virtual do Azure para hospedar o Django
1. Siga as instruções fornecidas [aqui](tutorial.md) para criar uma máquina virtual do Azure da distribuição do Windows Server 2012 R2 Datacenter.
2. Instrua o Azure para direcionar o tráfego da porta 80 da web para a porta 80 na máquina virtual:
   
   * Navegue até sua máquina virtual recém-criada no portal clássico do Azure e clique na aba **PONTOS DE EXTREMIDADE** .
   * Clique no botão **ADICIONAR** na parte inferior da tela.
     ![adicionar ponto de extremidade](./media/python-django-web-app/django-helloworld-addendpoint.png)
   * Abra o **TCP** do protocolo **PORTA PÚBLICA 80** como **PORTA PRIVADA 80**.
     ![][port80]
3. Na guia **PAINEL**, clique em **CONECTAR** para usar a **Área de Trabalho Remota** para fazer logon remotamente na máquina virtual recém-criada do Azure.  

**Observação importante:** todas as instruções abaixo pressupõem que você fez o logon corretamente na máquina virtual e está emitindo comandos lá em vez de sua máquina local.

## <a id="setup"> </a>Instalando Python, Django, WFastCGI
**Observação:** para fazer downloads usando o Internet Explorer, talvez você precise configurar IE ESC (Iniciar/Ferramentas Administrativas/Gerenciador de Servidores/Servidor Local, em seguida, clique em **Configuração de Segurança Reforçada do IE**, defina como Desabilitado).

1. Instalar o Python 2.7 ou 3.4 mais recente de [python.org][python.org].
2. Instale os pacotes wfastcgi e django usando pip.
   
    No Python 2.7, use o comando a seguir.
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    No Python 3.4, use o comando a seguir.
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Instalando o IIS com FastCGI
1. Instale o IIS com o suporte FastCGI.  Isso pode levar alguns minutos.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Criando um novo aplicativo Django
1. Em *C:\inetpub\wwwroot*, digite o seguinte comando para criar um novo projeto de Django:
   
   No Python 2.7, use o comando a seguir.
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   No Python 3.4, use o comando a seguir.
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![O resultado do comando New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. O comando **django-admin** gera uma estrutura básica para sites da Web baseados em Django:
   
   * **helloworld\manage.py** ajuda a iniciar a hospedagem e parar a hospedagem do seu site da web baseado em Django
   * **helloworld\helloworld\settings.py** contém as configurações do Django para o seu aplicativo.
   * **helloworld/helloworld/urls.py** contém o código de mapeamento entre cada url e sua visualização.
3. Crie um novo arquivo chamado **views.py** no diretório *C:\inetpub\wwwroot\helloworld\helloworld*. Isso irá conter o modo de exibição que processa a página "hello world". Inicie o editor e digite o seguinte:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Substitua o conteúdo do arquivo urls.py pelo seguinte.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="configuring-iis"></a>Configurando o IIS
1. Desbloqueie a seção de manipuladores no applicationHost global.  Isso habilitará o uso do manipulador python no web.config.
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Habilite o WFastCGI.  Isso adicionará um aplicativo ao applicationhost.config global que se refere ao seu interpretador de Python executável e ao script wfastcgi.py.
   
    Python 2.7:
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3.4:
   
        c:\python34\scripts\wfastcgi-enable
3. Crie um arquivo web.config em *C:\inetpub\wwwroot\helloworld*.  O valor do atributo `scriptProcessor` deve corresponder à saída da etapa anterior.  Consulte a página de [wfastcgi][wfastcgi] em pypi para obter mais sobre configurações de wfastcgi.
   
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
4. Atualize o local do Site da Web padrão IIS para apontar para a pasta do projeto django.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Por fim, carregue a página da Web no seu navegador.

![Uma janela do navegador exibindo a página de boas-vindas no Azure.][1]

## <a name="shutting-down-your-azure-virtual-machine"></a>Desligando a máquina virtual do Azure
Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

