---
title: "Compilar um projeto no Azure usando Jenkins e Instâncias de Contêiner do Azure"
description: "Saiba como usar o plug-in do Agente de Contêiner do Azure para Jenkins para compilar um projeto no Azure com Instâncias de Contêiner do Azure"
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 04a60bf021ec6e265a3880264386ad32ec7e8177
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Compilar um projeto no Azure usando Jenkins e Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure facilitam para você começar e executar, sem a necessidade de provisionar máquinas virtuais ou adotar um serviço de nível superior. As Instâncias de Contêiner do Azure fornece a cobrança por segundo com base na capacidade que você necessita; tornando-se uma opção atraente para cargas de trabalho temporárias, como executar uma compilação.

Você aprenderá como:
> [!div class="checklist"]
> * Instalar e configurar um servidor Jenkins no Azure
> * Instalar e configurar o plug-in de Agentes de Contêiner do Azure para Jenkins
> * Usar Instâncias de Contêiner do Azure para criar o [aplicativo de exemplo PetClinic Spring](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure** - Para saber mais sobre as opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [um mês de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **CLI do Azure 2.0 ou o Azure Cloud Shell** - Instalar um dos seguintes produtos no qual inserir comandos do Azure:

    - [CLI do Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) - Permite que você execute comandos do Azure de um comando ou janela do terminal.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) - Experiência de shell baseada no navegador O Cloud Shell permite o acesso a uma experiência de linha de comando baseada em navegador, que foi criada tendo em mente as tarefas de gerenciamento do Azure.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Instalar um servidor Jenkins no Azure usando a imagem do Jenkins Marketplace

Jenkins dá suporte a um modelo onde o servidor Jenkins delega trabalho para um ou mais agentes para permitir que uma única instalação Jenkins hospede um número grande de projetos ou para fornecer ambientes diferentes, necessários para compilações ou testes. As etapas nesta seção o orientam durante a instalação e configuração de um servidor Jenkins no Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Conectar ao servidor Jenkins em execução no Azure

Depois que você instalou Jenkins no Azure, você precisa se conectar ao Jenkins. As etapas a seguir orientam você durante a configuração de uma conexão SSH para a VM Jenkins em execução no Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Atualizar o DNS Jenkins

Jenkins precisa saber sua própria URL durante a criação de links que apontam para si mesmo. Por exemplo, a URL precisa ser usada quando Jenkins envia emails que contenham links diretos para criar resultados. 

Esta seção orienta a definir a URL Jenkins.

1. Em seu navegador, navegue até o painel de Jenkins em `http://localhost:8080`.

1. Selecione **Gerenciar Jenkins**.

    ![Opções para gerenciar Jenkins no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selecione **Configurar Sistema**.

    ![Gerenciar a opção de plug-ins Jenkins no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Em **Localização Jenkins**, insira a URL do seu servidor Jenkins.

1. Selecione **Salvar**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Atualizar Jenkins para permitir o protocolo de inicialização de rede de Java (JNLP)

O agente Jenkins conecta-se com o servidor Jenkins via protocolo de inicialização de rede de Java (JNLP). Esta seção explica como especificar uma porta para agentes JNLP usar ao se comunicar com o servidor Jenkins.

1. No painel do Jenkins, clique em **Gerenciar Jenkins**.

    ![Opções para gerenciar Jenkins no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selecione **Configurar a Segurança Global**.

    ![Configurar a segurança global no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. Em **Agentes**, selecione **Fixo**e insira uma porta. A captura de tela mostra um valor de porta de 12345 como exemplo. Você deve especificar uma porta que faça sentido para o seu ambiente.

    ![Atualizar as configurações de segurança global Jenkins para permitir JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Selecione **Salvar**.

1. Usando o CLI do Azure 2.0 ou o Cloud Shell, digite o seguinte comando para criar uma regra de entrada para o grupo de segurança de rede Jenkins:

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Crie e adicione uma entidade de serviço do Azure nas credenciais do Jenkins

É necessária uma entidade de serviço do Azure para implantar no Azure. As etapas a seguir o orientará pelo processo de criação de uma entidade de serviço (se você ainda não tiver uma) e atualizar Jenkins com sua entidade de serviço.

1. Se você já tiver uma entidade de serviço (e conhecer sua ID de assinatura, locatário, appId e senha), você pode ignorar esta etapa. Se você precisar criar uma entidade de segurança, consulte o artigo [Criar uma entidade de serviço Azure com o CLI do Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Ao criar a entidade de segurança, anote os valores para o ID de assinatura, o locatário, a appId e a senha.

1. Selecione **Credenciais**.

    ![Gerenciar a opção de credenciais no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. Em **Credenciais**, selecione **Sistema**.

    ![Gerenciar a opção de credenciais do sistema no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Selecione **Credenciais globais (irrestrito)**.

    ![Gerenciar a opção de credenciais do sistema global no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Selecione **Adicionar algumas credenciais**.

    ![Adicionar credenciais no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. Na lista suspensa **Tipo**, selecione **entidade de serviço do Microsoft Azure** para fazer com que a página exiba os campos específicos para adicionar uma entidade de serviço. Em seguida, forneça os valores solicitados da seguinte maneira:

    - **Escopo** - Selecione a opção **Global (Jenkins, nós, itens, todos os itens filhos, etc.)** .
    - **ID da assinatura** - Use a ID de assinatura do Azure especificada durante a execução `az account set`.
    - **ID do cliente** - Use o `appId` valor retornado de `az ad sp create-for-rbac`.
    - **Segredo do cliente** - Use o `password` valor retornado de `az ad sp create-for-rbac`.
    - **ID do locatário** - Use o `tenant` valor retornado de `az ad sp create-for-rbac`.
    - **Ambiente do Azure** - Selecione `Azure`.
    - **ID** - Digite `myTestSp`. Esse valor é usado de novo posteriormente neste tutorial.
    - **Descrição** - (Opcional) Insira um valor de descrição para essa entidade de segurança.

    ![Especifique novas propriedades de entidade de serviço no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Depois que você inseriu as informações que definem a entidade de segurança, você pode opcionalmente selecionar **Verificar a entidade de serviço** para garantir que tudo está funcionando corretamente. Se a entidade de serviço está definida corretamente, você verá uma mensagem dizendo "Entidade de serviço do Microsoft Azure verificada com êxito." abaixo do campo **Descrição**.

1. Quando terminar, selecione **OK** para adicionar a entidade de segurança para Jenkins. O painel Jenkins exibe a entidade de segurança recém-adicionada na página **Credenciais Globais**.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Criar um grupo de recursos do Azure para suas Instâncias de Contêiner do Azure

Instâncias de Contêiner do Azure devem ser colocadas em um grupo de recursos do Azure. Um grupo de recursos do Azure é um contêiner que mantém os recursos relacionados a uma solução do Azure.

Usando o CLI do Azure 2.0 ou o Cloud Shell, digite o seguinte comando para criar um grupo de recursos chamado `JenkinsAciResourceGroup` no local `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

Quando terminar, o comando `az group create` exibirá informações semelhantes ao seguinte exemplo:

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Instalar o plug-in de Agentes de Contêiner do Azure para Jenkins

Se você já instalou o plug-in de Agentes de Contêiner do Azure, poderá ignorar esta seção.

Após criar o grupo de recursos do Azure para seu agente Jenkins, as etapas a seguir ilustram como instalar o plug-in de Agentes de Contêiner do Azure:

1. No painel do Jenkins, clique em **Gerenciar Jenkins**.

    ![Opções para gerenciar Jenkins no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selecione **Gerenciar plug-ins**.

    ![Gerenciar a opção de plug-ins Jenkins no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Selecione **Disponível**.

    ![Exibir a opção de plug-ins Jenkins disponível no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Digite `Azure Container Agents` para a caixa de texto **Filtro**. (A lista filtra quando você insere o texto.)

    ![Filtrar os plug-ins Jenkins disponíveis no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Marque a caixa de seleção ao lado do plug-in de **Agentes de Contêiner do Azure** e uma das opções de instalação. Para fins desta demonstração, selecionei a opção **Instalar sem reinicialização**.

    ![Instalar os plug-ins de Agentes de Contêiner do Azure do painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  Depois de selecionar a opção para instalar os plug-ins desejados, o painel Jenkins exibe uma página detalhando o status do que você está instalando.

    ![Status de instalação dos plug-ins de Agentes de Contêiner do Azure do painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Para retornar à página principal do painel Jenkins, selecione **Voltar para o topo da página**.

## <a name="configure-the-azure-container-agents-plugin"></a>Configurar o plug-in de Agentes de Contêiner do Azure

Depois de instalar o plug-in de Agentes de Contêiner do Azure, esta seção orienta como configurar o plug-in no painel Jenkins.

1. No painel do Jenkins, clique em **Gerenciar Jenkins**.

    ![Opções para gerenciar Jenkins no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selecione **Configurar Sistema**.

    ![Gerenciar a opção de plug-ins Jenkins no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Localize a seção **Cloud** na parte inferior da página e da lista suspensa **Adicionar uma nova nuvem**, selecione **Instância de Contêiner do Azure**.

    ![Adicionar um novo provedor de nuvem do painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. Na seção **Instância de Contêiner do Azure**, especifique os seguintes valores:

    - **Nome da nuvem** - (Opcional uma vez que o valor padrão é um nome gerado automaticamente.) Especifique um nome para esta instância. 
    - **Credencial do Azure** - Selecione a seta suspensa e, em seguida, selecione a `myTestSp` entrada que identifica a entidade de serviço do Azure criada anteriormente.
    - **Grupo de Recursos** - Selecione a seta suspensa e, em seguida, selecione a `JenkinsAciResourceGroup` entrada que identifica o grupo de recursos da Instância do Contêiner do Azure criado anteriormente.

    ![Definindo as propriedades de Instância de Contêiner do Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Selecione a seta suspensa **Adicionar modelo de contêiner** e, em seguida, selecione **Modelo de Contêiner Aci**.

1. Na seção **Modelo de Contêiner Aci**, especifique os seguintes valores:

    - **Nome** - Digite `ACI-container`.
    - **Rótulos** - Digite `ACI-container`.
    - **Imagem de Docker** - Digite `cloudbees/jnlp-slave-with-java-build-tools`

    ![Definindo as propriedades de imagem da Instância de Contêiner do Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Selecione **Avançado**.

1. Selecione a seta suspensa da **Estratégia de Retenção** e selecione **Estratégia de Retenção do Contêiner Ocioso**. Ao selecionar essa opção, Jenkins mantém o agente até que nenhum trabalho novo seja executado no agente e o tempo ocioso especificado tiver decorrido.

    ![Definindo estratégia de retenção da Instância de Contêiner do Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Selecione **Salvar**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Criar o trabalho do aplicativo de Spring PetClinic no Jenkins

As etapas a seguir orientam você na criação de um trabalho Jenkins - como um projeto freestyle - para compilar o aplicativo de Spring PetClinic.

1. No painel do Jenkins, clique em **Novo Item**.

    ![Nova opção de menu do item no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Insira `myPetClinicProject` para o nome do item e selecione **Projeto Freestyle**.

    ![Novo projeto freestyle no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Selecione **OK**.

1. Selecione a guia **Geral** e especifique os seguintes valores:

    - **Restringir onde o projeto pode ser executado** - Selecione esta opção.
    - **Expressão de rótulo** - Digite `ACI-container`. Ao sair do campo, você verá uma mensagem confirmando que o rótulo é atendido pela configuração de nuvem criada na etapa anterior.

    ![Configurações gerais para um novo projeto freestyle no painel Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Selecione a guia **Gerenciamento do código-fonte** e especifique os seguintes valores:

    - **Gerenciamento do código-fonte** - Selecione **Git**.
    - **URL do repositório** - Insira a seguinte URL para o repositório GitHub do aplicativo de exemplo Spring PetClinic: `https://github.com/spring-projects/spring-petclinic.git`.

1. Selecione a guia **Compilar** e execute as seguintes tarefas:

    - Selecione a seta suspensa **Adicionar etapa de compilação** e selecione **Invocar destinos de nível superior Maven**.

    - Para **metas**, digite `package`.

1. Selecione **Salvar** para salvar a definição do novo projeto.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Compilar o trabalho do aplicativo de Spring PetClinic no Jenkins

É hora de compilar seu projeto! Esta seção explica como compilar um projeto do painel Jenkins.

1. No painel do Jenkins, selecione `myPetClinicProject`.

    ![Selecione o projeto a ser compilado do painel Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Selecione **Compilar agora**. 

    ![Compile o projeto do painel Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Quando você inicia uma compilação em Jenkins, a compilação é colocada na fila. No caso de um Agente de Contêiner do Azure, a compilação não pode ser executada até que o Agente de Contêiner do Azure seja iniciado e colocado online. Até lá, você verá uma mensagem indicando o nome do agente e o fato de que a compilação está pendente. (Esse processo leva cerca de cinco minutos, mas só é necessário na primeira vez que você usa o agente para uma compilação. As compilações subsequentes são muito mais rápidas, pois o agente está online nesse momento.)

    ![A compilação é colocada na fila até que o agente seja criado e colocado online.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    Depois de iniciada a compilação, uma barra de progresso indica que a compilação está em execução:

    ![A compilação está em execução quando você vê a barra de progresso.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Quando a barra de progresso desaparecer, selecione a seta ao lado do número de compilação. No menu de contexto, selecione **Saída do console**.

    ![Clique no item de menu do Log de Console para exibir as informações de compilação.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. As informações de log de console emitidas do processo de compilação. Para exibir todas as informações de compilação (incluindo informações sobre a compilação que está sendo executada remotamente no agente do Azure que você criou), selecione **log completo**.

    ![Clique no link do log completo para exibir informações mais detalhadas de compilação.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    O log completo exibe informações mais detalhadas de compilação:

    ![O log completo exibe informações mais detalhadas de compilação.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Para exibir a disposição da compilação, role até o final do log.

    ![A disposição de compilação é exibida na parte inferior do log de compilação.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Limpar recursos do Azure

Neste tutorial, você criou os recursos contidos em dois grupos de recursos do Azure: 
    - `JenkinsResourceGroup` - Contém os recursos do Azure para o servidor Jenkins.
    - `JenkinsAciResourceGroup` - Contém os recursos do Azure para o agente Jenkins.
    
Se você não precisar mais usar qualquer um dos recursos em um grupo de recursos do Azure, você pode excluir o grupo de recursos usando o `az group delete` comando da seguinte maneira (substituindo o &lt;Grupoderecursos> pelo nome do grupo de recursos que você deseja excluir):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Visite o Jenkins no hub do Azure para ver os artigos e exemplos mais recentes](https://docs.microsoft.com/azure/jenkins/)