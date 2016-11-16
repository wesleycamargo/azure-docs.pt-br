---
title: Criar uma conta do Lote do Azure | Microsoft Docs
description: Aprenda a criar uma conta do Lote do Azure no portal do Azure para executar cargas de trabalho paralelas em larga escala na nuvem.
services: batch
documentationcenter: 
author: mmacy
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/21/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1a4e242c2d179d9aaf869d6f95af96ff754adf93


---
# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Criar uma conta do Lote do Azure usando o portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [.NET de Gerenciamento do Lote](batch-management-dotnet.md)
> 
> 

Saiba como criar uma conta do Lote do Azure no [Portal do Azure][azure_portal] e onde encontrar propriedades da conta importantes, como chaves de acesso e conta URLs. Também tratamos dos preços do Lote e da vinculação de uma conta do Armazenamento do Azure para sua conta do Lote para que você possa usar [pacotes de aplicativos](batch-application-packages.md) e [persistir a saída de trabalho e tarefa](batch-task-output.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch
1. Entre no [Portal do Azure][azure_portal].
2. Clique em **Novo** > **Computação** > **Serviço de Lote**.
   
    ![Lote no Marketplace][marketplace_portal]
3. A folha **Nova Conta do Lote** é exibida. Veja os itens *a* até *e* abaixo para obter descrições de cada elemento da folha.
   
    ![Criar uma conta do Batch][account_portal]
   
    a. **Nome da Conta**: um nome exclusivo para sua conta do Lote. Esse nome deve ser exclusivo na região do Azure em que a conta é criada (confira o *Local* abaixo). Ele pode conter somente caracteres minúsculos, números e deve ter de 3 a 24 caracteres de comprimento.
   
    b. **Assinatura**: uma assinatura na qual a conta do Lote será criada. Se você tiver somente uma assinatura, ela será selecionada por padrão.
   
    c. **Grupo de recursos**: um grupo de recursos para sua nova conta do Lote ou, opcionalmente, crie um novo.
   
    d. **Local**: uma região do Azure na qual a conta do Lote será criada. Somente as regiões com suporte da sua assinatura e do seu grupo de recursos são exibidas como opções.
   
    e. **Conta de Armazenamento** (opcional): uma conta de armazenamento de **Uso geral** que você associa à sua nova conta do Lote. Confira [Conta vinculada do Armazenamento do Azure](#linked-azure-storage-account) abaixo para obter mais detalhes.
4. Clique em **Criar** para criar a conta.
   
   O portal indica que ele está **Implantando** a conta e, após a conclusão, uma notificação **Implantações bem-sucedidas** aparecerá em *Notificações*.

## <a name="view-batch-account-properties"></a>Exibir propriedades de conta do Lote
Após a criação da conta, você poderá abrir a **folha Conta do Lote** para acessar suas propriedades e configurações. Você pode acessar todas as propriedades e configurações de conta usando o menu à esquerda da folha Conta de Lote.

![Folha Conta do Lote no portal do Azure][account_blade]

* **URL da conta do Lote**: os aplicativos que você criou com as [APIs de desenvolvimento do Lote](batch-technical-overview.md#batch-development-apis) precisam de uma URL de conta para gerenciar recursos e executar trabalhos na conta. Uma URL de conta do Lote tem o seguinte formato:
  
    `https://<account_name>.<region>.batch.azure.com`

![URL de conta do Lote no portal][account_url]

* **Chaves de acesso**: seus aplicativos também precisam de uma chave de acesso ao trabalhar com recursos em sua conta do Lote. Para exibir ou regenerar chaves de acesso da sua conta do Lote, insira `keys` na caixa **Pesquisar** do menu à esquerda na folha Conta do Lote e, em seguida, selecione **Chaves**.
  
    ![Chaves de conta do Lote no portal do Azure][account_keys]

## <a name="pricing"></a>Preços
As contas do Lote são oferecidas apenas em uma "Camada Gratuita", o que significa que não são cobradas pela própria conta do Lote. Você é cobrado pelos recursos de computação subjacentes do Azure consumidos por suas soluções do Lote e pelos recursos consumidos por outros serviços quando suas cargas de trabalho forem executadas. Por exemplo, você será cobrado pelos nós de computação em seus pools e pelos dados guardados no Armazenamento do Azure como entrada ou saída de suas tarefas. De forma semelhante, se você usar o recurso [pacotes de aplicativos](batch-application-packages.md) do Lote, será cobrado pelos recursos do Armazenamento do Azure usados para armazenar os pacotes de aplicativos. Veja [Preços do Lote][batch_pricing] para saber mais.

## <a name="linked-azure-storage-account"></a>Conta vinculada do Armazenamento do Azure
Como mencionado anteriormente, você pode (opcionalmente) vincular uma conta de armazenamento de **Finalidade geral** à sua nova conta do Lote. O recurso [pacotes de aplicativos](batch-application-packages.md) do Lote usa o armazenamento de blobs em uma conta do Armazenamento de Uso geral vinculada, como faz a biblioteca [.NET de Convenções de Arquivo do Lote](batch-task-output.md). Esses recursos opcionais ajudarão você a implantar os aplicativos executados por suas tarefas do Lote, persistindo os dados que eles produzem.

No momento, o Lote dá suporte *somente* ao tipo de conta de armazenamento de **Uso geral**, conforme descrito na etapa 5, [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account), em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Ao vincular uma conta do Armazenamento do Azure à sua conta do Lote, vincule *somente* uma conta de armazenamento de **Finalidade geral** .

![Criação de uma conta de armazenamento de “finalidade geral”][storage_account]

É recomendável que você crie uma conta do Armazenamento para uso exclusivo de sua conta do Lote.

> [!WARNING]
> Tome cuidado ao regenerar as chaves de acesso de uma conta de Armazenamento vinculada. Regenere somente uma chave de conta do Armazenamento e clique em **Sincronizar Chaves** na folha Conta do Armazenamento vinculada. Aguarde cinco minutos para permitir que as chaves sejam propagadas para os nós de computação em seus pools e regenere e sincronize a outra chave, se necessário. Se você regenerar as chaves ao mesmo tempo, os nós de computação não poderão sincronizar nenhuma delas e, assim, perderão o acesso à conta de armazenamento.
> 
> 

  ![Regeneração de chaves da conta de armazenamento][4]

## <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote
Esteja ciente de que como sua assinatura do Azure e outros serviços do Azure, determinados [limites e cotas](batch-quota-limit.md) se aplicam a contas do Lote. As cotas atuais em uma conta do Lote aparecem no portal nas **Propriedades**da conta.

![Cotas de conta do Lote no portal do Azure][quotas]

Lembre-se dessas cotas quando estiver projetando e dimensionando suas cargas de trabalho do Lote. Por exemplo, se seu pool não alcançar o número de destino de nós de computação especificado, talvez você tenha atingido o limite de cota de núcleos para sua conta do Lote.

Observe também que você não está restrito a uma única conta do Lote para sua assinatura do Azure. Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote na mesma assinatura mas em diferentes regiões do Azure.

Muitas dessas cotas podem ser aumentadas com uma solicitação de suporte do produto gratuito enviada no portal do Azure. Confira [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter detalhes sobre a solicitação de aumentos de cota.

## <a name="other-batch-account-management-options"></a>Outras opções de gerenciamento de conta do Lote
Além de usar o portal do Azure, você também pode criar e gerenciar contas do Lote com o seguinte:

* [Cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](../xplat-cli-install.md)
* [.NET de Gerenciamento do Lote](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximas etapas
* Veja a [visão geral dos recursos do Lote do Azure](batch-api-basics.md) para saber mais sobre os conceitos e os recursos do serviço do Lote. O artigo aborda os recursos principais do Lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço que permitem a execução da carga de trabalho de computação em larga escala.
* Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](batch-dotnet-get-started.md). O [artigo introdutório](batch-dotnet-get-started.md) orienta você por meio de um aplicativo de trabalho que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação e que inclui o uso do Armazenamento do Azure para preparação e recuperação de um arquivo de carga de trabalho.

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
[cotas]: ./media/batch-account-create-portal/quotas.png



<!--HONumber=Nov16_HO2-->


