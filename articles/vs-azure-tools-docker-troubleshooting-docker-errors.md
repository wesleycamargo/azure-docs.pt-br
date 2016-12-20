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
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae041787ad23050b8864341dbc39ea4a17954d0f


---
# <a name="troubleshooting-visual-studio-docker-development"></a>Solucionar problemas de desenvolvimento do Docker do Visual Studio
Ao trabalhar com o Visual Studio Tools para Docker Preview, você pode encontrar alguns problemas devido à natureza de versão de visualização.
Veja a seguir alguns problemas comuns e suas resoluções.

## <a name="unable-to-validate-volume-mapping"></a>Não é possível validar o mapeamento de volume
O mapeamento de volume é necessário para compartilhar o código-fonte e os binários do seu aplicativo com a pasta de aplicativo no contêiner.  Mapeamentos de volume específico estão contidos nos arquivos docker-compose.dev.debug.yml e docker-compose.dev.release.yml. Como os arquivos são alterados em seu computador host, os contêineres refletem essas alterações em uma estrutura de pastas semelhante.

Para habilitar o mapeamento de volume, abra **Configurações...** no ícone de bandeja do Docker do Windows "moby" e selecione a guia **Unidades Compartilhadas**.  Certifique-se de que a letra da unidade que hospeda o projeto, bem como a letra da unidade em que reside o %USERPROFILE% são compartilhadas ao verificá-las e, em seguida, clicando em **Aplicar**.

Para testar se o mapeamento do volume está funcionando, depois que as unidades forem compartilhadas, recrie e aperte F5 no Visual Studio ou tente o seguinte de um prompt de comando:

*Em um prompt de comando do Windows*

*[Observação: isso pressupõe que sua pasta de usuários está localizada na unidade "C" e que ela foi compartilhada.  Atualizar conforme necessário se você tiver compartilhado uma unidade diferente]*

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

**Observação:** *ao trabalhar com as VMs do Linux, o sistema de arquivos do contêiner irá diferenciar as letras maiúsculas das minúsculas.*

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Compilação: a tarefa "PrepareForBuild" falhou inesperadamente.
Microsoft.DotNet.Docker.CommandLine.ClientException: erro ao tentar se conectar:

Verifique se o host do Docker padrão está em execução. Abra um prompt de comando e execute:

```
docker info
```

Se isso retorna um erro, tente iniciar o aplicativo da área de trabalho **Docker para Windows** .  Se o aplicativo da área de trabalho estiver em execução, o ícone **moby** na bandeja deve estar visível. Clique com o botão direito do mouse no ícone de bandeja e abra **Configurações**.  Clique na guia **Redefinir** e, em seguida, **Docker de reinicialização..**.

## <a name="manually-upgrading-from-version-031-to-040"></a>Atualização manual da versão 0.31 para 0.40
1. Fazer backup do projeto
2. Exclua os seguintes arquivos do projeto:
   
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
3. Feche a Solução e remova as seguintes linhas do arquivo .xproj:
   
    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```
4. Reabra a Solução
5. Remova as seguintes linhas do arquivo Properties\launchSettings.json:
   
    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```
6. Remova os seguintes arquivos relacionados ao Docker de project.json no publishOptions:
   
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
7. Desinstale a versão anterior e instale as ferramentas do Docker 0.40 e, em seguida, **Adicionar->Suporte ao Docker** novamente no menu de contexto para o ASP.NET Core Web ou aplicativo de Console. Isso adicionará os novo artefatos necessários do Docker de volta ao seu projeto. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Uma caixa de diálogo de erro ocorre ao tentar **Adicionar->Suporte ao Docker** ou Depurar (F5) um aplicativo do ASP.NET Core em um contêiner
Ocasionalmente, vimos que ao desinstalar e instalar extensões, o cache MEF (Managed Extensibility Framework) do Visual Studio pode ser corrompido. Quando isso ocorre, ele pode causar várias caixas de diálogo de erro ao adicionar Suporte ao Docker e/ou tentar executar ou Depurar (F5) seu aplicativo do ASP.NET Core. Como solução temporária, execute as seguintes etapas para excluir e gerar novamente o cache MEF.

1. Feche todas as instâncias do Visual Studio
2. Abra %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
3. Exclua as seguintes pastas
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
4. Abra o Visual Studio
5. Tente o cenário novamente 




<!--HONumber=Nov16_HO3-->


