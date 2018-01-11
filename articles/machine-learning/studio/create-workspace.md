---
title: "Criar um espaço de trabalho do Machine Learning Studio | Microsoft Docs"
description: "Como criar um espaço de trabalho para o Machine Learning Studio do Microsoft Azure"
services: machine-learning
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.author: garye
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview
ms.openlocfilehash: 7aad885b1643ddeb25e5d00a151005b8444315fa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Criar e compartilhar um espaço de trabalho de Azure Machine Learning
Esse menu vincula-se aos tópicos que descrevem como configurar os vários ambientes de ciência de dados usados pelo CAPS (Processo Cortana Analytics).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Para usar o Azure Machine Learning Studio, você precisa ter um espaço de trabalho de do Machine Learning. Esse espaço de trabalho contém as ferramentas necessárias para criar, gerenciar e publicar testes.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Para criar um espaço de trabalho
1. Entre no [Portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para entrar e criar um espaço de trabalho, você precisa ser um administrador de assinatura do Azure. 
    >
    > 

2. Clique em **+Novo**

3. Na caixa de pesquisa, digite **Espaço de trabalho do Microsoft Azure Machine Learning Studio** e selecione o item correspondente. Em seguida, selecione **Criar** na parte inferior da página.

4. Insira suas informações de espaço de trabalho:

    - O *nome do espaço de trabalho* pode ter até 260 caracteres, sem terminar com um espaço. O nome não pode incluir estes caracteres:`< > * % & : \ ? + /`
    - O *plano do serviço Web* escolhido (ou criado), juntamente com o *tipo de preço* associado, será usado se você implantar os serviços Web deste espaço de trabalho.

    ![Criar um novo espaço de trabalho](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

Após a implantação do espaço de trabalho, abra-o no Machine Learning Studio.

1. Vá até o Machine Learning Studio em [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecione seu espaço de trabalho no canto superior direito.

    ![Selecione o espaço de trabalho](./media/create-workspace/open-workspace.png)

3. Clique em **meus experimentos**.

    ![Experimentos abertos](./media/create-workspace/my-experiments.png)

Para saber mais sobre como gerenciar seu espaço de trabalho, consulte [Gerenciar um espaço de trabalho do Azure Machine Learning](manage-workspace.md).
Se você encontrar um problema ao criar seu espaço de trabalho, consulte [Guia de solução de problemas: criar e conectar-se a um Espaço de Trabalho do Machine Learning](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Compartilhar um espaço de trabalho do Azure Machine Learning
Depois de criar um Espaço de Trabalho do Machine Learning, você pode convidar usuários ao seu espaço de trabalho e compartilhar acesso ao seu espaço de trabalho, conjuntos de dados, notebooks etc. Você pode adicionar usuários em uma das duas funções:

* **Usuário**: um usuário do espaço de trabalho pode criar, abrir, modificar e excluir conjuntos de dados, experimentos etc. no espaço de trabalho.
* **Proprietário**: um proprietário pode convidar e remover usuários no espaço de trabalho, além do que o usuário pode fazer.

> [!NOTE]
> A conta de administrador que cria o espaço de trabalho é adicionado automaticamente ao espaço de trabalho como proprietário. No entanto, outros administradores ou usuários nessa assinatura não recebem automaticamente acesso ao espaço de trabalho - é preciso convidá-los explicitamente.
> 
> 

### <a name="to-share-a-workspace"></a>Para compartilhar um espaço de trabalho

1. Entre no Machine Learning Studio em [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **CONFIGURAÇÕES**

3. Clique na guia **USUÁRIOS**

4. Clique em **CONVIDAR MAIS USUÁRIOS** na parte inferior da página

    ![Configurações do Studio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de email. O usuário precisa de uma conta da Microsoft válida ou de uma conta organizacional (do Azure Active Directory).

6. Selecione se deseja adicionar os usuários como Proprietário ou Usuário.

7. Clique no botão de seleção **OK**.

Cada usuário que você adicionar receberá um email com instruções sobre como entrar no espaço de trabalho compartilhado.

> [!NOTE]
> Para que os usuários possam implantar ou gerenciar os serviços Web neste espaço de trabalho, eles devem ser colaboradores ou administradores na assinatura do Azure. 



