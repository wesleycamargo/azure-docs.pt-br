---
title: Configurar contas do Azure para o Projeto Acústica
titlesuffix: Azure Cognitive Services
description: Siga este guia para configurar contas de armazenamento e lote do Azure necessárias para trabalhar com acústica.
services: cognitive-services
author: ashtat
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 05f7c435ca61930da6270c54e71c784f1b7aebcc
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900604"
---
# <a name="create-an-azure-batch-account"></a>Criar uma Conta de Lote do Azure
Siga este guia para configurar contas de armazenamento e lote do Azure necessárias para trabalhar com acústica. Para obter informações sobre a plug-in do Unity desenvolvido como parte do projeto acústica, consulte [What ' s acústica](what-is-acoustics.md). Para obter informações sobre como incorporar acústica em seu projeto do Unity, consulte [Introdução ao](getting-started.md).  

## <a name="get-an-azure-subscription"></a>Obter uma assinatura do Azure
Uma [assinatura do Azure](https://azure.microsoft.com/free/) é necessária antes de configurar contas do lote e armazenamento. Se você estiver se inscrevendo pela primeira vez, o Azure fornece alguns recursos gratuitos de tempo limitado e o crédito de US $200.

## <a name="create-azure-batch-and-storage-accounts"></a>Criar contas de lote e armazenamento do Azure
Em seguida, siga [estas instruções](https://docs.microsoft.com/azure/batch/batch-account-create-portal) para configurar o seu Azure Batch e as contas do Armazenamento do Azure associadas.

Escolha as opções padrão para as contas de lote e armazenamento:
  
  ![Nova conta em lote](media/NewBatchAccountCreate.png)

  ![Nova conta de armazenamento](media/BatchStorageAccountCreate.png)

Demora alguns minutos para o Azure implantar as contas. Procure uma notificação de conclusão no canto superior direito do portal.
  
  ![Contas implantadas](media/BatchAccountsDeployNotification.png)

Suas contas agora devem estar visíveis no seu painel.
  
  ![Painel do Portal](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurar a acústica da interface do usuário para assar com credenciais do Azure
Clique no link da conta do Lote no painel e, em seguida, clique no link **Chaves** na página da conta do Lote para acessar suas credenciais.
  
  ![Link de chaves do lote](media/BatchAccessKeys.png)

  ![Credenciais de conta do lote](media/BatchKeysInfo.png)

Clique no link **Conta de armazenamento** na página para acessar suas credenciais da conta do Armazenamento do Azure.
  
  ![Credenciais da conta de armazenamento](media/StorageKeysInfo.png)

Insira essas credenciais na guia Bake, conforme descrito na explicação passo a passo da [bake](bake-ui-walkthrough.md).

## <a name="node-types-and-region-support"></a>Tipos de nó e o suporte de região
A Acústica do Projeto requer os nós da VM do Azure otimizados para computação das séries F e H, que podem não ser suportados em todas as regiões do Azure. Por favor, verifique [esta tabela](https://azure.microsoft.com/global-infrastructure/services) para garantir que você está escolhendo o local certo para sua conta do Lote. Esta série H de momento em que as máquinas virtuais têm suporte no Leste dos EUA, Centro Norte dos EUA, Centro-Sul dos EUA, oeste dos EUA, oeste dos EUA 2, Europa Setentrional, Europa Ocidental e Oeste do Japão.

## <a name="upgrading-your-quota"></a>Atualizando sua cota
As contas do Azure Batch são aprovisionadas na criação de contas com um limite de 20 núcleos de computação. Talvez você queira aumentar esse limite para tempos de cozimento mais rápidos, porque você pode paralelizar sua carga de trabalho de acústica em vários nós, até o número de pontos de sonda em sua cena. Você pode solicitar um aumento de cota, clicando na **cota** link na sua página de portal de lote do Azure e, em seguida, clicando em **solicitação de aumento de cota**:

![Aumento de cota do Azure](media/azurequotas.png)

## <a name="next-steps"></a>Próximas etapas
* Comece a [integrar a acústica ao seu projeto Unity](getting-started.md)
* Explore o [cena de exemplo](sample-walkthrough.md)

