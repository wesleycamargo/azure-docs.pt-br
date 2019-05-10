---
title: 'Início Rápido: Executar um notebook na nuvem'
titleSuffix: Azure Machine Learning service
description: Introdução ao Serviço do Azure Machine Learning. Use um servidor de notebook gerenciado na nuvem para experimentar seu workspace.  Seu workspace é o bloco fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: ecb97860e70141a744833aa9b9a4fcea3f3688f0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149836"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Início Rápido: Usar um servidor do notebook baseado em nuvem para começar a usar o Azure Machine Learning

Crie um servidor de notebook baseado em nuvem e use-o.  Neste início rápido, você executará o código Python que registra valores no [workspace do Serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md). O workspace é o bloco fundamental na nuvem usado para experimentar, treinar e implantar modelos de machine learning com o Machine Learning. 

Este início rápido mostra como criar um recurso de nuvem no workspace do Azure Machine Learning, configurado com o ambiente do Python necessário para executar o Azure Machine Learning. Em vez disso, para usar seu próprio ambiente, consulte [Início Rápido: Usar seu próprio servidor do notebook para começar a usar o Azure Machine Learning](quickstart-run-local-notebook.md).  
 
Neste início rápido, você realiza as seguintes ações:

* Criar um novo servidor de notebook baseado em nuvem em seu workspace.
* Iniciar a interface da Web do Jupyter.
* Abra um notebook que contém código para estimar o pi e registrar erros em cada iteração.
* Execute o notebook.
* Exiba os valores de erro registrados em log no workspace. Este exemplo mostra como o espaço de trabalho pode ajudar a manter o controle das informações geradas em um script. 

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning.  [Crie seu workspace](setup-create-workspace.md#portal) agora se você não tiver um.

## <a name="create-a-cloud-based-notebook-server"></a>Criar um servidor de notebook baseado em nuvem

 Em seu workspace, você cria um recurso de nuvem para começar a usar notebooks Jupyter. Esse recurso oferece uma plataforma baseada em nuvem pré-configurada com tudo de que você precisa para executar o serviço do Azure Machine Learning.

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).  Se você não souber como localizar seu workspace no portal, veja como [localizar seu workspace](how-to-manage-workspace.md#view).

1. Na página do seu workspace no portal do Azure, selecione **VMs de Notebook** à esquerda.

1. Selecione **+Novo** para criar uma VM de notebook.

     ![Selecione Nova VM](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Forneça um nome para sua VM. Em seguida, selecione **Criar**.

    > [!NOTE]
    > O nome da VM do seu Notebook deve ter entre 2 e 16 caracteres. Os caracteres válidos são letras, dígitos e o caractere -.  O nome também deve ser exclusivo em toda a sua assinatura do Azure.

    ![Criar uma nova VM](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Aguarde aproximadamente de 4 a 5 minutos, até que o status mude para **Executando**.


## <a name="launch-jupyter-web-interface"></a>Iniciar interface da Web do Jupyter

Depois que sua VM estiver em execução, use a seção **VMs de Notebook** para iniciar a interface da Web do Jupyter.

1. Selecione **Jupyter** na coluna **URI** para sua VM.  

    ![Iniciar o servidor do Jupyter notebook](./media/quickstart-run-cloud-notebook/start-server.png)

    O link inicia seu servidor de notebook e abre a página da Web do Jupyter notebook em uma nova guia do navegador.  O link só funcionará para a pessoa que cria a VM.

1. Na página da Web do Jupyter notebook, o nome da pasta superior é o seu nome de usuário.  Selecione esta pasta.

1. O nome da pasta de amostras inclui um número de versão, por exemplo, **amostras-1.0.33.1**.  Selecione a pasta de amostras.

1. Selecione o notebook de **início rápido**.

## <a name="run-the-notebook"></a>Executar o notebook

Execute um notebook que estima o pi e registra o erro em seu workspace.

1. Selecione **01.run experiment.ipynb** para abrir o notebook.

1. Clique na primeira célula de código e selecione **Executar**.

    > [!NOTE]
    > As células de código têm colchetes antes delas. Se os colchetes estiverem vazios (__[]__), o código não terá sido executado. Embora o código esteja em execução, você verá um asterisco (__[*]__). Após a conclusão do código, um número **[1]** será exibido.  O número indica a ordem na qual as células foram executadas.
    >
    > Use **Shift-Enter** como um atalho para executar uma célula.

    ![Execute a primeira célula de código](media/quickstart-run-cloud-notebook/cell1.png)

1. Execute a segunda célula de código. Se você vir instruções para se autenticar, copie o código e siga o link para entrar. Após entrar, seu navegador se lembrará dessa configuração.  

    ![Autenticar](media/quickstart-run-cloud-notebook/authenticate.png)

1. Ao concluir, o número de célula __[2]__ será exibido.  Se você precisasse entrar, veria uma mensagem de autenticação bem-sucedida.   Se você não precisasse entrar, não veria nenhuma saída para essa célula, apenas o número seria exibido para mostrar que a célula foi executada com êxito.

    ![Mensagem de êxito](media/quickstart-run-cloud-notebook/success.png)

1. Execute o restante das células de código.  À medida que cada célula conclui a execução, você verá seu número de célula sendo exibido. Somente a última célula exibe qualquer outra saída.  

    Na maior célula de código, você vê `run.log` usado em vários lugares. Cada `run.log` adiciona seu valor ao seu workspace.


## <a name="view-logged-values"></a>Exibir valores registrados em log

1. A saída da célula `run` contém um link para o portal do Azure para exibir os resultados do experimento em seu workspace. 

    ![Exibir experimentos](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Clique no **Link para o portal do Azure** para exibir informações sobre a execução em seu workspace.  Esse link abre seu workspace no portal do Azure.

1. Os gráficos dos valores registrados que você vê foram criados automaticamente no workspace. Sempre que registra diversos valores com o mesmo parâmetro de nome, um gráfico é gerado automaticamente para você.

   ![Exibir histórico](./media/quickstart-run-cloud-notebook/web-results.png)

Já que o código para aproximar o pi usa valores aleatórios, seus gráficos mostrarão valores diferentes.  

## <a name="clean-up-resources"></a>Limpar recursos 

### <a name="stop-the-notebook-vm"></a>Interromper a VM de notebook

Interrompa a VM de notebook quando você não a estiver usando para reduzir o custo.  

1. Em seu workspace, selecione **VMs de Notebook**.

   ![Interromper o servidor da VM](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Selecione a VM na lista.

1. Selecione **Interromper**.

1. Quando estiver pronto para usar o servidor novamente, selecione **Iniciar**.

### <a name="delete-everything"></a>Excluir tudo

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você concluiu estas tarefas:

* Criar uma VM de notebook.
* Iniciar a interface da Web do Jupyter.
* Abra um notebook que contém código para estimar o pi e registrar erros em cada iteração.
* Execute o notebook.
* Exiba os valores de erro registrados em log no workspace.  Este exemplo mostra como o espaço de trabalho pode ajudar a manter o controle das informações geradas em um script. 

Na página da Web do Jupyter Notebook, procure entre os outros notebooks da pasta de amostras para saber mais sobre o Serviço do Azure Machine Learning.

Para obter uma experiência de fluxo de trabalho detalhado, siga os tutoriais do Machine Learning para treinar e implantar um modelo:  

> [!div class="nextstepaction"]
> [Tutorial: treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)
