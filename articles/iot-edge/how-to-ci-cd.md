---
title: Integração contínua e implantação contínua – Azure IoT Edge | Microsoft Docs
description: Configurar a integração contínua e a implantação contínua – Azure IoT Edge com Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c4f5d6888d581cb44702a8d76e1ebbb13845091
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582908"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implantação contínua no Azure IoT Edge

Você pode facilmente adotar o DevOps com seus aplicativos do Azure IoT Edge com as tarefas internas do Azure IoT Edge em Pipelines do Azure ou [plug-in do Azure IoT Edge para Jenkins](https://plugins.jenkins.io/azure-iot-edge) no seu servidor Jenkins. Este artigo demonstra como você pode usar a integração contínua e recursos de implantação contínua de Azure Pipelines para criar, testar e implantar aplicativos de forma rápida e eficiente para o Azure IoT Edge. 

Neste artigo, você aprenderá a:
* Criar e fazer check-in de uma solução do IoT Edge de exemplo.
* Configure a CI (integração contínua) para criar a solução.
* Configure a CD (implantação contínua) para implantar a solução e exibir as respostas.

![Diagrama – ramificações de CI e CD para desenvolvimento e produção](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Criar um exemplo de solução do Azure IoT Edge usando o Visual Studio Code

Nesta seção, você criará uma solução de exemplo do IoT Edge contendo testes de unidade que podem ser executados como parte do processo de build. Antes de seguir as diretrizes nesta seção, conclua as etapas em [Desenvolver uma solução do IoT Edge com vários módulos no Visual Studio Code](how-to-develop-multiple-modules-vscode.md).

1. Na paleta de comandos do VS Code, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Em seguida, selecione a pasta do workspace, forneça um nome para a solução (o nome padrão é **EdgeSolution**) e crie um módulo C# (**FilterModule**) como o primeiro módulo de usuário nesta solução. Você também precisa especificar o repositório de imagens do Docker para o seu primeiro módulo. O repositório de imagens padrão baseia-se em um registro de Docker local (`localhost:5000/filtermodule`). Altere-o para o Registro de Contêiner do Azure (`<your container registry address>/filtermodule`) ou o Docker Hub para obter mais integração contínua.

    ![Configurar o Registro de Contêiner do Azure](./media/how-to-ci-cd/acr.png)

2. A janela de código VS carregará seu workspace da solução do Azure IoT Edge. Você pode, opcionalmente, digitar e executar **Azure IoT Edge: adicionar módulo do IoT Edge** para adicionar mais módulos. Há uma pasta `modules`, uma pasta `.vscode` e um arquivo de modelo de manifesto de implantação na pasta raiz. Todos os códigos de usuário do módulo serão subpastas na pasta `modules`. O `deployment.template.json` é o modelo de manifesto de implantação. Alguns dos parâmetros nesse arquivo serão analisados a partir de `module.json`, que existe em cada pasta de módulo.

3. Agora, sua solução do IoT Edge está pronta. O módulo C# padrão atua como um módulo de mensagem do pipe. No `deployment.template.json`, você verá que esta solução contém dois módulos. A mensagem será gerada pelo módulo `tempSensor`, será enviada diretamente pelo pipe por meio de `FilterModule` e, em seguida, será enviada para o Hub IoT.

4. Salve esses projetos e confirme em seus Azure Repos repositórios.
    
> [!NOTE]
> Para obter mais informações de como usar o Azure Repos, confira [Compartilhar seu código com o GIT do Visual Studio e do Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipelines-for-continuous-integration"></a>Configurar o Azure Pipelines para integração contínua
Nesta seção, você criará um pipeline de build configurado para ser executado automaticamente quando você fizer check-in das alterações na solução do IoT Edge de exemplo e mostrará os log de build no Azure Pipelines.

1. Entre em sua organização do Azure DevOps (**https://dev.azure.com/{your organização}/**) e abra o projeto no qual você fez check-in do aplicativo de exemplo.

    ![Realizar check-in de código para Azure Pipelines](./media/how-to-ci-cd/init-project.png)

1. Em seus Azure Pipelines, abra a guia **Compilações**, escolha **+ Novo pipeline**. Ou, se você já tiver pipelines de build, escolha o botão **+ Novo**. Em seguida, selecione **Novo pipeline de build**.

    ![Criar um novo pipeline de build](./media/how-to-ci-cd/add-new-build.png)

1. Se solicitado, selecione o Azure Repos para sua fonte. Em seguida, selecione o projeto, o repositório e o branch no qual o código está localizado. Escolha **Continuar**.

    ![Selecionar Git do Azure Repos](./media/how-to-ci-cd/select-vsts-git.png)

    Na janela **Selecionar um modelo**, escolha **começar com um Processo vazio**.

    ![Comece com um processo vazio](./media/how-to-ci-cd/start-with-empty.png)

1. No editor de pipeline, escolha o pool de agentes. 
    
    * Se você quiser compilar os módulos na plataforma amd64 para contêineres do Linux, escolha **Ubuntu 1604 hospedado**
    * Se você quiser compilar os módulos na plataforma amd64 para contêineres do Windows, escolha **VS2017 hospedado** 
    * Se você quiser compilar os módulos na plataforma arm32v7 para contêineres do Linux, precisará configurar seu próprio agente de build clicando no botão **Gerenciar**.
    
    ![Configurar o pool de agentes de build](./media/how-to-ci-cd/configure-env.png)

1. No Trabalho de agente, clique em "+" para adicionar duas tarefas no pipeline de build. O primeiro deles é de **Azure IoT Edge**. E a terceira é do **Publish Build Artifacts**
    
    ![Adicionar tarefas ao pipeline de build](./media/how-to-ci-cd/add-tasks.png)

1. Na primeira tarefa do **Azure IoT Edge**, atualize o **Nome de exibição** para **Azure IoT Edge - imagens de módulo de compilação**e, na lista suspensa **Ação**, selecione **Compilar imagens de módulo**. No controle **arquivo .template.json**, selecione o **deployment.template.json**, que descreve a solução IoT Edge. Em seguida, escolha **Plataforma padrão**, verifique se você selecionar a mesma plataforma como o dispositivo IoT Edge. Essa tarefa irá compilar todos os módulos na solução com a plataforma de destino especificado. E também gerará um arquivo **deployment.json**, você pode encontrar o caminho do arquivo em Variáveis de Saída. Defina o alias como `edge` para essa variável.
    
    ![Configurar a tarefa de criação de imagens de módulo](./media/how-to-ci-cd/build-and-push.png)

1. Na segunda tarefa do **Azure IoT Edge**, atualize o **Nome de exibição** para **Azure IoT Edge - imagens de módulo de push**e, na lista suspensa **Ação**, selecione **Compilar imagens de módulo**. Em seguida, escolha Tipo de Registro de Contêiner, configure e selecione o mesmo registro em seu código (module.json). No controle **arquivo .template.json**, selecione o **deployment.template.json**, que descreve a solução IoT Edge. Em seguida, escolha **Plataforma padrão**, verifique se você seleciona a mesma plataforma para as suas imagens do módulo de compilação. Essa tarefa enviará por push a todas as imagens de módulo para o registro de contêiner que você selecionou. E também credenciais de registro de contêiner no arquivo **deployment.json**, você pode encontrar o caminho do arquivo em Variáveis de Saída. Defina o alias como `edge` para essa variável. Se você tiver vários registros de contêiner para hospedar suas imagens de módulo, você precisará duplicar essa tarefa, selecione o registro de contêiner diferente e usar **Ignorar módulos** nas configurações avançadas para ignorar as imagens que não são para esse registro específico.

    ![Configurar a tarefa de efetuar push de imagens de módulo](./media/how-to-ci-cd/push.png)

1. Na tarefa **Publish Build Artifacts**, você especifica o arquivo de implantação gerado pela tarefa de build. Defina o **Caminho para publicar** para `$(edge.DEPLOYMENT_FILE_PATH)`.

    ![Configurar tarefa de publicar artefato](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Abra a guia **Gatilhos** e ative o gatilho **Integração contínua**. Verifique se o branch que contém seu código está incluído.

    ![Ativar o gatilho de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

    Salve o novo pipeline de build com o botão **Salvar**.


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>Configurar o Azure Pipelines para implantação contínua
Nesta seção, você criará um pipeline de lançamento configurado para ser executado automaticamente quando seu pipeline de build remover artefatos e ele mostrará logs de implantação no Azure Pipelines.

1. Na guia **Versões**, escolha **+ Novo pipeline**. Ou, se você já tiver pipelines de lançamento, escolha o botão **+Novo** e clique em **+Novo pipeline de lançamento**.  

    ![Adicionar pipeline de lançamento](./media/how-to-ci-cd/add-release-pipeline.png)

    Na janela **Selecionar um modelo**, escolha **começar com um Trabalho vazio**.

    ![Começar um trabalho vazio](./media/how-to-ci-cd/start-with-empty-job.png)

2. Em seguida, o pipeline de lançamento inicializa com um estágio: **Estágio 1**. Renomeie o **Estágio 1** como **Garantia de Qualidade** e trate-o como um ambiente de teste. Em um pipeline de implantação contínua típico, normalmente existem vários estágios, você pode criar mais com base na sua prática de DevOps.

    ![Criar um estágio de ambiente de teste](./media/how-to-ci-cd/QA-env.png)

3. Link da versão para os artefatos de build. Clique em **Adicionar** na área de artefatos.

    ![Adicionar artefatos](./media/how-to-ci-cd/add-artifacts.png)  
    
    Em **Adicionar uma página de artefato**, escolha **Build** como o Tipo de origem. Em seguida, selecione o projeto e o pipeline de build que você criou. Em seguida, selecione**Adicionar**.

    ![Adicionar um artefato de build](./media/how-to-ci-cd/add-an-artifact.png)

    Abra o gatilho de implantação contínua para que a nova versão seja criada sempre que um novo build estiver disponível.

    ![Configurar um gatilho de implantação contínua](./media/how-to-ci-cd/add-a-trigger.png)

4. Navegue até o **estágio Garantia de Qualidade** e configure as tarefas nesse estágio.

    ![Configurar tarefas de Garantia de Qualidade](./media/how-to-ci-cd/view-stage-tasks.png)

   A tarefa de implantação não diferencia a plataforma, o que significa que você pode escolher **VS2017 hospedado** ou **Ubuntu 1604 hospedado** no **Pool de agentes** (ou qualquer outro agente gerenciado por você mesmo). Clique em "+" e adicione uma tarefa.

    ![Adicionar tarefas para Garantia de Qualidade](./media/how-to-ci-cd/add-task-qa.png)

5. Na tarefa do Azure IoT Edge, navegue até a lista suspensa **Ação**, selecione **Implantar no dispositivo do IoT Edge**. Selecione sua **assinatura do Azure** e insira seu **nome do Hub IoT**. Você pode escolher implantar em um único dispositivo ou em vários. Se você estiver implantando em **vários dispositivos**, será necessário especificar a **condição de destino** do dispositivo. A condição de destino é um filtro para corresponder a um conjunto de dispositivos do Edge no Hub IoT. Se você quiser usar Marcas de Dispositivo como a condição, será necessário atualizar as Marcas de dispositivos correspondentes com o dispositivo gêmeo do Hub IoT. Atualize a **ID de implantação do IoT Edge** para "deploy-qa" em configurações avançadas. Considere que você tem vários dispositivos do IoT Edge que foram marcados como “garantia de qualidade”, a configuração da tarefa deve ser como na captura de tela a seguir. 

    ![Implantar na garantia de qualidade](./media/how-to-ci-cd/deploy-to-qa.png)

    Salve o novo pipeline de build com o botão **Salvar**. E, em seguida, clique em **Pipeline** para voltar para o pipeline.

6. A segunda etapa é para seu ambiente de produção. Para adicionar um novo estágio "PROD", você pode simplesmente clonar o estágio de "Garantia de Qualidade" e renomear estágio clonado como **PROD**,

    ![Clonar estágio](./media/how-to-ci-cd/clone-stage.png)

7. Configure as tarefas para seu ambiente de produção. Considere que você tem vários dispositivos IoT Edge que foram marcados como “prod”, nas configurações da tarefa, atualize a Condição de Destino para “prod” e defina a ID de implantação como “deploy-prod” nas configurações avançadas. Salve ele com o botão **Salvar**. E, em seguida, clique em **Pipeline** para voltar para o pipeline.
    
    ![Implantar na produção](./media/how-to-ci-cd/deploy-to-prod.png)

7. Atualmente, nosso artefato de build será disparado continuamente no estágio **Garantia de Qualidade** e, em seguida, no estágio **PROD**. Mas na maioria das vezes você precisa integrar alguns casos de teste nos dispositivos de garantia de qualidade e aprovar manualmente os bits. Posteriormente, os bits serão implantados no ambiente PROD. Configure uma aprovação no estágio PROD como a captura de tela a seguir.

    1. Abra o painel de configuração **Condições de pré-implantação**.

        ![Abrir Condições de pré-implantação](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Defina **Habilitado** em **Aprovações de pré-implantação**. E preencha a entrada **Aprovadores**. Salve ele com o botão **Salvar**.
    
        ![Definir condições](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Agora seu pipeline de lançamento foi configurado conforme a captura de tela.

    ![Pipeline de lançamento](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificar o CI/CD do IoT Edge com os pipelines de lançamento e de build

Nesta seção, você vai disparar um build para fazer com que o pipeline de CI/CD funcione. Em seguida, verifique se a implantação é bem-sucedida.

1. Para disparar um trabalho de build, você pode efetuar push da confirmação do repositório do código-fonte ou dispará-lo manualmente. Você pode disparar um trabalho de build em seu pipeline de build clicando no botão **Fila** como na captura de tela a seguir.

    ![Gatilho manual](./media/how-to-ci-cd/manual-trigger.png)

2. Se o pipeline de build for concluído com êxito, ele disparará uma versão o estágio **Garantia de Qualidade**. Navegue até os logs do pipeline de build e você deverá ver a seguinte captura de tela.

    ![Logs de build](./media/how-to-ci-cd/build-logs.png)

3. A implantação com êxito para o estágio **Garantia de Qualidade** dispararia uma notificação para o aprovador. Navegue até o pipeline de lançamento, você pode ver a seguinte captura de tela. 

    ![Aprovação pendente](./media/how-to-ci-cd/pending-approval.png)


4. Depois de o aprovador aprovar essa alteração, ela poderá ser implantada em **PROD**.

    ![Implantar em prod](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Próximas etapas

* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
