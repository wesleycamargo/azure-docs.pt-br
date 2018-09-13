---
title: Gerenciar o Key Vault no Azure Stack usando o portal | Microsoft Docs
description: Saiba como gerenciar o Cofre de chaves no Azure Stack por meio do portal
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.openlocfilehash: 51c04a567ff953c4e84930e3feae448f78627683
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713928"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Gerenciar o Key Vault no Azure Stack por meio do portal

Você pode gerenciar o Cofre de chaves no Azure Stack por meio do portal do Azure Stack. Este artigo ajuda você a começar a criar e gerenciar um cofre de chaves no Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos

Você deve assinar uma oferta que inclui o serviço do Azure Key Vault.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

1. Entrar para o [portal do usuário](https://portal.local.azurestack.external).

2. O painel, selecione **+ criar um recurso** > **segurança + identidade** > **Key Vault**.

    ![Tela de Cofre de chaves](media/azure-stack-kv-manage-portal/image1.png)

3. No **criar Key Vault** painel, atribuir uma **nome** para o cofre. Nomes de cofre podem conter apenas caracteres alfanuméricos e o caractere especial de hífen (-). Eles não devem começar com um número.

4. Escolha uma **assinatura** da lista de assinaturas disponíveis. Todas as assinaturas que oferecem o serviço Key Vault são exibidas na lista suspensa.

5. Selecione um **Grupo de Recursos** existente ou crie um novo.

6. Selecione o **tipo de preço**.
    >[!NOTE]
    > Cofres de suporte do Kit de desenvolvimento do Azure Stack de chaves **Standard** somente SKUs.

7. Escolha um dos existentes **políticas de acesso** ou criar um novo. Uma política de acesso permite que você conceda permissões para um usuário, aplicativo ou um grupo de segurança executar operações com este cofre.

8. Como opção, escolha uma **política de acesso avançado** para habilitar o acesso aos recursos. Por exemplo: máquinas virtuais (VMs) para a implantação, o Gerenciador de recursos de implantação de modelo e o acesso ao Azure Disk Encryption para criptografia de volume.

9. Depois de definir as configurações, selecione **Okey**e, em seguida, selecione **criar**. Isso inicia a implantação do Cofre de chaves.

## <a name="manage-keys-and-secrets"></a>Gerenciar chaves e segredos

Depois de criar um cofre, use as seguintes etapas para criar e gerenciar chaves e segredos no cofre.

### <a name="create-a-key"></a>Criar uma chave

1. Entrar para o [portal do usuário](https://portal.local.azurestack.external).

2. O painel, selecione **todos os recursos**, selecione o Cofre de chaves que você criou anteriormente e, em seguida, selecione o **chaves** lado a lado.

3. No **teclas** painel, selecione **Add**.

4. No **criar uma chave** painel, na lista de **opções**, escolha o método que você deseja usar para criar uma chave. Você pode **Generate** uma nova chave **carregar** existente da chave ou use **restaurar Backup** para selecionar um backup de uma chave.

5. Insira um **nome** para sua chave. O nome da chave pode conter apenas caracteres alfanuméricos e hífen (-) caractere especial.

6. Opcionalmente, configure a **definir data de ativação** e **definir data de validade** valores para a sua chave.

7. Selecione **criar** para iniciar a implantação.

Depois que a chave é criada com êxito, você pode selecioná-lo sob **chaves** e exibir ou modificar suas propriedades. A seção de propriedades contém o **identificador de chave**, que é um identificador de URI (Uniform Resource) usado por aplicativos externos para acessar essa chave. Para limitar as operações sobre essa chave, defina as configurações em **operações permitidas**.

![Chave URI](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>Criar um segredo

1. Entrar para o [portal do usuário](https://portal.local.azurestack.external).
2. O painel, selecione **todos os recursos**, selecione o Cofre de chaves que você criou anteriormente e, em seguida, selecione o **segredos** lado a lado.

3. Sob **segredos**, selecione **Add**.

4. Sob **criar um segredo**, na lista de **opções de carregamento**, escolha uma opção pelo qual você deseja criar um segredo. Você pode criar um segredo **manualmente** se você digitar um valor para o segredo ou carregar um **certificado** em seu computador local.

5. Insira um **nome** para o segredo. O nome do segredo pode conter apenas caracteres alfanuméricos e hífen (-) caractere especial.

6. Opcionalmente, especifique o **tipo de conteúdo**e configurar valores para **definir data de ativação** e **definir data de validade** para o segredo.

7. Selecione **criar** para iniciar a implantação.

Depois que o segredo é criado com êxito, você pode selecioná-lo sob **segredos** e exibir ou modificar suas propriedades. O **identificador de segredo** é um URI que aplicativos externos podem usar para acessar este segredo.

![Segredo do URI](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>Próximas etapas

* [Implantar uma VM ao recuperar a senha armazenada no cofre de chaves](azure-stack-kv-deploy-vm-with-secret.md)
* [Implantar uma VM com certificado armazenado no cofre de chaves](azure-stack-kv-push-secret-into-vm.md)
