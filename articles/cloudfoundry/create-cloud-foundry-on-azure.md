---
title: Criar o Cloud Foundry no Azure
description: Saiba como configurar os parâmetros necessários para provisionar um cluster de PCF Cloud Foundry no Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250541"
---
# <a name="create-cloud-foundry-on-azure"></a>Criar o Cloud Foundry no Azure

Este tutorial fornece etapas rápidas sobre como criar e gerar os parâmetros necessários para provisionar um cluster de PCF Pivotal Cloud Foundry no Azure.  A solução Pivotal Cloud Foundry pode ser encontrada realizando uma pesquisa no Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Texto alternativo da imagem](media/deploy/pcf-marketplace.png "Pesquisar o Pivotal Cloud Foundry no Azure")


## <a name="generate-an-ssh-public-key"></a>Gerar uma chave pública SSH

Há várias maneiras para gerar uma chave SSH pública usando o Windows, Mac ou Linux.

```Bash
ssh-keygen -t rsa -b 2048
```
- Clique aqui para ver as [instruções]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) para o seu ambiente.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

> [!NOTE]
>
> Criar uma entidade de serviço exige uma permissão de conta de proprietário.  Além disso, é possível escrever um script para automatizar a criação da entidade de serviço. Por exemplo, usando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) da CLI do Azure.

1. Faça logon na sua conta do Azure.

    `az login`

    ![Texto alternativo da imagem](media/deploy/az-login-output.png "Logon da CLI do Azure")
 
    Copie o valor de “id” como sua **ID da assinatura** e o valor de **tenantId** para ser usado mais tarde.

2. Defina sua assinatura padrão para essa configuração.

    `az account set -s {id}`

3. Crie um aplicativo do AAD para o PCF e especifique uma senha alfanumérica exclusiva.  Armazene a senha como seu **clientSecret** para ser usada posteriormente.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copie o valor de “appId” na saída como sua **ClientID** para ser usada posteriormente.

    > [!NOTE]
    >
    > Escolha sua própria página inicial de aplicativo e URI do identificador.  Por exemplo: http://www.contoso.com.

4. Crie uma entidade de serviço com sua nova “appId”.

    `az ad sp create --id {appId}`

5. Defina a função de permissão da sua entidade de serviço como um **Colaborador**.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Ou então, você também pode usar…

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Texto alternativo da imagem](media/deploy/svc-princ.png "Atribuição de função da entidade de serviço")

6. Verifique se é possível fazer logon com êxito na sua entidade de serviço usando a appId, a senha e a tenantId.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Crie um arquivo .json no seguinte formato usando Use todos os valores **ID da assinatura**, **tenantId**, **clientID** e **clientSecret** que você copiou acima.  Salve o arquivo.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Obter o Pivotal Network Token

1. Registre-se ou entre com sua conta do [Pivotal Network](https://network.pivotal.io).
2. Clique no seu nome de perfil no canto superior direito da página e selecione **Editar perfil”.
3. Role até a parte inferior da página e copie o valor **LEGACY API TOKEN**.  Esse é seu valor de **Pivotal Network Token** que será usado posteriormente.

## <a name="provision-your-cloud-foundry-on-azure"></a>Provisionar seu Cloud Foundry no Azure

1. Agora você tem todos os parâmetros necessários para provisionar seu cluster do [Pivotal Cloud Foundry no Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
2. Insira os parâmetros e crie seu cluster do PCF.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Verifique a implantação e faça logon Pivotal Ops Manager

1. Seu cluster do PCF mostrará um status de implantação.

    ![Texto alternativo da imagem](media/deploy/deployment.png "Status de implantação do Azure")

2. Clique no link **Implantações** na barra de navegação à esquerda para obter as credenciais para o PCF Ops Manager e, em seguida, clique no **Nome da implantação** na próxima página.
3. No painel de navegação à esquerda, clique no link **Saídas** para exibir a URL, o Nome de usuário e a Senha do PCF Ops Manager.  O valor de “OPSMAN-FQDN” é a URL.
 
    ![Texto alternativo da imagem](media/deploy/deploy-outputs.png "Saída da implantação do Cloud Foundry")
 
4. Inicie a URL em um navegador da Web e insira as credenciais da etapa anterior para fazer logon.

    ![Texto alternativo da imagem](media/deploy/pivotal-login.png "Página de logon do Pivotal")
         
    > [!NOTE]
    >
    > Se o navegador Internet Explorer falhar devido a uma mensagem de aviso de site não seguro, clique em “Mais informações” e “Vá para a página da Web.  Para o Firefox, clique em Avançar e adicione a certificação para continuar.

5. O PCF Ops Manager exibirá as instâncias implantadas do Azure. Agora você pode começar a implantar e gerenciar seus aplicativos aqui.
               
    ![Texto alternativo da imagem](media/deploy/ops-mgr.png "Instância implantada do Azure no Pivotal")
 
