---
title: "Solução de problemas de erros do cliente Docker no Windows usando o Visual Studio | Microsoft Docs"
description: "Solucione os problemas encontrados na utilização do Visual Studio para criar e implantar aplicativos Web no Docker ou no Windows usando o Visual Studio."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Solucionar problemas de desenvolvimento do Docker do Visual Studio

Ao trabalhar com o Visual Studio Tools para Docker Preview, você pode encontrar alguns problemas devido à natureza de versão de visualização.
Veja a seguir alguns problemas comuns e suas resoluções.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Contêineres do Linux**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Erros de build ocorrem durante a depuração de um aplicativo Web ou de console do .NET Core.  

Isso pode estar relacionado ao não compartilhamento da unidade na qual reside o projeto com o Docker para Windows.  Você poderá receber um erro como o seguinte:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Para resolvê-lo, clique com o botão direito do mouse no ícone de bandeja do sistema do Docker para Windows e selecione “Configurações...”.  Clique na guia “Unidades Compartilhadas” e compartilhe a letra da unidade na qual reside o projeto.

### <a name="windows-containers"></a>**Contêineres do Windows**

O conteúdo a seguir é específico à solução de problemas durante a depuração de aplicativos Web e de console do .NET Framework em contêineres do Windows

### <a name="pre-requisites"></a>Pré-requisitos

1. Visual Studio 2017 RC (ou posterior) com o .NET Core e a carga de trabalho do Docker (Visualização) instalados.
2. Atualização de Aniversário do Windows 10 com os últimos patches do Windows Update instalados.
3. Docker para Windows (Beta) – https://docs.docker.com/docker-for-windows/ (Versão 1.12.2-beta28 7813 ou posterior).
4. No ícone de bandeja do sistema do Docker para Windows, selecione “Mudar para contêineres do Windows”.  Após a reinicialização do computador, garanta que essa configuração é mantida.

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>Se você clicar em **Docker: Depurar Projeto**, isso resultará no erro “A versão do cliente 1.22 é muito antiga.  A versão mínima de API com suporte é 1.24. Atualize o cliente para uma versão mais nova”.

A versão 1.13-RC2-beta31 (9123) ou posterior do Docker para Windows exige o uso da versão “2.1” do Docker Compose.   Para corrigir isso, altere todas as ocorrências de versão “2” nos arquivos docker-compose*.yml dentro do projeto. Os modelos padrão que o Visual Studio adiciona ao projeto serão atualizados em uma versão futura das ferramentas. 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>A saída do console não é exibida na janela de saída do Visual Studio durante a depuração de um aplicativo de console.

Esse é um problema conhecido no Depurador do Visual Studio (msvsmon.exe), que, no momento, não é projetado para esse cenário.  Estamos buscando maneiras de fornecer suporte para esse recurso em uma versão futura.  Para ver a saída por meio do aplicativo de console no Visual Studio, use “Docker: Iniciar Projeto”, que é equivalente a “Iniciar sem Depuração”.

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>A depuração de aplicativos Web com a configuração de versão falha com o erro Proibido (403).

Para resolver o problema, abra o arquivo web.release.config na solução e comente ou exclua as seguintes linhas:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>Ao mudar para contêineres do Windows, potencialmente, é possível ver uma caixa de diálogo de erro com a mensagem “Resposta de erro do daemon: tempo limite de E/S”.

É possível acompanhar esse problema no Docker para Windows em https://github.com/docker/for-win/issues/178.


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Contêineres do Linux**

### <a name="unable-to-validate-volume-mapping"></a>Não é possível validar o mapeamento de volume
O mapeamento de volume é necessário para compartilhar o código-fonte e os binários do seu aplicativo com a pasta de aplicativo no contêiner.  Mapeamentos de volume específico estão contidos nos arquivos docker-compose.dev.debug.yml e docker-compose.dev.release.yml. Como os arquivos são alterados em seu computador host, os contêineres refletem essas alterações em uma estrutura de pastas semelhante.

Para habilitar o mapeamento de volume, abra **Configurações...** no ícone de bandeja do Docker do Windows "moby" e selecione a guia **Unidades Compartilhadas**.  Garanta que a letra da unidade que hospeda o projeto, bem como a letra da unidade na qual reside o %USERPROFILE%, são compartilhadas marcando-as e, em seguida, clicando em **Aplicar**.

Para testar se o mapeamento do volume está funcionando e se uma ou mais unidades foram compartilhadas, pressione Recompilar e F5 no Visual Studio ou tente o seguinte em um prompt de comando:

*Em um prompt de comando do Windows*

> [!Note]
> Este exemplo pressupõe que a pasta Usuários está localizada na unidade “C” e que ela foi compartilhada.
> Atualize, conforme necessário, se você tiver compartilhado outra unidade.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*No contêiner do Linux*

```
/ # ls
```

Você deverá ver uma listagem de diretórios da pasta Usuários/Público.
Se nenhum arquivo for exibido, e sua pasta /c/Usuários/Público não estiver vazia, o mapeamento de volume não estará configurado corretamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Mude para o diretório wormhole para ver o conteúdo do diretório `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!Note]
> Ao trabalhar com VMs Linux, o sistema de arquivos do contêiner diferencia maiúsculas de minúsculas.

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Compilação: a tarefa "PrepareForBuild" falhou inesperadamente.

Microsoft.DotNet.Docker.CommandLine.ClientException: erro ao tentar se conectar:

Verifique se o host do Docker padrão está em execução. Abra um prompt de comando e execute:

```
docker info
```

Se isso retorna um erro, tente iniciar o aplicativo da área de trabalho **Docker para Windows** .  Se o aplicativo da área de trabalho estiver em execução, o ícone **moby** na bandeja deverá estar visível. Clique com o botão direito do mouse no ícone de bandeja e abra **Configurações**.  Clique na guia **Redefinir** e, em seguida, em **Reiniciar Docker...**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Atualização manual da versão 0.31 para 0.40


1. Fazer backup do projeto
1. Exclua os seguintes arquivos do projeto:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Feche a Solução e remova as seguintes linhas do arquivo .xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Reabra a Solução
1. Remova as seguintes linhas do arquivo Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Remova os seguintes arquivos relacionados ao Docker de project.json no publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Desinstale a versão anterior e instale as ferramentas do Docker 0.40 e, em seguida, **Adicionar->Suporte ao Docker** novamente no menu de contexto para o ASP.NET Core Web ou aplicativo de Console. Isso adiciona os novos artefatos necessários do Docker de volta ao projeto.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Uma caixa de diálogo de erro ocorre ao tentar **Adicionar->Suporte ao Docker** ou Depurar (F5) um aplicativo do ASP.NET Core em um contêiner

Ocasionalmente, vimos que ao desinstalar e instalar extensões, o cache MEF (Managed Extensibility Framework) do Visual Studio pode ser corrompido. Quando isso ocorre, pode causar várias caixas de diálogo de erro durante a adição do Suporte ao Docker e/ou tentativa de executar ou Depurar (F5) o aplicativo ASP.NET Core. Como solução temporária, execute as seguintes etapas para excluir e gerar novamente o cache MEF.

1. Feche todas as instâncias do Visual Studio
1. Abra %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Exclua as seguintes pastas
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abra o Visual Studio
1. Tente o cenário novamente



<!--HONumber=Dec16_HO2-->


