---
title: Criar um workspace do Machine Learning Studio | Microsoft Docs
description: Como criar um workspace para o Machine Learning Studio do Microsoft Azure
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 19cbb27221bfb95a0900eb7a604e540cb066037e
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345548"
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Criar e compartilhar um workspace de Azure Machine Learning

Para usar o Azure Machine Learning Studio é necessário ter um espaço de trabalho do Machine Learning Studio. Esse workspace contém as ferramentas necessárias para criar, gerenciar e publicar testes.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Para criar um workspace
1. Entre no [Portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para entrar e criar um workspace, você precisa ser um administrador de assinatura do Azure. 
    >
    > 

2. Clique em **+Novo**

3. Na caixa de pesquisa, digite **Workspace do Microsoft Azure Machine Learning Studio** e selecione o item correspondente. Em seguida, selecione **Criar** na parte inferior da página.

4. Insira suas informações de workspace:

    - O *nome do workspace* pode ter até 260 caracteres, sem terminar com um espaço. O nome não pode incluir estes caracteres:`< > * % & : \ ? + /`
    - O *plano do serviço Web* escolhido (ou criado), juntamente com o *tipo de preço* associado, será usado se você implantar os serviços Web deste workspace.

    ![Criar um novo workspace](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

Após a implantação do workspace, abra-o no Machine Learning Studio.

1. Navegue até Machine Learning Studio em [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecione seu workspace no canto superior direito.

    ![Selecione o workspace](./media/create-workspace/open-workspace.png)

3. Clique em **meus experimentos**.

    ![Experimentos abertos](./media/create-workspace/my-experiments.png)

Para saber mais sobre como gerenciar seu workspace, consulte [Gerenciar um workspace do Azure Machine Learning](manage-workspace.md).
Se você encontrar um problema ao criar seu workspace, consulte [Guia de solução de problemas: criar e conectar-se a um Workspace do Machine Learning](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Compartilhar um workspace do Azure Machine Learning
Depois de criar um Workspace do Machine Learning, você pode convidar usuários ao seu workspace e compartilhar acesso ao seu workspace, conjuntos de dados, notebooks etc. Você pode adicionar usuários em uma das duas funções:

* **Usuário**: um usuário do workspace pode criar, abrir, modificar e excluir conjuntos de dados, experimentos etc. no workspace.
* **Proprietário**: um proprietário pode convidar e remover usuários no workspace, além do que o usuário pode fazer.

> [!NOTE]
> A conta de administrador que cria o workspace é adicionado automaticamente ao workspace como proprietário. No entanto, outros administradores ou usuários nessa assinatura não recebem automaticamente acesso ao workspace - é preciso convidá-los explicitamente.
> 
> 

### <a name="to-share-a-workspace"></a>Para compartilhar um workspace

1. Entre no Machine Learning Studio em [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **CONFIGURAÇÕES**

3. Clique na guia **USUÁRIOS**

4. Clique em **CONVIDAR MAIS USUÁRIOS** na parte inferior da página

    ![Configurações do Studio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de email. O usuário precisa de uma conta da Microsoft válida ou de uma conta organizacional (do Azure Active Directory).

6. Selecione se deseja adicionar os usuários como Proprietário ou Usuário.

7. Clique no botão de seleção **OK**.

Cada usuário que você adicionar receberá um email com instruções sobre como entrar no workspace compartilhado.

> [!NOTE]
> Para que os usuários possam implantar ou gerenciar os serviços Web neste workspace, eles devem ser colaboradores ou administradores na assinatura do Azure. 



