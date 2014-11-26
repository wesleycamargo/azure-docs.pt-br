<properties urlDisplayName="Create Web and Worker Roles" pageTitle="Criar fun&ccedil;&otilde;es Web e de Trabalho" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="PHP" title="Como criar fun&ccedil;&otilde;es Web e de trabalho do PHP" authors="bswan" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="bswan" />

# Como criar funções Web e de trabalho do PHP

Este guia mostrará como criar funções Web ou de trabalho do PHP em um ambiente de desenvolvimento Windows, como escolher uma versão específica do PHP nas versões "internas" disponíveis, como alterar a configuração do PHP, habilitar extensões e, finalmente, como implantar no Azure. Também descreve como configurar uma função Web ou de trabalho para usar um tempo de execução do PHP (com configuração e extensões personalizadas) que você fornece.

## <a name="TableOfContents"></a> Sumário

-   [O que são funções Web e de Trabalho do PHP?][O que são funções Web e de Trabalho do PHP?]
-   [Baixar o SDK do Azure para PHP][Baixar o SDK do Azure para PHP]
-   [Como: Criar um projeto de Serviço de Nuvem][Como: Criar um projeto de Serviço de Nuvem]
-   [Como: Adicionar funções de trabalho e Web PHP][Como: Adicionar funções de trabalho e Web PHP]
-   [Como: Especificar a versão do PHP interno][Como: Especificar a versão do PHP interno]
-   [Como: Personalizar o tempo de execução do PHP interno][Como: Personalizar o tempo de execução do PHP interno]
-   [Como: Usar seu próprio tempo de execução PHP][Como: Usar seu próprio tempo de execução PHP]
-   [Como: Executar seu aplicativo nos Emuladores de Computação e de Armazenamento][Como: Executar seu aplicativo nos Emuladores de Computação e de Armazenamento]
-   [Como: Publicar seu aplicativo][Como: Publicar seu aplicativo]

## <a name="WhatIs"></a>O que são funções Web e de trabalho do PHP?

O Azure fornece três modelos de computação para aplicações em execução: [Sites do Azure][Sites do Azure], [Máquinas Virtuais do Azure][Máquinas Virtuais do Azure], e [Serviços de Nuvem do Azure][Serviços de Nuvem do Azure] Todos os três modelos oferecem suporte ao PHP. Os Serviços de Nuvem, que incluem as funções Web e de trabalho, fornecem a *PaaS (plataforma como serviço)*. Dentro de um serviço de nuvem, uma função Web fornece um servidor web dos Serviços de Informações da Internet (IIS) dedicado para hospedar aplicativos web de front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, de longa execução ou perpétuas independentes de interação com o usuário ou de entrada.

Para obter mais informações, consulte [O que é um Serviço de Nuvem?][O que é um Serviço de Nuvem].

## <a name="DownloadSdk"></a>Baixar o SDK do Azure para PHP

O [SDK do Azure para PHP][SDK do Azure para PHP] consiste em vários componentes. Este artigo usará dois deles: O PowerShell do Azure e o Emuladores do Azure. Esses dois componentes podem ser instalados pelo Microsoft Web Platform Installer aqui: [Instalar o PowerShell do Azure e o Emuladores do Azure][Instalar o PowerShell do Azure e o Emuladores do Azure].

## <a name="CreateProject"></a>Como: Criar um projeto de Serviço de Nuvem

A primeira etapa na criação de uma função Web ou de trabalho do PHP é criar um projeto de Serviço do Azure. Um projeto de serviço do Azure atua como um contêiner lógico para funções Web e de trabalho e contém os arquivos de [(definição do serviço (.csdef)][definição do serviço (.csdef)] e de [configuração do serviço (.cscfg)][configuração do serviço (.cscfg)] do projeto.

Para criar um novo projeto de Serviço do Azure, execute o seguinte comando:

    PS C:PS C:\>New-AzureServiceProject myProjectgt;New-AzureServiceProject myProject

Esse comando irá criar um novo diretório (`myProject`) ao qual você pode adicionar funções Web e de trabalho.

## <a name="AddRole"></a>Como: Adicionar funções de trabalho ou Web PHP

Para adicionar uma função Web do PHP a um projeto, execute o seguinte comando no diretório raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para uma função de trabalho, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>O par&acirc;metro <code data-inline="1">roleName</code> &eacute; opcional. Se for omitido, o nome da fun&ccedil;&atilde;o ser&aacute; gerado automaticamente. A primeira fun&ccedil;&atilde;o Web criada ser&aacute; <code data-inline="1">WebRole1</code>, a segunda ser&aacute; <code data-inline="1">WebRole2</code> e assim por diante. A primeira fun&ccedil;&atilde;o de trabalho criada ser&aacute; <code data-inline="1">WorkerRole1</code>, a segunda ser&aacute; <code data-inline="1">WorkerRole2</code> e assim por diante.</p> 
</div>

## <a name="SpecifyPHPVersion"></a>Como: Especificar a versão do PHP interno

Quando você adiciona uma função Web ou de trabalho do PHP a um projeto, os arquivos de configuração do projeto são modificados para que o PHP seja instalado em cada instância da web ou de trabalho do seu aplicativo quando ele for implantado. Para ver a versão do PHP que será instalada por padrão, execute o seguinte comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

A saída do comando acima será semelhante à mostrada a seguir. Neste exemplo, o sinalizador `IsDefault` está definido como `true` para PHP 5.3.17 indicando que ele será a versão do PHP padrão instalada.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Você pode definir a versão de tempo de execução do PHP para qualquer uma das versões do PHP listadas. Por exemplo, para definir a versão do PHP (para uma função com o nome `roleName`) como 5.4.0, use o comando a seguir:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Mais vers&otilde;es do PHP podem estar dispon&iacute;veis no futuro, e as vers&otilde;es dispon&iacute;veis podem ser alteradas.</p> 
</div>

## <a name="CustomizePHP"></a>Como: Personalizar o tempo de execução do PHP interno

Você tem controle total sobre a configuração do tempo de execução do PHP que é instalado quando você segue as etapas acima, incluindo a modificação das configurações de `php.ini` e a habilitação de extensões.

Para personalizar o tempo de execução do PHP interno, siga estas etapas:

1.  Adicione uma nova pasta, chamada `php`, ao diretório `bin` de sua função Web. Para uma função de trabalho, adicione-o ao diretório raiz da função.
2.  Na pasta `php`, crie outra pasta chamada `ext`. Coloque todos os arquivos de extensão `.dll` (por exemplo, `php_mongo.dll`) que você deseja habilitar nessa pasta.
3.  Adicionar um arquivo `php.ini` à pasta `php`. Habilite todas as extensões personalizadas e defina todas as diretivas do PHP nesse arquivo. Por exemplo, se você quiser ativar o `display_errors` e habilitar a extensão `php_mongo.dll`, os conteúdos do seu arquivo `php.ini` serão da seguinte maneira:

        display_errors=On
        extension=php_mongo.dll

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Todas as configura&ccedil;&otilde;es que voc&ecirc; n&atilde;o definir explicitamente no arquivo <code data-inline="1">php.ini</code> que voc&ecirc; fornecer ser&atilde;o definidas automaticamente para seus valores padr&atilde;o. No entanto, lembre-se de que voc&ecirc; pode adicionar um <code data-inline="1">php.ini</code> completo. </p> 
</div>

## <a name="OwnPHP"></a>Como: Usar seu próprio tempo de execução PHP

Em alguns casos, em vez de selecionar um tempo de execução do PHP interno e configurá-lo conforme descrito acima, você pode desejar fornecer seu próprio tempo de execução do PHP. Por exemplo, você pode usar o mesmo tempo de execução do PHP em uma função Web ou de trabalho que você usa em seu ambiente de desenvolvimento, tornando mais fácil garantir que esse aplicativo não mudará o comportamento no ambiente de produção.

### <a name="OwnPHPWebRole"></a>Configurando uma função Web para usar o seu próprio tempo de execução do PHP

Para configurar uma função Web para usar um tempo de execução do PHP que você fornecer, siga as etapas a seguir.

1.  Criar um projeto de Serviço do Azure e adicionar uma função Web do PHP conforme descrito nas seções [Como: Criar um projeto de serviços de nuvem][Como: Criar um projeto de Serviço de Nuvem] e [Como: Adicionar funções Web ou de Trabalho do PHP][Como: Adicionar funções de trabalho e Web PHP] acima.
2.  Crie uma pasta `php` na pasta `bin` que está no diretório raiz de sua função Web e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas etc.) para a pasta `php`.
3.  (OPCIONAL) Se o tempo de execução do PHP usar [Drivers da Microsoft para PHP para SQL Server][Drivers da Microsoft para PHP para SQL Server], você precisará configurar a função web para instalar o [SQL Server Native Client 2012][SQL Server Native Client 2012] quando ela for provisionada. Para fazer isso, adicione o instalador `sqlncli.msi` à pasta `bin` no diretório raiz da função Web. Você pode baixar o instalador aqui: [instalador sqlncli.msi x64][instalador sqlncli.msi x64]. O script de inicialização descrito na próxima etapa executará o instalador silenciosamente quando a função for provisionada. Se o tempo de execução do PHP não usar os Drivers Microsoft para PHP para SQL Server, você poderá remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4.  A próxima etapa é definir uma tarefa de inicialização que configura os [Serviços de Informações da Internet (IIS)][Serviços de Informações da Internet (IIS)] para usar o tempo de execução do PHP para manipular solicitações de páginas `.php`. Para fazer isso, abra o arquivo `setup_web.cmd` (no arquivo `bin` do diretório raiz da função Web) em um editor de texto e substitua seu conteúdo pelo script a seguir:

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5.  Adicione os arquivos do aplicativo ao diretório raiz da função Web. Esse será o diretório raiz do servidor web.

