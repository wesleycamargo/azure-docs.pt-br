---
title: Implantar o AKS (Serviço de Kubernetes do Azure) usando o Jenkins e o padrão de implantação azul/verde
description: Saiba como implantar o AKS (Serviço de Kubernetes do Azure) usando o Jenkins e o padrão de implantação azul/verde
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 472622f78303593b7a4d5d5136aa47f34a1f44b1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227734"
---
# <a name="deploy-to-azure-kubernetes-service-aks-using-jenkins-and-bluegreen-deployment-pattern"></a>Implantar o AKS (Serviço de Kubernetes do Azure) usando o Jenkins e o padrão de implantação azul/verde

O Serviço de Kubernetes do Azure (AKS) gerencia seu ambiente Kubernetes hospedado, tornando rápido e fácil implantar e gerenciar os aplicativos em contêiner sem conhecimento de orquestração do contêiner. O AKS também elimina a sobrecarga das operações em andamento e a manutenção provisionando, atualizando e dimensionando recursos sob demanda, sem colocar seus aplicativos offline. Para obter mais informações sobre o AKS, consulte a [documentação do AKS](/azure/aks/).

Implantação azul/verde é um padrão DevOps CD (Entrega Contínua) que se baseia em manter uma versão existente (azul) em tempo real, enquanto uma nova versão (verde) é implantada. Normalmente, esse padrão emprega o balanceamento de carga para direcionar o crescente aumento de tráfego à implantação verde. Se o monitoramento descobrir um incidente, o tráfego pode ser redirecionado para a implantação azul ainda em execução. Para obter mais informações sobre Entrega Contínua, consulte o artigo [o que é Entrega Contínua](/azure/devops/what-is-continuous-delivery).

Neste tutorial, você saberá como executar as seguintes tarefas aprendendo a implantar no AKS usando o Jenkins e o padrão de implantação azul/verde:

> [!div class="checklist"]
> * Saiba mais sobre o padrão de implantação azul/verde
> * Crie um cluster de Kubernetes gerenciado.
> * Executar um script de exemplo para configurar um cluster de Kubernetes
> * Configurar manualmente um cluster de Kubernetes
> * Criar e executar um trabalho do Jenkins

