---
title: Gerenciar o Cofre de chaves na pilha do Azure usando o portal | Microsoft Docs
description: Saiba como gerenciar o Cofre de chaves na pilha do Azure usando o portal
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: d263cbcc81be37eaedfdb771436fd13ef25362f8
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Gerenciar o Cofre de chaves na pilha do Azure usando o portal

Você pode gerenciar o Cofre de chaves na pilha do Azure usando o portal de pilha do Azure. Este artigo ajuda você a começar a criar e gerenciar um cofre de chaves na pilha do Azure. 

## <a name="prerequisites"></a>Pré-requisitos  

Você deve assinar uma oferta que inclui o serviço de Cofre de chaves do Azure.
 
## <a name="create-a-key-vault"></a>Criar um cofre de chave 

1. Entrar para o [portal do usuário](https://portal.local.azurestack.external).  

2. No painel, selecione **novo** > **segurança + identidade** > **Cofre de chaves**.  

    ![Tela de Cofre de chaves](media/azure-stack-kv-manage-portal/image1.png)  

3. No **criar Cofre de chaves** painel, atribua um **nome** para seu cofre. Nomes de cofre podem conter apenas caracteres alfanuméricos e caracteres especiais hífen (-). Eles não devem começar com um número.  

4. Escolha um **assinatura** da lista de assinaturas disponíveis. Todas as assinaturas que oferecem o serviço de Cofre de chaves são exibidas na lista suspensa.  

5. Selecione uma existente **grupo de recursos** ou criar um novo.  

6. Selecione o **preço**.  
    >[!NOTE]
    > Chave de cofres no suporte do Kit de desenvolvimento de pilha do Azure **padrão** SKUs.

7. Escolha uma das existente **políticas de acesso** ou criar um novo. Uma política de acesso permite que você conceda permissões para um usuário, aplicativo ou um grupo de segurança executar operações com este cofre.  

8. Como opção, escolha uma **política de acesso avançados** para habilitar recursos, como acesso a máquinas virtuais (VMs) para implantação, acesso ao Gerenciador de recursos para implantação de modelo e acesso a criptografia de disco do Azure para criptografia de volume. 
  
9.  Depois de configurar as configurações, selecione **Okey**e, em seguida, selecione **criar**. Isso inicia a implantação de Cofre de chaves. 

## <a name="manage-keys-and-secrets"></a>Gerenciar chaves e segredos

Depois de criar um cofre, use as etapas a seguir para criar e gerenciar chaves e segredos no cofre.

### <a name="create-a-key"></a>Criar uma chave

1. Entrar para o [portal do usuário](https://portal.local.azurestack.external).  

2. No painel, selecione **todos os recursos**, selecione o Cofre de chaves que você criou anteriormente e, em seguida, selecione o **chaves** lado a lado.  

3. No **chaves** painel, selecione **adicionar**. 

4. No **criar uma chave** painel, na lista de **opções**, escolha o método que você deseja usar para criar uma chave. Você pode **gerar** uma nova chave, **carregar** existente da chave, ou use **restaurar Backup** para selecionar um backup de uma chave.  

5. Insira um **nome** para sua chave. O nome da chave pode conter apenas caracteres alfanuméricos e o hífen (-) do caractere especial.  

6. Opcionalmente, configure o **definir a data de ativação** e **definir data de validade** valores para a chave.  

7. Selecione **criar** para iniciar a implantação.  

Depois que a chave é criada com êxito, você pode selecioná-lo em **chaves** e exibir ou modificar suas propriedades. A seção de propriedades contém o **identificador de chave**, que é um URI Uniform Resource Identifier () pelo qual os aplicativos externos podem acessar essa chave. Para limitar as operações nesta chave, defina as configurações em **operações permitidas**.

![Chave URI](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>Criar um segredo 

1. Entrar para o [portal do usuário](https://portal.local.azurestack.external).  
2. No painel, selecione **todos os recursos**, selecione o Cofre de chaves que você criou anteriormente e, em seguida, selecione o **segredos** lado a lado.  

3. Em **segredos**, selecione **adicionar**.  

4. Em **criar um segredo**, na lista de **opções de carregamento**, escolha uma opção que você deseja criar um segredo. Você pode criar um segredo **manualmente** se você inserir um valor para o segredo ou carregar um **certificado** em sua máquina local.  

5. Insira um **nome** para o segredo. O nome do segredo pode conter apenas caracteres alfanuméricos e o hífen (-) do caractere especial.  

6. Opcionalmente, especifique o **tipo de conteúdo**e defina valores para **definir a data de ativação** e **definir data de validade** para o segredo.  

7. Selecione **criar** para iniciar a implantação.  

Depois que o segredo é criado com êxito, você pode selecioná-lo em **segredos** e exibir ou modificar suas propriedades. A seção de propriedades contém um **identificador de segredo**, que é um URI que aplicativos externos podem acessar este segredo. 

![Segredo URI](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Próximas etapas
* [Implantar uma VM, recuperando a senha armazenada no cofre de chaves](azure-stack-kv-deploy-vm-with-secret.md) 
* [Implantar uma VM com o certificado armazenado no cofre de chaves](azure-stack-kv-push-secret-into-vm.md)     


