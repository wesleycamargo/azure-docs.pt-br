---
title: "Gerenciar um espaço de trabalho do Machine Learning | Microsoft Docs"
description: "Gerencie o acesso aos espaços de trabalho de Azure Machine Learning e implante e gerencie serviços Web da API ML"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
ms.openlocfilehash: 2e4b2869b6eac9670853832d58bc37f1cb0ed001
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Gerenciar um espaço de trabalho do Azure Machine Learning

> [!NOTE]
> Para obter informações sobre como gerenciar serviços Web no portal de serviços Web do Machine Learning, veja [Gerenciar um serviço Web usando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md).
> 
> 

Você pode gerenciar os espaços de trabalho do Machine Learning no Portal do Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Para gerenciar um espaço de trabalho no portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com/) usando uma conta de administrador de assinatura do Azure.
2. Na caixa de pesquisa na parte superior da página, insira "espaços de trabalho do machine learning" e, em seguida, escolha **Espaço de Trabalho do Machine Learning**.
3. Clique no espaço de trabalho que você deseja gerenciar.

Além das informações de gerenciamento de recursos padrão e das opções disponíveis, você pode:

- Exibir **Propriedades** - essa página exibe as informações do espaço de trabalho e dos recursos e você pode alterar o assinatura e o grupo de recursos aos quais esse espaço de trabalho está conectado.
- **Ressincronizar as Chaves de Armazenamento** - o espaço de trabalho mantém chaves para a conta de armazenamento. Se a conta de armazenamento alterar as chaves, clique em **Ressincronizar chaves** para sincronizar as chaves com o espaço de trabalho.

Para gerenciar os serviços Web associados a esse espaço de trabalho, use o Portal de Serviços Web do Machine Learning. Consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md) para obter informações completas.

> [!NOTE]
> Para implantar ou gerenciar Novos serviços Web, você deverá ser atribuído a uma função de colaborador ou de administrador na assinatura na qual o serviço Web é implantado. Se você convidar outro usuário para um espaço de trabalho do Machine Learning, deverá atribuí-lo a uma função de colaborador ou de administrador na assinatura antes de implantar ou gerenciar os serviços Web. 
> 
>Para saber mais sobre como configurar permissões de acesso, consulte [Exibir atribuições de acesso para usuários e grupos no Portal do Azure](../../active-directory/role-based-access-control-manage-assignments.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [implantar o Machine Learning com modelos do Azure Resource Manager](deploy-with-resource-manager-template.md). 