---
title: Criar uma conta do Lote no portal do Azure | Microsoft Docs
description: Aprenda a criar uma conta do Lote do Azure no portal do Azure para executar cargas de trabalho paralelas em larga escala na nuvem.
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 11f8c3f37e56e0b5c566c4abdb60697c5279e72a
ms.lasthandoff: 04/06/2017


---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Lote com o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [.NET de Gerenciamento do Lote](batch-management-dotnet.md)
> 
> 

Saiba como criar uma conta do Lote do Azure no [portal do Azure][azure_portal] e escolha as propriedades da conta que se ajustam ao seu cenário de computação. Saiba onde encontrar propriedades da conta importantes, como chaves de acesso e URLs de conta. 

Para saber mais sobre contas do Lote e cenários, confira a [visão geral do recurso](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Use o portal para criar uma conta do Lote em um dos dois *modos de alocação de pool*: o modo do **serviço Lote** ou o mais recente modo de **assinatura do usuário**, que requer mais configuração. Para saber mais sobre esses dois modos, confira a [visão geral do recurso](batch-api-basics.md#account). Para os recursos do modo de assinatura do usuário, confira também a [postagem do blog](https://blogs.technet.microsoft.com/windowshpc/2017/03/17/azure-batch-vnet-and-custom-image-support-for-virtual-machine-pools/).

## <a name="batch-service-mode"></a>Modo de serviço do Lote



1. Entre no [Portal do Azure][azure_portal].
2. Clique em **Novo** > **Computação** > **Serviço de Lote**.
   
    ![Lote no Marketplace][marketplace_portal]
3. A folha **Nova Conta do Lote** é exibida. Confira as descrições de cada elemento de folha abaixo.
   
    ![Criar uma conta do Batch][account_portal]
   
    a. **Nome da conta**: o nome da conta do Lote que você escolher deve ser exclusivo dentro da região do Azure onde a conta é criada (confira **Local** abaixo). O nome da conta pode conter apenas minúsculas ou números e deve ter 3 a 24 caracteres de comprimento.
   
    b. **Assinatura**: a assinatura na qual a conta do Lote será criada. Se você tiver somente uma assinatura, ela será selecionada por padrão.

    c. **Modo de alocação de pool**: selecione **Serviço Lote**.
   
    c. **Grupo de recursos**: selecione um grupo de recursos para sua nova conta do Lote ou, opcionalmente, crie um novo.
   
    d. **Local**: a região do Azure na qual a conta do Lote será criada. Somente as regiões com suporte da sua assinatura e do seu grupo de recursos são exibidas como opções.
   
    e. **Conta de armazenamento** (opcional): uma conta do Armazenamento do Azure de uso geral que você associa à sua nova conta do Lote. Isso é recomendado para a maioria das contas do Lote. Confira [Conta do Armazenamento do Azure vinculada](#linked-azure-storage-account) mais adiante neste artigo para obter mais detalhes.

4. Clique em **Criar** para criar a conta.
   
   O portal indica que a implantação está em andamento. Após a conclusão, uma notificação de **Implantações bem-sucedidas** aparece em **Notificações**.
   
## <a name="user-subscription-mode"></a>Modo de assinatura do usuário

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Permitir que o Lote do Azure acesse a assinatura (operação única)
Ao criar sua primeira conta do Lote no modo de assinatura do usuário, execute as etapas a seguir para registrar sua assinatura com o Lote. (Se você fez isso anteriormente, pule para a próxima seção.)

1. Entre no [Portal do Azure][azure_portal].

2. Clique em **Mais Serviços** > **Assinaturas**e clique na assinatura que você deseja usar para a conta do Lote. 

3. Na folha **Assinatura**, clique em **Controle de acesso (IAM)** > **Adicionar**.

    ![Controle de acesso de assinatura][subscription_access]

4. Na folha **Adicionar permissões**, selecione a função **Colaborador** e procure **MicrosoftAzureBatch** (sem espaços). Selecione **MicrosoftAzureBatch**e clique em **Salvar**.

    ![Adicionar permissões do Lote][add_permission]

### <a name="create-a-key-vault"></a>Criar um cofre de chave
No modo de assinatura do usuário, é necessário ter um Azure Key Vault que pertença ao mesmo grupo de recursos da conta do Lote a ser criada. Verifique se o grupo de recursos está em uma região em que o Lote esteja [disponível](https://azure.microsoft.com/regions/services/) e que tenha suporte pela assinatura.

1. No [portal do Azure][azure_portal], clique em **Novo** > **Segurança + Identidade** > **Key Vault**. 

2. Na folha **Criar Key Vault**, insira um nome para o cofre de chaves e crie um grupo de recursos na região desejada para a conta do Lote. Deixe as configurações restantes com valores padrão e clique em **Criar**.

### <a name="create-a-batch-account"></a>Criar uma conta do Batch

1. No [portal do Azure][azure_portal], clique em **Novo** > **Computação** > **Serviço Lote**.
   
    ![Lote no Marketplace][marketplace_portal]
3. A folha **Nova Conta do Lote** é exibida. Confira as descrições de cada elemento de folha abaixo.
   
    ![Criar uma conta do Batch][account_portal_byos]
   
    a. **Nome da conta**: o nome da conta do Lote que você escolher deve ser exclusivo dentro da região do Azure onde a conta é criada (confira **Local** abaixo). O nome da conta pode conter apenas minúsculas ou números e deve ter 3 a 24 caracteres de comprimento.
   
    b. **Assinatura**: se você tiver mais de uma assinatura, selecione a que você registrou com o serviço Lote.

    c. **Modo de alocação de pool**: selecione **Assinatura de usuário**.

    d. **Key Vault**: selecione o cofre de chaves que você criou para sua conta do Lote na seção anterior. Opcionalmente, crie um novo cofre de chaves. Depois de selecionar o cofre, marque a caixa de seleção para conceder ao Lote do Azure acesso ao cofre de chaves.
   
    c. **Grupo de recursos**: selecione o grupo de recursos em que você criou o cofre de chaves.
   
    d. **Local**: região do Azure em que você criou o cofre de chaves para a conta do Lote. 
   
    e. **Conta de armazenamento** (opcional): uma conta do Armazenamento do Azure de uso geral que você associa à sua nova conta do Lote. Isso é recomendado para a maioria das contas do Lote. Confira [Conta vinculada do Armazenamento do Azure](#linked-azure-storage-account) abaixo para obter mais detalhes.

4. Clique em **Criar** para criar a conta.
   
   O portal indica que a implantação está em andamento. Após a conclusão, uma notificação de **Implantações bem-sucedidas** aparece em **Notificações**.



## <a name="view-batch-account-properties"></a>Exibir propriedades de conta do Lote
Após a criação da conta, você poderá abrir a **folha Conta do Lote** para acessar suas propriedades e configurações. Você pode acessar todas as propriedades e configurações de conta usando o menu à esquerda da folha Conta de Lote.

![Folha Conta do Lote no portal do Azure][account_blade]

* **URL de conta do Lote**: ao desenvolver um aplicativo com as [APIs do Lote](batch-apis-tools.md#batch-development-apis), você precisará de uma URL de conta para acessar os recursos do Lote. Uma URL de conta do Lote tem o seguinte formato:
  
    `https://<account_name>.<region>.batch.azure.com`

![URL de conta do Lote no portal][account_url]

* **Chaves de acesso** (modo de serviço Lote): para autenticar o acesso à conta do Lote do aplicativo, você precisará de uma chave de acesso da conta. (Essa configuração não está disponível no modo de assinatura do usuário, em que você usa a autenticação do Azure Active Directory.)

    Para exibir ou regenerar chaves de acesso da sua conta do Lote, insira `keys` na caixa **Pesquisar** do menu à esquerda na folha Conta do Lote e, em seguida, selecione **Chaves**. 
  
    ![Chaves de conta do Lote no portal do Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Conta vinculada do Armazenamento do Azure

Você pode, opcionalmente, vincular uma conta de Armazenamento do Azure de finalidade geral à sua nova conta do Lote. O recurso de [pacotes de aplicativos](batch-application-packages.md) do Lote usa o armazenamento de Blobs do Azure, como faz a biblioteca [.NET de Convenções de Arquivo do Lote](batch-task-output.md). Esses recursos opcionais ajudarão você a implantar os aplicativos executados por suas tarefas do Lote, persistindo os dados que eles produzem.

É recomendável que você crie uma nova conta de armazenamento exclusivamente para uso com sua conta do Lote.

![Criação de uma conta de armazenamento de “finalidade geral”][storage_account]

> [!NOTE] 
> O Lote do Azure atualmente dá suporte apenas ao tipo de conta de Armazenamento de uso geral. Esse tipo de conta é descrito na etapa 5, [Criar uma conta de armazenamento] (../storage/storage-create-storage-account.md#create-a-storage-account), em [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md).
>
>

> [!WARNING]
> Tome cuidado ao regenerar as chaves de acesso de uma conta de Armazenamento vinculada. Regenere somente uma chave de conta do Armazenamento e clique em **Sincronizar Chaves** na folha Conta do Armazenamento vinculada. Aguarde cinco minutos para permitir que as chaves sejam propagadas para os nós de computação em seus pools e regenere e sincronize a outra chave, se necessário. Se você regenerar as chaves ao mesmo tempo, os nós de computação não poderão sincronizar nenhuma delas e, assim, perderão o acesso à conta de armazenamento.
> 
> 

![Regeneração de chaves da conta de armazenamento][4]

## <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote
Esteja ciente de que como sua assinatura do Azure e outros serviços do Azure, determinados [limites e cotas](batch-quota-limit.md) se aplicam a contas do Lote. As cotas atuais em uma conta do Lote aparecem no portal nas **Propriedades**da conta.

![Cotas de conta do Lote no portal do Azure][quotas]



Adicionalmente, muitas dessas cotas podem ser aumentadas com uma solicitação de suporte do produto gratuito enviada no portal do Azure. Confira [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter detalhes sobre a solicitação de aumentos de cota.

## <a name="other-batch-account-management-options"></a>Outras opções de gerenciamento de conta do Lote
Além de usar o portal do Azure, você também pode criar e gerenciar contas do Lote com o seguinte:

* [Cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](batch-cli-get-started.md)
* [.NET de Gerenciamento do Lote](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximas etapas
* Veja a [visão geral dos recursos do Lote](batch-api-basics.md) para saber mais sobre os conceitos e os recursos do serviço do Lote. O artigo aborda os recursos principais do Lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço que permitem a execução da carga de trabalho de computação em larga escala.
* Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](batch-dotnet-get-started.md) ou do [Python](batch-python-tutorial.md). O artigo introdutório orienta você por meio de um aplicativo de trabalho que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação e que inclui o uso do Armazenamento do Azure para preparação e recuperação de um arquivo de carga de trabalho.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regeneração de chaves da conta de armazenamento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png
