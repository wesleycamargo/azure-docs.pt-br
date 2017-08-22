---
title: "Instalar o .NET em uma Função do Serviço de Nuvem | Microsoft Docs"
description: "Este artigo descreve como instalar o .NET Framework manualmente em funções de trabalho ou Web do serviço de nuvem."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e6154d990e10f67d4b30b889a62a99cedcbfccbe
ms.contentlocale: pt-br
ms.lasthandoff: 07/25/2017

---

# <a name="install-net-on-a-cloud-service-role"></a>Instalar o .NET em uma Função do Serviço de Nuvem
Este artigo descreve como instalar versões diferentes do .NET Framework nas Funções de Trabalho e Web do Serviço de Nuvem em comparação com o que vem com o SO Convidado. Por exemplo, você pode usar estas etapas para instalar o .NET 4.6.1 no SO convidado do Azure Família 4, que não vem com nenhuma versão do .NET 4.6. Para obter as informações mais recentes sobre versões do SO convidado, consulte [Notícias sobre a versão do SO Convidado do Azure](cloud-services-guestos-update-matrix.md).

>[!NOTE]
>O SO Convidado 5 inclui o .NET 4.6

>[!IMPORTANT]
>O SDK 2.9 do Azure contém uma restrição na implantação do .NET 4.6 no SO Convidado 4 ou inferior. Uma correção está disponível [aqui](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

O processo de instalação do .NET em suas funções de trabalho e web envolve incluir o pacote do instalador do .NET como parte do seu projeto de nuvem e iniciar o instalador como parte das tarefas de inicialização da função.  

## <a name="add-the-net-installer-to-your-project"></a>Adicione o instalador do .NET ao seu projeto
* Baixe o instalador da Web do .NET Framework que você deseja instalar
  * [Instalador da Web do .NET 4.7](http://go.microsoft.com/fwlink/?LinkId=825298)
  * [.NET 4.6.1 Web Installer](http://go.microsoft.com/fwlink/?LinkId=671729)

* Para uma função Web
  1. No **Gerenciador de Soluções**, em **Funções** no projeto do serviço de nuvem, clique com botão direito do mouse em sua função e selecione **Adicionar > Nova Pasta**. Crie uma pasta chamada *bin*
  2. Clique com o botão direito do mouse na pasta **bin** e selecione **Adicionar > Item Existente**. Selecione o instalador do .NET e adicione-o à pasta bin.
  
* Para uma função de trabalho
  1. Clique com o botão direito do mouse em sua função e selecione **Adicionar > Item Existente**. Selecione o instalador do .NET e adicione-o à função. 

Os arquivos adicionados dessa maneira à pasta de conteúdo de função são automaticamente adicionados ao pacote de serviço de nuvem e implantados em um local consistente na máquina virtual. Repita esse processo para todas as funções de trabalho e web no seu serviço de nuvem para que todas as funções tenham uma cópia do instalador.

> [!NOTE]
> Você deve instalar o .NET 4.6.1 em sua função de Serviço de Nuvem, mesmo se o aplicativo for para .NET 4.6. O SO Convidado do Azure inclui as atualizações [3098779](https://support.microsoft.com/kb/3098779) e [3097997](https://support.microsoft.com/kb/3097997). A instalação do .NET 4.6 sobre essas atualizações pode causar problemas durante a execução de seus aplicativos .NET, portanto, instale o .NET 4.6.1 diretamente em vez do .NET 4.6. Para saber mais, confira [KB 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Conteúdos de função com arquivos do instalador][1]

## <a name="define-startup-tasks-for-your-roles"></a>Defina tarefas de inicialização para suas funções
As tarefas de inicialização permitem que você execute operações antes de uma função iniciar. Instalar o .NET Framework como parte da tarefa de inicialização garante que o Framework seja instalado antes que qualquer um de seus códigos de aplicativo seja executado. Para obter mais informações sobre as tarefas de inicialização, consulte: [Execução de tarefas de inicialização no Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte ao arquivo *ServiceDefinition.csdef* sob o nó **WebRole** ou **WorkerRole** para todas as funções:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    A configuração anterior executa o comando do console *install.cmd* com privilégios de administrador a fim de instalar o .NET Framework. A configuração também cria um *LocalStorage* com o nome *NETFXInstall*. O script de inicialização define a pasta temporária para usar esse recurso de armazenamento local. É importante definir o tamanho desse recurso como, pelo menos, 1.024 MB para garantir que a estrutura seja instalada corretamente. Para saber mais sobre as tarefas de inicialização, confira: [Tarefas de inicialização comuns do Serviço de nuvem](cloud-services-startup-tasks-common.md) 

2. Crie um arquivo **install.cmd** e adicione-o a todas as funções clicando com o botão direito do mouse na função e selecionando **Adicionar > Item Existente...**. Agora todas as funções devem ter o arquivo do instalador do .NET, assim como o arquivo install.cmd.
   
   ![Conteúdos de função com todos os arquivos][2]
   
   > [!NOTE]
   > Use um editor de texto básico como o bloco de notas para criar esse arquivo. Se você usar o Visual Studio para criar um arquivo de texto e, em seguida, renomeá-lo como '.cmd', o arquivo ainda poderá conter uma marca de ordem de byte UTF-8 e a execução da primeira linha do script resultará em um erro. Verifique se a primeira linha do arquivo é um comando REM, isso poderá ignorar o processamento de marca de ordem de byte UTF-8. 
   > 
   > 

3. Adicione o seguinte script ao arquivo **install.cmd** :
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" *****
    set netfx="NDP47"

    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."

    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit

    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%

    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp

    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp

    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp

    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"

    :NDP47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"

    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%

    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed

    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%

    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%

    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%

    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%

    :exit
    EXIT /B 0
    ```
   
    O script de instalação verifica se a versão do .NET Framework já está instalada no computador ao consultar o Registro. Se a versão do .NET não estiver instalada, o instalador da Web do .Net será iniciado. Para ajudar na solução de problemas, o script registrará todas as atividades em um arquivo denominado *startuptasklog-(currentdatetime).txt* colocado no armazenamento local *InstallLogs*.
   
   > [!NOTE]
   > O script ainda mostra como instalar o .NET 4.5.2 ou o .NET 4.6 para manter a continuidade. Não é necessário instalar manualmente o .NET 4.5.2, pois ele já está disponível no SO Convidado do Azure. Em vez de instalar o .NET 4.6, instale diretamente o .NET 4.6.1 devido à [KB 3118750](https://support.microsoft.com/kb/3118750).
   > 
   > 

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurar o diagnóstico para transferir os logs das tarefas de inicialização para o Armazenamento de Blobs
Para simplificar a solução de quaisquer problemas de instalação, você pode configurar o Diagnóstico do Azure para transferir os arquivos de log gerados pelo script de inicialização ou pelo instalador do .NET para o Armazenamento de Blobs. Com essa abordagem, você pode exibir os logs simplesmente ao baixar os arquivos de log do armazenamento de blobs em vez de utilizar a área de trabalho remota na função.

Para configurar o diagnóstico, abra o *diagnostics.wadcfgx* e adicione o seguinte abaixo do nó **Directories**: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Esse XML configura o diagnóstico do Azure para transferir todos os arquivos no diretório do *log* sob o recurso *NETFXInstall* para a conta de armazenamento de diagnóstico no contêiner de blobs *netfx-install*.

## <a name="deploying-your-service"></a>Implantando o serviço
Ao implantar o serviço, as tarefas de inicialização instalarão o .NET Framework, se ele ainda não estiver instalado. Suas funções ficam no estado *ocupado* enquanto o Framework estiver sendo instalado e podem até mesmo reiniciar se a instalação do Framework exigir. 

## <a name="additional-resources"></a>Recursos adicionais
* [Instalação do .NET Framework][Installing the .NET Framework]
* [Como determinar quais versões do .NET Framework estão instaladas][How to: Determine Which .NET Framework Versions Are Installed]
* [Solução de problemas de instalações do .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



