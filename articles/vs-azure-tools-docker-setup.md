---
title: Configurar um host do Docker com o VirtualBox | Microsoft Docs
description: Instruções passo a passo para configurar uma instância de Docker padrão usando a máquina Docker e o VirtualBox
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 11e238fa901a164df1dfd896e38df828601e650b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Configurar um host do Docker com o VirtualBox
## <a name="overview"></a>Visão geral
Este artigo orienta você pela configuração de uma instância de Docker padrão usando a máquina Docker e o VirtualBox. Se você estiver usando o [Docker para Windows beta](http://beta.docker.com/), essa configuração não é necessária.

## <a name="prerequisites"></a>pré-requisitos
As ferramentas a seguir precisam ser instaladas.

* [Caixa de Ferramentas do Docker](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configurando o cliente Docker com o Windows PowerShell
Para configurar um cliente Docker, apenas abra o Windows PowerShell e execute as seguintes etapas:

1. Crie uma instância de host do docker padrão.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Verifique se a instância padrão está configurada e em execução. (Você deve ver uma instância chamada “padrão” em execução.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![saída do docker-machine Is][0]
3. Defina o padrão como o host atual e configure seu shell.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Exiba os contêineres do Docker ativo. A lista deve estar vazia.
   
    ```PowerShell
    docker ps
    ```
   
    ![saída do docker ps][1]

> [!NOTE]
> Sempre que você reinicializar o computador de desenvolvimento, precisará reiniciar o host do Docker local.
> Para fazer isso, envie o seguinte comando em um prompt de comando: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
