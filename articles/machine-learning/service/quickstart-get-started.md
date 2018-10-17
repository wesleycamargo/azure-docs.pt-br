---
title: 'Início Rápido: criar um workspace do serviço do Machine Learning no portal do Azure – Azure Machine Learning'
description: Use o portal do Azure para criar um workspace do serviço do Azure Machine Learning. Este workspace é o bloco fundamental na nuvem para experimentação, treinamento e implantação de modelos de aprendizado de máquina com o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b6f0201a36a676e7647b9f5e60bc2df3415b9594
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831323"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning-service"></a>Início Rápido: usar o portal do Azure para começar a usar o serviço do Azure Machine Learning

Neste início rápido, você usará o portal do Azure para criar um workspace do serviço do Azure Machine Learning. Este workspace é o bloco fundamental na nuvem para experimentação, treinamento e implantação de modelos de aprendizado de máquina com o serviço de Azure Machine Learning. 

Neste tutorial, você irá:

* Criar um workspace na sua assinatura do Azure
* Experimente com o Python em um Azure Notebook e registre em log valores em várias iterações
* Exibir os valores registrados em log no seu workspace

Para sua conveniência, os seguintes recursos do Azure são adicionados automaticamente ao seu workspace quando regionalmente disponíveis: [Registro de Contêiner](https://azure.microsoft.com/services/container-registry/), [Armazenamento](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) e [Key Vault](https://azure.microsoft.com/services/key-vault/).

Os recursos que você cria podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do serviço do Azure Machine Learning. Como com outros serviços do Azure, há limites em determinados recursos (para por exemplo, tamanho do cluster BatchAI) associado ao serviço Azure Machine Learning. Leia [este](how-to-manage-quotas.md) artigo sobre os limites padrão e como solicite mais cota.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-a-workspace"></a>Criar um espaço de trabalho 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Na página do workspace, clique em `Explore your Azure Machine Learning service workspace`

 ![explorar workspace](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Usar o workspace

Agora veja como um workspace ajuda você a gerenciar seus scripts de aprendizado de máquina. Nesta seção, você:

* Abre um notebook no Azure Notebooks
* Executa código que cria alguns valores registrados em log
* Exibir os valores registrados em log no seu workspace

Este é um exemplo de como o workspace pode ajudar a manter o controle das informações geradas em um script. 

### <a name="open-a-notebook"></a>Abra um notebook 

O Azure Notebooks fornece uma plataforma de nuvem gratuita para Jupyter Notebooks, previamente configurados com tudo o que você precisa para executar o serviço Azure Machine Learning.  

Clique no botão `Open Azure Notebooks` para testar seu primeiro experimento.

 ![Inicializar o Azure Notebook](./media/quickstart-get-started/explore_ws.png)

Depois de entrar, uma nova guia é aberta e um prompt `Clone Library` é exibido.  Clique em `Clone`


### <a name="run-the-notebook"></a>Executar o notebook

Junto com dois notebooks, você verá um arquivo `config.json`.  Este arquivo de configuração contém informações sobre o workspace que você acabou de criar.  

Clique em `01.run-experiment.ipynb` para abrir o notebook.

Você pode executar as células uma por vez usando `Shift`+`Enter`.  Ou use o menu `Cells` > `Run All` para executar o notebook inteiro.  Quando você vê [*] ao lado de uma célula, significa que ela está em execução.  Quando o código para essa célula for concluído, um número aparecerá.

Talvez você receba uma solicitação para entrar.  Copie o código na mensagem, clique no link e cole o código na nova janela.  Não copie um espaço antes ou depois do código.  Entre com a mesma conta usada no portal do Azure.

 ![logon](./media/quickstart-get-started/login.png)

No notebook, a segunda célula lê do `config.json` para se conectar ao seu workspace.
```
ws = Workspace.from_config()
```

A terceira célula de código inicia um experimento com o nome de "meu-primeiro-experimento".  Você usará esse nome para pesquisar informações sobre a execução em seu workspace.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

Na última célula do notebook, observe os valores gravados em um arquivo de log.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Você poderá exibir esses valores no seu workspace depois que o código tiver sido executado.

## <a name="view-logged-values"></a>Exibir valores registrados em log

Depois de concluir todas as células no notebook, volte à página do portal.  

Clique em `View Experiments`.

![exibir experimentos](./media/quickstart-get-started/view_exp.png)

Feche o pop-up `Reports`.

Clique em `my-first-experiment`.

Veja informações sobre a execução que você acabou de executar.  Role para baixo a página para encontrar a tabela de execuções e clique no link do número de execução.

 ![link do histórico de execução](./media/quickstart-get-started/report.png)

Você verá gráficos criados automaticamente dos valores registrados:

   ![exibir histórico](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>Limpar recursos 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único workspace, exibindo as propriedades do workspace e selecionando o botão Excluir.

## <a name="next-steps"></a>Próximas etapas

Agora você criou os recursos necessários para começar a experimentar e a implantar modelos. Você também executou um código em um notebook e explorou o histórico de execuções desse o código em seu workspace na nuvem.

Para obter uma experiência de fluxo de trabalho detalhado, siga os tutoriais do Azure Machine Learning para treinar e implantar um modelo.  

> [!div class="nextstepaction"]
> [Tutorial: Treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)
