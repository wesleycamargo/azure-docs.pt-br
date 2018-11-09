---
title: Criar e gerenciar espaços de trabalho de serviço do Azure Machine Learning
description: Saiba como criar, exibir e excluir espaços de trabalho do Azure Machine Learning no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 2d62e2688c6c17cc96798cb1a560808f6da9c6a9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242697"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Criar e gerenciar espaços de trabalho de serviço do Azure Machine Learning

Neste artigo, você vai criar, exibir e excluir [**Espaços de trabalho do Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace) no portal do Azure para o [serviço do Azure Machine Learning](overview-what-is-azure-ml.md).  Também é possível criar e excluir workspaces [usando a CLI](reference-azure-machine-learning-cli.md) ou [com o código Python](https://aka.ms/aml-sdk).

Para criar um workspace, você precisa de uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-workspace"></a>Criar um workspace 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Exibir um workspace

1. No canto superior esquerdo do portal, selecione **Todos os serviços**. 

1. No campo de filtro em **Todos os serviços**, digite **Espaço de trabalho do Machine Learning**.  

   ![pesquisar espaço de trabalho de serviço do Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. Nos resultados do filtro, selecione **Espaço de trabalho do Machine Learning** para exibir uma lista dos espaços de trabalho. 

   ![pesquisar espaço de trabalho de serviço do Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Examine a lista de workspaces encontrada. É possível filtrar com base na assinatura, grupos de recursos e locais.  

   ![Lista de espaços de trabalho de serviço Azure Machine Learning](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Selecione o workspace que você acabou de criar para exibir suas propriedades.

   ![png](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Excluir um workspace

Use o botão Excluir na parte superior do workspace que você deseja excluir.

  ![png](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Limpar recursos 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Siga o tutorial completo para aprender a usar um workspace para criar, treinar e implantar modelos com o serviço do Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
