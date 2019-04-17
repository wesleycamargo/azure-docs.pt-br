---
title: Início rápido executar um notebook na nuvem
titleSuffix: Azure Machine Learning service
description: Introdução ao Serviço do Azure Machine Learning. Use um servidor de notebook gerenciado na nuvem para experimentar seu workspace.  Seu workspace é o bloco fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 0672d90a25bc4c879d28512ab212f98f29efbf3b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358209"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Início Rápido: Usar um servidor do notebook baseado em nuvem para começar a usar o Azure Machine Learning

Neste artigo, você usará o Azure Notebooks para executar código registrado no [workspace do serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md). Seu workspace é o bloco fundamental na nuvem usado para experimentar, treinar e implantar modelos de machine learning com o Machine Learning. 

Este início rápido usa recursos de nuvem e não exige nenhuma instalação. Em vez disso, para usar seu próprio ambiente, consulte [Início Rápido: Usar seu próprio servidor do notebook para começar a usar o Azure Machine Learning](quickstart-run-local-notebook.md).  
 
Neste início rápido, você realiza as seguintes ações:

* Conecte-se ao workspace com o Python em um Jupyter Notebook. O notebook contém um código para estimar o pi e erros de logs em cada iteração. 
* Exiba os valores de erro registrados em log no workspace.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="prerequisite"></a>Pré-requisito

1. [Crie um workspace do Azure Machine Learning](setup-create-workspace.md#portal) se não tiver um.

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).  Confira como [encontrar seu workspace](how-to-manage-workspace.md#view).

## <a name="use-your-workspace"></a>Usar seu workspace

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Saiba como um workspace ajuda a gerenciar os scripts de machine learning. Nesta seção, você executa as seguintes etapas:

* Abra um notebook no Azure Notebooks.
* Execute o código que cria alguns valores registrados em log.
* Exiba os valores registrados em log no seu espaço de trabalho.

Este exemplo mostra como o espaço de trabalho pode ajudar a manter o controle das informações geradas em um script. 

### <a name="open-a-notebook"></a>Abra um notebook 

O [Azure Notebooks](https://notebooks.azure.com) fornece uma plataforma de nuvem gratuita para Jupyter Notebooks que está previamente configurada com tudo o que você precisa para executar o Machine Learning. No workspace, é possível iniciar essa plataforma para começar a usar o workspace do serviço do Azure Machine Learning.

1. Na página Visão geral do workspace, selecione **Introdução ao Azure Notebooks** para testar seu primeiro experimento no Azure Notebooks.  O Azure Notebooks é um serviço separado que permite executar notebooks Jupyter gratuitamente na nuvem.  Quando você usa esse link para o serviço, informações sobre como se conectar ao seu workspace serão adicionadas à biblioteca criada no Azure Notebooks.

   ![Explorar o workspace](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Entre no Azure Notebooks.  Lembre-se de entrar no Azure Notebooks com a mesma conta usada para entrar no portal do Azure. Sua organização poderá exigir [consentimento do administrador](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) antes de poder entrar.

1. Depois de entrar, uma nova guia é aberta e um prompt `Clone Library` é exibido. A clonagem dessa biblioteca carregará um conjunto de notebooks e outros arquivos na sua conta do Azure Notebooks.  Esses arquivos ajudarão você a explorar as capacidades do Azure Machine Learning.

1. Desmarque a opção **Público** para não compartilhar suas informações de workspace com outras pessoas.

1. Selecione **Clonar**.

   ![Clonar uma biblioteca](./media/quickstart-run-cloud-notebook/clone.png)

1. Se vir que o status do projeto está parado, clique em **Executar em computação gratuita** para usar o servidor de notebook gratuito.

    ![Executar um projeto em computação gratuita](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>Executar o notebook

Na lista de arquivos para este projeto, você vê um arquivo `config.json`. Este arquivo de configuração contém informações sobre o workspace que você criou no portal do Azure.  Esse arquivo permite que seu código se conecte e adicione informações ao seu workspace.

1. Selecione **01.run experiment.ipynb** para abrir o notebook.

1. A área de status informará para aguardar até o kernel ser iniciado.  A mensagem desaparecerá depois que o kernel estiver pronto.

    ![Aguardar até o kernel iniciar](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Depois que o kernel tiver iniciado, execute as células uma de cada vez usando **Shift+Enter**. Ou selecione **Células** > **Executar todas** para executar o notebook inteiro. Quando você vir um asterisco, __*__, ao lado de uma célula, significa que ela ainda está em execução. Após o código para a célula ser concluído, um número é exibido. 

1. Siga as instruções no notebook para autenticar sua assinatura do Azure.

Depois de concluir a execução de todas as células no bloco de anotações, você poderá exibir os valores registrados em seu workspace.

## <a name="view-logged-values"></a>Exibir valores registrados em log

1. A saída da célula `run` contém um link para o portal do Azure para exibir os resultados do experimento em seu workspace. 

    ![Exibir experimentos](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Clique no **Link para o portal do Azure** para exibir informações sobre a execução em seu workspace.  Esse link abre seu workspace no portal do Azure.

1. Os gráficos dos valores registrados que você vê foram criados automaticamente no workspace. Sempre que registra diversos valores com o mesmo parâmetro de nome, um gráfico é gerado automaticamente para você.

   ![Exibir histórico](./media/quickstart-run-cloud-notebook/web-results.png)

Já que o código para aproximar o pi usa valores aleatórios, seus gráficos mostrarão valores diferentes.  

## <a name="clean-up-resources"></a>Limpar recursos 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Você criou os recursos necessários para experimentar e implantar modelos. Você também executou código em um notebook. E explorou o histórico de execução desse código em seu workspace na nuvem.

Para obter uma experiência de fluxo de trabalho detalhado, siga os tutoriais do Machine Learning para treinar e implantar um modelo:  

> [!div class="nextstepaction"]
> [Tutorial: Treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)