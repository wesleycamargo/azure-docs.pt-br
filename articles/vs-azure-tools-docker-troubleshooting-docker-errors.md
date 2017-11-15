---
title: "Solução de erros do cliente Docker no Windows usando o Visual Studio | Microsoft Docs"
description: "Solucione os problemas encontrados na utilização do Visual Studio para criar e implantar aplicativos Web no Docker ou no Windows usando o Visual Studio 2017."
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Solucionar problemas de desenvolvimento do Visual Studio 2017 com o Docker

Ao trabalhar com Ferramentas do Visual Studio para Docker, você poderá encontrar problemas ao compilar ou depurar seu aplicativo. A seguir estão algumas etapas comuns para solução de problemas.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Compartilhamento de volume não está habilitado. Habilite o compartilhamento de volume nas configurações do Docker CE para Windows (somente contêineres do Linux)

Para resolver esse problema:

1. Clique com o botão direito do mouse em **Docker para Windows** na área de notificação e selecione **Configurações**.
1. Selecione **Unidades Compartilhadas** e compartilhe a unidade do sistema junto com a unidade na qual o projeto reside.

> [!NOTE]
> Se o arquivos aparecer compartilhado, você ainda precisará clicar no link "Redefinir credenciais…" na parte inferior da caixa de diálogo para reabilitar o volume de compartilhamento.

![unidades compartilhadas](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>Não é possível iniciar a depuração

Uma razão para isso pode estar relacionada a ter componentes de depuração obsoletos na pasta de perfil do usuário. Execute os seguintes comandos para remover essas pastas para que os componentes de depuração mais recentes sejam baixados na próxima sessão de depuração.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Erros específicos de rede ao depurar o aplicativo

Tente executar o script baixado de [Rede de Host do Contêiner de Limpeza](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), que atualizará os componentes relacionados à rede no seu computador host.


## <a name="microsoftdockertools-github-repo"></a>Repositório Microsoft/DockerTools GitHub

Para quaisquer outros problemas que encontrar, consulte problemas do [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues).