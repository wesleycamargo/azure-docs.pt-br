<properties
   pageTitle="Instalar o .NET em uma Função do Serviço de Nuvem"
   description="Este artigo descreve como instalar o .NET Framework manualmente em funções de trabalho ou Web do serviço de nuvem."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/23/2015"
   ms.author="saurabh"/>

# Instalar o .NET em uma Função do Serviço de Nuvem 

Este artigo descreve como instalar o .NET Framework em funções de trabalho ou Web do serviço de nuvem. Você pode usar estas etapas para instalar o .NET framework 4.5.2 na Família 4 de SO Convidado do Azure. Para obter as informações mais recentes sobre versões do sistema operacional convidado, consulte [Azure Guest OS Releases and SDK Compatibility Matrix (Matriz de compatibilidade do SDK e das versões do sistema operacional convidado do Azure)](cloud-services-guestos-update-matrix.md).

O processo de instalação do .NET em suas funções de trabalho e web envolve incluir o pacote do instalador do .NET como parte do seu projeto de nuvem e iniciar o instalador como parte das tarefas de inicialização da função.

## Adicione o instalador do .NET ao seu projeto
1. Baixe o [instalador da Web do .NET 4.5.2](http://www.microsoft.com/pt-br/download/details.aspx?id=42643)
2. Para uma função Web
  1. Em **Gerenciador de Soluções**, em **Funções** no projeto de serviço de nuvem, clique com o botão direito do mouse na sua função e selecione **Adicionar>Nova Pasta**. Crie uma pasta chamada *bin*
  2. Clique com o botão direito na pasta **bin** e selecione **Adicionar>Item Existente**. Selecione o instalador do .NET e adicione-o à pasta bin.
3. Para uma função de trabalho
  1. Clique com o botão direito na sua função e selecione **Adicionar>Item Existente**. Selecione o instalador do .NET e adicione-o à função. 

Os arquivos adicionados dessa maneira à pasta de conteúdo de função serão automaticamente adicionados ao pacote de serviço de nuvem e implantados em um local consistente na máquina virtual. Repita esse processo para todas as funções de trabalho e web no seu serviço de nuvem para que todas as funções tenham uma cópia do instalador.

![Conteúdos de função com arquivos do instalador][1]

## Defina tarefas de inicialização para suas funções
As tarefas de inicialização permitem que você execute operações antes de uma função iniciar. Instalar o .NET Framework como parte da tarefa de inicialização garante que o Framework seja instalado antes de qualquer um de seus códigos de aplicativo ser executado. Para obter mais informações sobre as tarefas de inicialização, consulte: [Execução de tarefas de inicialização do Azure](https://msdn.microsoft.com/library/azure/hh180155.aspx).

1. Adicione o seguinte ao arquivo *ServiceDefinition.csdef* no nó *WebRole* ou *WorkerRole* para todas as funções:
	
	```xml
	 <LocalResources>
	    <LocalStorage name="InstallLogs" sizeInMB="5" cleanOnRoleRecycle="false" />
	 </LocalResources>
	 <Startup>
	    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
	        <Environment>
	        <Variable name="PathToInstallLogs">
	        <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='InstallLogs']/@path" />
	        </Variable>
	        </Environment>
	    </Task>
	 </Startup>
	```

	A configuração acima executará o comando do console *install.cmd* com privilégios de administrador para que ele possa instalar o .NET Framework. A configuração também cria um LocalStorage com o nome *InstallLogs* para armazenar quaisquer informações de log criadas pelo script de instalação. Para obter mais informações, consulte: [Usar o armazenamento local para armazenar arquivos durante a inicialização](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. Crie o arquivo install.cmd com o seguinte conteúdo:

	```
	REM install.cmd to install .NET Framework
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set startuptasklog=%PathToInstallLogs%\startuptasklog-%timestamp%.txt
	set netfxinstallerlog = %PathToInstallLogs%\NetFXInstallerLog-%timestamp%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo Checking if .NET 4.5.2 is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find "0x5cbf5"
	if %ERRORLEVEL%== 0 goto end
	echo Installing .NET 4.5.2. Log: %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0NDP452-KB2901954-Web.exe /q /serialdownload /log %netfxinstallerlog%
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	```

	O script de instalação verifica se o .NET 4.5.2 já está instalado no computador ao consultar o Registro. Se o .NET 4.5.2 não estiver instalado, o instalador da Web do .Net é iniciado. Para ajudar a solucionar problemas com quaisquer questões, o script registrará toda a atividade em um arquivo chamado *startuptasklog-(currentdatetime).txt* armazenado no armazenamento local *InstallLogs*.

	> [AZURE.NOTE]Use um editor de texto simples como o Bloco de Notas para criar esse arquivo. Se você usar o Visual Studio para criar um arquivo de texto e, em seguida, renomeá-lo como '.cmd', o arquivo ainda pode conter uma marca de ordem de byte UTF-8 e a execução da primeira linha do script resultará em um erro. Se você usar o Visual Studio para criar o arquivo, adicione um REM (Comentário) à primeira linha do arquivo para que ela seja ignorada ao executar.
      
3. Adicione o arquivo install.cmd a todas as funções clicando com o botão direito do mouse na função e selecionando **Adicionar>Item Existente...**. Agora todas as funções devem ter o arquivo do instalador do .NET, assim como o arquivo install.cmd.
	
	![Conteúdos de função com todos os arquivos][2]

## Configure o diagnóstico para transferir os logs de tarefas de inicialização para o armazenamento do blob (opcional)
Opcionalmente, você pode configurar o Diagnóstico do Azure para transferir os arquivos de log gerados pelo script de inicialização ou o instalador do .NET para o armazenamento de blob. Com essa abordagem, você pode exibir os logs simplesmente ao baixar os arquivos de log do armazenamento de blob em vez de precisar utilizar a área de trabalho remota na função.

Para configurar o diagnóstico, abra o *diagnostics.wadcfgx* e adicione o seguinte abaixo do *<Directories>* nó:

```xml 
<DataSources>
    <DirectoryConfiguration containerName="netfx-install">
    <LocalResource name="InstallLogs" relativePath="."/>
    </DirectoryConfiguration>
</DataSources>
```

Isso vai configurar o Diagnóstico do Azure para transferir todos os arquivos no recurso *InstallLogs* para a conta de armazenamento de diagnóstico no contêiner de blob *netfx-install*.

## Implantando o serviço 
Quando você implantar o serviço, as tarefas de inicialização serão executadas e instalarão o .NET Framework se ele ainda não estiver instalado. Suas funções estarão no estado ocupado enquanto o Framework estiver sendo instalado e podem até mesmo reiniciar se for necessário para a instalação do Framework.


## Recursos adicionais

- [Instalando o .NET Framework][]
- [Como determinar quais versões do .NET Framework estão instaladas][]
- [Solução de problemas de instalações do .NET Framework][]

[Como determinar quais versões do .NET Framework estão instaladas]: https://msdn.microsoft.com/library/hh925568.aspx
[Instalando o .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Solução de problemas de instalações do .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=62-->