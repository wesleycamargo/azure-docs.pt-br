---
title: Pipeline de CI/CD com o Azure DevOps Projects – Azure IoT Edge| Microsoft Docs
description: O Azure DevOps Projects facilita o uso inicial do Azure. Ele ajuda você a iniciar um aplicativo do Azure IoT Edge de sua escolha em algumas etapas rápidas.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074207"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Criar um pipeline de CI/CD para IoT Edge com o Azure DevOps Projects (versão prévia)

Configure a integração contínua (CI) e a entrega contínua (CD) para seu aplicativo IoT Edge com os Azure DevOps Projects. O DevOps Projects do Azure simplifica a configuração inicial de um pipeline de build e de lançamento no Azure Pipelines.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O DevOps Projects cria um pipeline de CI/CD no Azure DevOps. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione o ícone **Criar um recurso** na barra de navegação à esquerda, depois procure o **DevOps Projects**.  

1.  Selecione **Criar**.

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Seus módulos do Azure IoT Edge podem ser escritos em [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selecione seu idioma preferido para iniciar um novo aplicativo. Do mesmo modo, você pode selecionar **.NET**, **Node.js**, **Python**, **C**, pi **Java**, e, em seguida, clicar em **Próximo**.

    ![Selecione o idioma para criar um novo aplicativo](./media/how-to-devops-project/select-language.png)

2. Selecione **IoT simples (versão prévia)** e, em seguida, clique em **Próximo**.

    ![Selecione a estrutura de IoT simples](media/how-to-devops-project/select-iot.png)

3. Selecione **IoT Edge**e, em seguida, clique em **Próximo**.

    ![Selecione o serviço IoT Edge](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Crie uma nova organização do Azure DevOps gratuita ou escolha uma existente.

     a. Escolha um nome para o projeto. 

    b. Selecione a assinatura e o local do Azure, escolha um nome para o aplicativo e selecione **Concluído**.  

    ![Nomear e criar aplicativo](media/how-to-devops-project/select-devops.png)

1. Após alguns minutos, o painel do DevOps Projects é exibido no portal do Azure. Um aplicativo de IoT Edge é configurado em um repositório em sua organização do Azure DevOps, é executado um build, e seu aplicativo é implantado no IoT Edge. Esse painel oferece visibilidade ao seu repositório de código, pipeline de CI/CD e seu aplicativo no Azure.

    ![Aplicativo de exibição no portal do DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

O DevOps Projects criou um repositório Git no Azure Repos ou GitHub. Para exibir o repositório e fazer alterações de código no seu aplicativo, siga as etapas a seguir:

1. À esquerda do painel do DevOps Projects, selecione o link para seu branch **mestre**.  
Esse link abre uma exibição do repositório de Git recém-criado.

1. Para exibir a URL de clone do repositório, selecione **Clone** na parte superior direita do navegador. Você pode clonar seu repositório Git no VS Code ou outras ferramentas preferenciais. Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente no branch mestre.

    ![Clonar repositório do Git](media/how-to-devops-project/clone.png)

1. À esquerda do navegador, vá para o arquivo **modules/FilterModule/module.json**.

1. Selecione **Editar**, depois faça uma alteração no `"version"` em `"tag"`. Por exemplo, você pode atualizá-lo para `"version": "${BUILD_BUILDID}"` usar [variáveis de compilação do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) como parte de sua marca de imagem de módulo do Azure IoT Edge.

    ![Editar versão para aceitar as variáveis de compilação](media/how-to-devops-project/update-module-json.png)

1. Selecione **Confirmar**, depois salve as alterações.

1. No seu navegador, vá até o painel do Azure DevOps Project.  Agora você deve ver uma compilação que está em andamento. As alterações feitas são automaticamente compiladas e implantadas via pipeline de CI/CD.

    ![Exibir o status em andamento](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Examinar o pipeline de CI/CD

Na etapa anterior, o Azure DevOps Projects configurou automaticamente um pipeline de CI/CD para o seu aplicativo IoT Edge. Explore e personalize o pipeline conforme necessário. Siga as etapas a seguir para se familiarizar com os pipelines de build e de lançamento do Azure DevOps.

1. Na parte superior do painel do DevOps Projects, selecione **Pipelines de Build**.  
Esse link abre uma guia de navegador e o pipeline de build do Azure DevOps para seu novo projeto.

1. Selecione **Editar**.

    ![Editar o pipeline de build](media/how-to-devops-project/click-edit-button.png)

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, compilando as imagens do módulo IoT Edge, enviando por push os módulo do IoT Edge e publicando as saídas que são usadas para implantações. Para saber mais sobre as tarefas do Azure IoT Edge para CI, você pode visitar [Configurar Pipelines do Azure para integração contínua](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration).

    ![Visualizar tarefas de integração contínua](media/how-to-devops-project/ci.png)

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**.   
No painel **Histórico**, é exibida uma trilha de auditoria das alterações recentes do build.  O Azure Pipelines controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**. O DevOps Projects criou automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Liberar** em **Pipelines**. O Azure DevOps Projects cria um pipeline de lançamento para gerenciar implantações no Azure IoT Edge.

    ![Visualizar pipeline de lançamento](media/how-to-devops-project/release-pipeline.png)

1. Selecione **Editar**. O pipeline de lançamento contém um pipeline, o qual define o processo de lançamento.  

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone **Soltar**, selecione o **Gatilho de implantação contínua**.  
Esse pipeline de lançamento tem um gatilho de CD habilitado, o qual executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual.  

1. À esquerda, selecione **Tarefas**. As tarefas são as atividades realizadas pelo seu processo de implantação. Neste exemplo, uma tarefa foi criada para implantar suas imagens de módulo ao Azure IoT Edge. Para saber mais sobre as tarefas do Azure IoT Edge para CI, você pode visitar [Configurar Pipelines do Azure para implantação contínua](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment).

    ![Visualizar tarefas de implantação contínua](media/how-to-devops-project/dev-release.png)

1. À direita, selecione **Exibir versões**. Essa exibição mostra um histórico de versões.

1. Selecione as reticências (...) ao lado de uma das versões, depois selecione **Abrir**.  
Há vários menus para explorar, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas à implantação específica. 

1. Selecione **Logs**. Os logs contêm informações úteis sobre o processo de implantação. Eles podem ser exibidos durante e após as implantações.


## <a name="clean-up-resources"></a>Limpar recursos

É possível excluir o Serviço de Aplicativo do Azure e outros recursos criados quando eles não forem mais precisos. Use a funcionalidade **Excluir** no painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as tarefas para o Azure IoT Edge em DevOps do Azure na [Integração contínua e implantação contínua no Azure IoT Edge](how-to-ci-cd.md)
* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