## <a name="prerequisites"></a>Pré-requisitos
- [Conta do GitHub](https://github.com): é necessário uma conta do GitHub para clonar o repositório de exemplo.
- [CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest): a CLI 2.0 do Azure é usada para criar o cluster de Kubernetes.
- [Chocolatey](https://chocolatey.org) - um gerenciador de pacotes usado para instalar o kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): uma interface de linha de comando para executar comandos em clusters de Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): um processador JSON de linha de comando leve.

## <a name="clone-the-sample-app-from-github"></a>Clonar o aplicativo de exemplo do GitHub

Um aplicativo de exemplo que ilustra como implantar no AKS usando o Jenkins e o padrão azul/verde está no repositório da Microsoft no GitHub. Nesta seção, você cria um fork desse repositório no GitHub e clona o aplicativo no sistema local.

1. Navegue até o repositório GitHub para o aplicativo de exemplo [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Exemplo de aplicativo no repositório GitHub da Microsoft.](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Crie o fork do repositório, selecionando **Fork** no canto superior direito da página e siga as instruções para criar fork do repositório na conta do GitHub.

    ![Crie o fork do aplicativo de exemplo para a conta do GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Após criar o fork do repositório, você verá que o nome da conta alterou para o nome da sua conta e uma nota indicará de onde o repositório foi bifurcado (Microsoft).

    ![Aplicativo de exemplo após criar fork para outra conta do GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Selecione **Clonar ou baixar**.

    ![O GitHub permite clonar ou baixar rapidamente um repositório.](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Na janela **Clonar com HTTPS**, selecione o ícone de cópia.

    ![Copie a URL de clone para a área de transferência.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Abra uma janela de Bash ou terminal.

1. Altere os diretórios para o local desejado onde você quer armazenar a cópia local (clone) do repositório.

1. Usando o comando `git clone`, clone a URL que você copiou anteriormente.

    ![Digite "git clone" e a URL de clone para criar um clone do repositório.](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Selecione a tecla &lt;Enter> para iniciar o processo de clonagem.

    ![O comando "git clone" cria uma cópia pessoal do repositório em que é possível testar](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Altere os diretórios para o diretório criado recentemente que contém o clone da origem do aplicativo.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Crie e configure um cluster de Kubernetes gerenciado

Nesta seção, você executa as seguintes etapas:

- Use a CLI 2.0 do Azure para criar um cluster de Kubernetes gerenciado.
- Saiba como configurar um cluster usando o script de configuração ou manualmente.
- Crie o Registro de Contêiner do Azure.

> [!NOTE]   
> O AKS está atualmente em versão prévia. Para obter informações sobre como habilitar a versão prévia para a assinatura do Azure. Para mais detalhes, consulte [Início Rápido: Implantar um cluster do Serviço de Kubernetes do Azure (AKS) ](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Usar a CLI 2.0 do Azure para criar um cluster de Kubernetes gerenciado
Para criar um cluster de Kubernetes gerenciado com [CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), certifique-se de estar usando a CLI do Azure versão 2.0.25 ou posterior.

1. Entre na sua conta do Azure. Ao inserir o seguinte comando `az login`, instruções são fornecidas para explicar como concluir a entrada. 
    
    ```bash
    az login
    ```

1. Quando você executa o comando `az login` na etapa anterior, uma lista de todas as suas assinaturas do Azure será exibida (juntamente com suas IDs de assinatura). Nesta etapa, você deve definir a assinatura do Azure padrão. Substitua o espaço reservado &lt;your-subscription-id > pela ID de assinatura do Azure desejada. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Crie um grupos de recursos. Substitua o espaço reservado &lt;your-resource-group-name > pelo nome do novo grupo de recursos e substitua o espaço reservado &lt;your-location> pelo local. O comando `az account list-locations` exibe todos os locais do Azure. Durante a visualização do AKS, nem todos os locais estão disponíveis. Se você inserir um local que não é válido neste momento, a mensagem de erro listará os locais disponíveis.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Crie o cluster de Kubernetes. Substituir &lt;your-resource-group-name > pelo nome do grupo de recursos criado na etapa anterior e substitua o &lt;you-kubernetes-cluster-name> pelo nome do novo cluster. (A conclusão desse processo pode demorar vários minutos.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Configurar o cluster de Kubernetes

A configuração de uma implantação azul/verde no AKS pode ser feita com um script de instalação fornecido no exemplo clonado anteriormente ou manualmente. Nesta seção, você verá como fazer ambos.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Configurar o cluster de Kubernetes por meio do exemplo de script de instalação
1. Edite o arquivo **deploy/aks/setup/setup.sh**, substituindo os seguintes espaços reservados pelos valores apropriados para o ambiente: 

    - **&lt;your-resource-group-name>**
    - **&lt;your-kubernetes-cluster-name>**
    - **&lt;your-location>**
    - **&lt;your-dns-name-suffix>**

    ![O script setup.sh contém vários espaços reservados que podem ser modificados para o ambiente.](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Execute o script de instalação.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Configurar um cluster de Kubernetes manualmente 
1. Baixe a configuração do Kubernetes na sua pasta de perfil.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Altere o diretório para o diretório **implantar/aks/instalação**. 

1. Execute os comandos **kubectl** a seguir para configurar os serviços para o ponto de extremidade público e os dois pontos de extremidade do teste.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Atualize o nome DNS para o público e os pontos de extremidade do teste. Quando um cluster de Kubernetes for criado, um [grupo de recursos adicionais](https://github.com/Azure/AKS/issues/3) será criado com o padrão de nomenclatura **MC_&lt;your-resource-group-name >_&lt;your-kubernetes-cluster-name>_&lt;your-location>**.

    Localizar os IPs públicos no grupo de recursos

    ![IP público no grupo de recursos](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Para cada um dos serviços, localize o endereço IP externo, executando o seguinte comando:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Atualize o nome DNS do endereço IP correspondente com o seguinte comando:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Repita a chamada para `todoapp-test-blue` e `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    O nome DNS deve ser exclusivo na assinatura. `<your-dns-name-suffix>` pode ser usado para garantir a exclusividade.

### <a name="create-azure-container-registry"></a>Criar o Registro de Contêiner do Azure

1. Execute o comando `az acr create` para criar um Registro de Contêiner do Azure. Após a criação do Registro de Contêiner do Azure, use `login server` como a URL de registro do Docker na próxima seção.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Execute o comando `az acr credential` para mostrar as credenciais do Registro de Contêiner do Azure. Observe o nome de usuário e a senha de registro do Docker, pois serão usados na próxima seção.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Preparar o servidor Jenkins

Nesta seção, você verá como preparar o servidor Jenkins para executar uma compilação que é boa para teste. No entanto, como explicado no artigo do Jenkins sobre as [implicações de segurança de compilação no mestre](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master), é recomendável usar um [agente de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) ou o [agente de Contêiner do Azure](https://plugins.jenkins.io/azure-container-agents) para acelerar um agente no Azure para executar as compilações. 

1. Implantar um [Mestre do Jenkins no Azure](https://aka.ms/jenkins-on-azure).

1. Conecte ao servidor via SSH e instale as ferramentas de compilação no servidor onde você executa a compilação.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Instale o Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Verifique se o usuário `jenkins` tem permissão para executar os comandos `docker`.

1. [Instale o kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Baixe o jq](https://stedolan.github.io/jq/download/).

1. Instale o jq usando o comando a seguir:

   ```bash
   sudo apt-get install jq
   ```
   
1. Instale os plug-ins no Jenkins executando as etapas a seguir no painel do Jenkins:

    1. Selecione **Gerenciar o Jenkins > Gerenciar Plug-ins > Disponíveis**.
    1. Pesquise e instale o Plug-in do Serviço de Contêiner do Azure.

1. Você precisará adicionar as credenciais que serão usadas para gerenciar recursos no Azure. Se você ainda não tiver o plug-in, instale o plug-in**Credencial do Azure**.

1. Adicione a credencial Entidade de Serviço do Azure como tipo/variante **Entidade de Serviço do Azure do Microsoft Azure**.

1. Adicione o nome de usuário de Registro do Docker do Azure e a senha (conforme obtidas na seção **Criar Registro de Contêiner do Azure**) como tipo/variante **Nome de usuário com senha**.

## <a name="edit-the-jenkinsfile"></a>Editar o Jenkinsfile

1. No seu próprio repositório, navegue até `/deploy/aks/` e abra `Jenkinsfile`

2. Atualize o arquivo conforme descrito a seguir:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    E atualize a ID de credencial do ACR:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Criar o trabalho
1. Adicione um novo trabalho no tipo **Pipeline**.

1. Selecione**Pipeline > Definição > Script de pipeline do SCM**.

1. Insira a url do repositório SCM com o &lt;your-forked-repo>

1. Insira o caminho do script como `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Executar o trabalho

1. Verifique se é possível executar o projeto com êxito no ambiente local. [Executar o projeto no computador local](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)

1. Execute o trabalho do Jenkins. Ao executar o trabalho do Jenkins pela primeira vez, o Jenkins implantará o aplicativo de tarefas pendentes no ambiente Azul, que é o ambiente inativo padrão. 

1. Para verificar se o trabalho foi executado, navegue até as urls:
    - Ponto de extremidade público: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Ponto de extremidade azul - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Ponto de extremidade verde - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Os pontos de extremidade público e Azul têm a mesma atualização, enquanto que ponto de extremidade verde mostra a imagem tomcat padrão. 

Se você executar a compilação mais de uma vez, ela alterna entre as implantações Azul e Verde. Em outras palavras, se o ambiente atual for azul, o trabalho será implantar/testar para o ambiente Verde e, em seguida, atualizar o ponto de extremidade público do aplicativo para rotear o tráfego para o ambiente Verde se tudo estiver correto com o teste.

## <a name="additional-information"></a>Informações adicionais

Para obter mais informações sobre a implantação de nenhum tempo de inatividade, confira este [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua todos os recursos criados neste tutorial.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>solução de problemas

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como implantar o AKS (Serviço de Kubernetes do Azure) usando o Jenkins e o padrão de implantação azul/verde. Para saber mais sobre o provedor do Jenkins do Azure, consulte o Jenkins no site do Azure.

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/jenkins/)