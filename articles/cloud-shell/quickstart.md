---
title: "Guia de início rápido do Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: "Guia de início rápido para o Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b087ce2b7ee71b867e2123a70a2bfbf0fbea1e3b
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="quickstart"></a>Início rápido
Este documento fornece detalhes sobre como usar o Azure Cloud Shell no [Portal do Azure](https://ms.portal.azure.com/).

## <a name="start-cloud-shell"></a>Iniciar o Cloud Shell
1. Inicie o **Cloud Shell** no painel de navegação superior do Portal do Azure <br>
![](media/shell-icon.png)
2. Selecione a assinatura com a qual uma conta de armazenamento e um compartilhamento de arquivos do Azure serão criados <br>
![](media/storage-prompt.png)
3. Selecione "Criar armazenamento"

> [!TIP]
> Você é autenticado automaticamente na CLI do Azure 2.0 em cada sessão.

### <a name="set-your-subscription"></a>Definir sua assinatura
1. Liste as assinaturas a que você tem acesso: <br>
`az account list`
2. Defina sua assinatura preferencial: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> Sua assinatura será lembrada para futuras sessões usando o `azureProfile.json` mantido no diretório $Home.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um novo grupo de recursos no Oeste dos EUA chamado "MyRG": <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Crie uma VM do Ubuntu em seu novo grupo de recursos. A CLI do Azure 2.0 criará chaves SSH e configurará a VM com elas. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Por padrão, as chaves pública e privada usadas para autenticar sua VM são colocadas em `/User/.ssh/id_rsa` e `/User/.ssh/id_rsa.pub` pela CLI do Azure 2.0. Sua pasta .ssh é mantida na imagem de 5 GB do compartilhamento de arquivos anexado do Azure.

Seu nome de usuário nessa VM será o nome de usuário usado no Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH em sua VM Linux
1. Pesquise pelo nome de sua VM na barra de pesquisa do Portal do Azure
2. Clique em "Conectar" e execute: `ssh username@ipaddress`

![](media/sshcmd-copy.png)

Após estabelecer a conexão SSH, você deverá ver o prompt de boas-vindas do Ubuntu.
![](media/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Limpando 
Exclua o grupo de recursos e todos os recursos dentro dele: <br>
Execute o `az group delete -n MyRG`

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre como persistir o armazenamento no Cloud Shell](persisting-shell-storage.md) [Saiba mais sobre a CLI do Azure 2.0] (https://docs.microsoft.com/cli/azure/) [Saiba mais sobre o Armazenamento de Arquivos do Azure] (https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 