6.  Publique o aplicativo conforme descrito na seção [Como: Publicar seu aplicativo][Como: Publicar seu aplicativo] a seguir.

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>O script <code data-inline="1">download.ps1</code> (na pasta <code data-inline="1">bin</code> do diret&oacute;rio raiz da fun&ccedil;&atilde;o Web) pode ser exclu&iacute;do ap&oacute;s a realiza&ccedil;&atilde;o das etapas descritas acima para usar seu pr&oacute;prio tempo de execu&ccedil;&atilde;o do PHP.</p> 
</div>

### <a name="OwnPHPWorkerRole"></a>Configurando uma função de trabalho para usar o seu próprio tempo de execução do PHP

Para configurar uma função de trabalho para usar um tempo de execução do PHP que você fornecer, siga as etapas a seguir.

1.  Criar um projeto de Serviço do Azure e adicionar uma função de trabalho do PHP conforme descrito nas seções [Como: Criar um projeto de serviços de nuvem][Como: Criar um projeto de Serviço de Nuvem] e [Como: Adicionar funções Web ou de Trabalho do PHP][Como: Adicionar funções de trabalho e Web PHP] acima.
2.  Crie uma pasta `php` no diretório raiz de sua função de trabalho e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas etc.) para a pasta `php`.
3.  (OPCIONAL) Se o tempo de execução do PHP usar [Drivers da Microsoft para PHP para SQL Server][Drivers da Microsoft para PHP para SQL Server], você precisará configurar a função de trabalho para instalar o [SQL Server Native Client 2012][SQL Server Native Client 2012] quando ela for provisionada. Para fazer isso, adicione o instalador `sqlncli.msi` ao diretório raiz da função de trabalho. Você pode baixar o instalador aqui: [instalador sqlncli.msi x64][instalador sqlncli.msi x64]. O script de inicialização descrito na próxima etapa executará o instalador silenciosamente quando a função for provisionada. Se o tempo de execução do PHP não usar os Drivers Microsoft para PHP para SQL Server, você poderá remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4.  A próxima etapa é definir uma tarefa de inicialização que adiciona o `php.exe` executável à variável de ambiente PATH da função de trabalho quando a função for configurada. Para fazer isso, abra o arquivo `setup_worker.cmd` (no diretório raiz da função de trabalho) em um editor de texto e substitua seu conteúdo pelo script a seguir:

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1  

5.  Adicione os arquivos do aplicativo ao diretório raiz da função de trabalho.

6.  Publique o aplicativo conforme descrito na seção [Como: Publicar seu aplicativo][Como: Publicar seu aplicativo] a seguir.

## <a name="Emulators"></a>Como: Executar seu aplicativo nos Emuladores de Computação e de Armazenamento

Os Emuladores de Computação e de Armazenamento do Azure fornecem um ambiente local no qual você pode testar seu aplicativo do Azure antes de implantá-lo na nuvem. Existem algumas diferenças entre os emuladores e o ambiente do Azure. Para compreender melhor isso, consulte [Diferenças entre o Emulador de Computação e o Azure (a página pode estar em inglês)][Diferenças entre o Emulador de Computação e o Azure (a página pode estar em inglês)] e [Diferenças entre o Emulador de Armazenamento e os Serviços de Armazenamento do Azure (a página pode estar em inglês)][Diferenças entre o Emulador de Armazenamento e os Serviços de Armazenamento do Azure (a página pode estar em inglês)].

Observe que você deve ter instalado o PHP localmente para usar o Emulador de Computação. O Emulador de Computação usará sua instalação local do PHP para executar o aplicativo.

Para executar seu projeto nos emuladores, execute o seguinte comando no diretório raiz do projeto:

    PS C:\MyProject> Start-AzureEmulator

Você verá uma saída semelhante à seguinte:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Você pode ver o aplicativo em execução no emulador abrindo um navegador da web e navegando até o endereço local mostrado na saída (`http://127.0.0.1:81` na saída do exemplo acima).

Para parar os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="Publish"></a>Como: Publicar seu aplicativo

Para publicar seu aplicativo, você precisa primeiro importar suas configurações de publicação com o cmdlet **PublishSettingsFile** e, em seguida, publicar seu aplicativo com o cmdlet **Publish-AzureServiceProject**. Detalhes sobre como usar cada um desses cmdlets podem ser encontrados em [Como: Importar configurações de publicação][Como: Importar configurações de publicação] e [Como: Implantar um serviço de nuvem no Azure][Como: Implantar um serviço de nuvem no Azure], respectivamente.

  [Baixar o SDK do Azure para PHP]: #DownloadSdk
  [Como: Criar um projeto de Serviço de Nuvem]: #CreateProject
  [Como: Adicionar funções de trabalho e Web PHP]: #AddRole
  [Como: Especificar a versão do PHP interno]: #SpecifyPHPVersion
  [Como: Personalizar o tempo de execução do PHP interno]: #CustomizePHP
  [Como: Usar seu próprio tempo de execução PHP]: #OwnPHP
  [Como: Executar seu aplicativo nos Emuladores de Computação e de Armazenamento]: #Emulators
  [Como: Publicar seu aplicativo]: #Publish
  [Sites do Azure]: /pt-br/develop/net/fundamentals/compute/#WebSites
  [Máquinas Virtuais do Azure]: /pt-br/develop/net/fundamentals/compute/#VMachine
  [Serviços de Nuvem do Azure]: /pt-br/develop/net/fundamentals/compute/#CloudServices
  [O que é um Serviço de Nuvem]: /pt-br/manage/services/cloud-services/what-is-a-cloud-service/
  [SDK do Azure para PHP]: /pt-br/develop/php/common-tasks/download-php-sdk/
  [Instalar o PowerShell do Azure e o Emuladores do Azure]: http://go.microsoft.com/fwlink/?LinkId=253447&clcid=0x409
  [Drivers da Microsoft para PHP para SQL Server]: http://php.net/sqlsrv
  [SQL Server Native Client 2012]: http://msdn.microsoft.com/pt-br/sqlserver/aa937733.aspx
  [instalador sqlncli.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
  [Como: Importar configurações de publicação]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [Como: Implantar um serviço de nuvem no Azure]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/#Deploy
