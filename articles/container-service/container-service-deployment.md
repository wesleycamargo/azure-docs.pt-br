---
title: "Implantar um cluster do contêiner Docker no Azure | Microsoft Docs"
description: "Implante um cluster do serviço de contêiner do Azure usando o portal do Azure ou o modelo do Azure Resource Manager."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure, dcos, swarm, kubernetes, serviço de contêiner do azure, acs"
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>Implantar um cluster do Serviço de Contêiner do Azure



O Serviço de Contêiner do Azure fornece implantação rápida de soluções populares de orquestração e clustering de contêiner de software livre. Este documento orienta a implantar um cluster do serviço de contêiner do Azure usando o portal do Azure ou um modelo de início rápido do Azure Resource Manager. 

> [!NOTE]
> O suporte a Kubernetes no Serviço de Contêiner do Azure está atualmente em visualização.

Você também pode implantar um cluster do serviço de contêiner do Azure usando a [CLI do Azure 2.0 (Visualização)](container-service-create-acs-cluster-cli.md) ou as APIs do Serviço de Contêiner do Azure.



## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: se não tiver uma, inscreva-se em uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

* **Chave pública SSH**: durante a implantação por meio do portal ou um dos modelos de início rápido do Azure, você precisa fornecer a chave pública para autenticação no serviço de contêiner do Azure de máquinas virtuais. Para criar chaves de Secure Shell (SSH), consulte o [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) orientação. 

* **ID de entidade de cliente e o segredo de serviço** (Kubernetes): para obter mais informações e diretrizes para criar uma entidade de serviço, consulte [sobre a entidade de serviço para um cluster Kubernetes](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Criar um cluster usando o portal do Azure
1. Entre no portal do Azure, selecione **Novo** e pesquise no Azure Marketplace o **Serviço de Contêiner do Azure**.

    ![Serviço de contêiner do Azure no Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Selecione **Serviço de Contêiner do Azure** e clique em **Criar**.

    ![Criar um serviço de contêiner](media/container-service-deployment/acs-portal2.png)  <br />

3. Insira as seguintes informações:

    * **Nome do usuário**: o nome de usuário para uma conta em cada uma das máquinas virtuais e conjuntos de escala de máquina virtual no cluster do Serviço de Contêiner do Azure.
    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo.
    * **Local**: selecione uma região do Azure para a implantação do Serviço de Contêiner do Azure.
    * **Chave pública SSH**: adicione a chave pública que será usada para autenticação em relação a Máquinas Virtuais do Serviço de Contêiner do Azure. É importante que esta chave não contenha quebras de linha e que inclua o prefixo `ssh-rsa`. O sufixo `username@domain` é opcional. A chave deve ser algo semelhante ao seguinte: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 

4. Clique em **OK** quando estiver pronto para continuar.

    ![Configurações Básicas](media/container-service-deployment/acs-portal3.png)  <br />

5. Selecione um tipo de Orquestração. As opções são:

  * **DC/OS**: implanta um cluster de DC/OS.
  * **Swarm**: implanta um cluster Docker Swarm.
  * **Kubernetes**: implanta um cluster Kubernetes


6. Clique em **OK** quando estiver pronto para continuar.

    ![Escolha um orquestrador](media/container-service-deployment/acs-portal4-new.png)  <br />

7. Se **Kubernetes** estiver selecionado na lista suspensa, você precisará inserir uma ID de cliente de entidade de serviço e o segredo do cliente da entidade de serviço. Para obter mais informações, confira [Sobre a entidade de serviço para um cluster Kubernetes](container-service-kubernetes-service-principal.md).

    ![Inserir a entidade de serviço para Kubernetes](media/container-service-deployment/acs-portal10.png)  <br />

7. Na folha de configurações do **Serviço de Contêiner do Azure**, insira as seguintes informações:

    * **Contagem de mestres**: o número de mestres do cluster. Se Kubernetes for selecionado, o número de mestre é definido como um padrão 1.
    * **Contagem de agentes**: para Docker Swarm e Kubernetes, esse valor será o número inicial de agentes no conjunto de dimensionamento de agentes. Para DC/OS, é o número inicial de agentes em um conjunto de escala privado. Além disso, é criado um conjunto de escala pública para DC/OS, que contém um número predeterminado de agentes. O número de agentes nesse conjunto de escala público é determinado pelo número de mestres criados no cluster: um agente público para um mestre e dois agentes públicos para três ou cinco mestres.
    * **Tamanho da máquina virtual de agente**: o tamanho das máquinas virtuais de agente.
    * **Prefixo DNS**: um nome exclusivo no mundo que será usado para prefixar partes-chave dos nomes de domínio totalmente qualificados para o serviço.
    * **Diagnóstico VM**: para alguns orquestradores, você pode optar por habilitar o diagnóstico de VM.

8. Clique em **OK** quando estiver pronto para continuar.

    ![Configurações de serviço de contêiner](media/container-service-deployment/acs-portal5.png)  <br />

9. Clique em **OK** após a validação de serviço.

    ![Validação](media/container-service-deployment/acs-portal6.png)  <br />

10. Examine os termos. Clique em **Comprar** para iniciar o processo de implantação.

    ![Purchase](media/container-service-deployment/acs-portal7.png)  <br />

    Se tiver optado por fixar a implantação no portal do Azure, você poderá ver o status da implantação.

    ![Status da Implantação](media/container-service-deployment/acs-portal8.png)  <br />

A implantação leva vários minutos para ser concluída. Em seguida, o cluster do serviço de contêiner do Azure está pronto para uso.



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Criar um cluster usando um modelo de início rápido
Modelos de início rápido do Azure estão disponíveis para implantar um cluster no serviço de contêiner do Azure. Os modelos de início rápido fornecidos também podem ser modificados para incluir uma configuração do Azure avançada ou adicional. Para criar um cluster do serviço de contêiner do Azure usando um modelo de início rápido do Azure, você precisa de uma assinatura do Azure. Se não tiver uma, inscreva-se para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Siga estas etapas para implantar um cluster usando um modelo e a CLI do Azure 2.0 (Visualização) (veja [instruções de instalação e configuração](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> Se você estiver em um sistema Windows, você pode usar etapas semelhantes para implantar um modelo usando o Azure PowerShell. Consulte as etapas nesta seção. Você também pode implantar um modelo por meio de [portal](../azure-resource-manager/resource-group-template-deploy-portal.md) ou outros métodos.

1. Para implantar um cluster DC/OS, Docker Swarm ou Kubernetes, selecione um dos modelos a seguir no GitHub. Observe que os modelos do DC/OS e do Swarm são iguais, com a exceção da seleção do orquestrador padrão.

    * [Modelo DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modelo Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Faça logon sua conta do Azure (`az login`) e certifique-se de que a CLI do Azure está conectada à sua assinatura do Azure. Você pode ver a assinatura padrão usando o comando a seguir:

    ```azurecli
    az account show
    ```
    
    Se você tiver mais de uma assinatura e a necessidade de definir uma assinatura padrão diferente, execute `az account set --subscription` e especifique o nome ou ID de assinatura.

3. Como prática recomendada, use um novo grupo de recursos para a implantação. Para criar um grupo de recursos, use o comando `az group create` para especificar um nome de grupo de recursos e um local: 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Crie um arquivo JSON que contém os parâmetros necessários do modelo. Baixe o arquivo de parâmetros chamado `azuredeploy.parameters.json` que acompanha o modelo do Serviço de Contêiner do Azure `azuredeploy.json` no GitHub. Insira valores de parâmetros necessários para seu cluster. 

    Por exemplo, para usar o [modelo da DC/SO](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos), fornecer valores de parâmetro para `dnsNamePrefix` e `sshRSAPublicKey`. Consulte as descrições em `azuredeploy.json` e opções para outros parâmetros.  
 

5. Criar um cluster do serviço de contêiner, passando o arquivo de parâmetros de implantação com o seguinte comando, onde:

    * **RESOURCE_GROUP** é o nome do grupo de recursos que você criou na etapa anterior.
    * **DEPLOYMENT_NAME** (opcional) é um nome que você atribui à implantação.
    * **TEMPLATE_URI** é o local do arquivo de implantação `azuredeploy.json`. Esse URI deve ser o arquivo Bruto, não um ponteiro para a interface do usuário do GitHub. Para localizar esse URI, selecione o `azuredeploy.json` arquivo no GitHub e, em seguida, clique no **Raw** botão.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    Você também pode fornecer parâmetros como uma cadeia de caracteres formatada em JSON na linha de comando. Use um comando semelhante a este:

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > A implantação leva vários minutos para ser concluída.
    > 

### <a name="equivalent-powershell-commands"></a>Comandos equivalentes do PowerShell
Você também pode implantar um modelo do Serviço de Contêiner do Azure com o PowerShell. Este documento se baseia na versão 1.0 do [módulo do Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

1. Para implantar um cluster DC/OS, Docker Swarm ou Kubernetes, selecione um dos modelos a seguir. Observe que os modelos do DC/OS e do Swarm são iguais, com a exceção da seleção do orquestrador padrão.

    * [Modelo DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modelo Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Antes de criar um cluster em sua assinatura do Azure, verifique se sua sessão do PowerShell foi conectada ao Azure. Você pode fazer isso com o comando `Get-AzureRMSubscription` :

    ```powershell
    Get-AzureRmSubscription
    ```

3. Se precisar entrar no Azure, use o comando `Login-AzureRMAccount` :

    ```powershell
    Login-AzureRmAccount
    ```

4. Como prática recomendada, use um novo grupo de recursos para a implantação. Para criar um grupo de recursos, use o comando `New-AzureRmResourceGroup` e especifique um nome de grupo de recursos e uma região de destino:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Depois de criar um grupo de recursos, você poderá criar seu cluster com o comando a seguir. O URI do modelo desejado será especificado para o parâmetro `-TemplateUri` . Quando você executar esse comando, o PowerShell solicitará os valores de parâmetros de implantação.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Fornecer parâmetros de modelo
Se estiver familiarizado com o PowerShell, você saberá que pode percorrer os parâmetros disponíveis para um cmdlet digitando um sinal de subtração (-) e, em seguida, pressionando a tecla TAB. Essa mesma funcionalidade também funciona com os parâmetros definidos no modelo. Assim que você digitar o nome do modelo, o cmdlet buscará o modelo, o analisará os parâmetros e adicionará dinamicamente os parâmetros do modelo ao comando. Isso facilita muito a especificação dos valores de parâmetros do modelo. E, se você esquecer um valor de parâmetro necessário, o PowerShell solicitará o valor.

A seguir, o comando completo com os parâmetros incluídos. Você pode fornecer seus próprios valores para os nomes dos recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Próximas etapas
Agora que você tem um cluster em funcionamento, confira estes documentos para obter detalhes sobre conexão e gerenciamento:

* [Conectar a um cluster do Serviço de Contêiner do Azure](container-service-connect.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o DC/SO](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o Docker Swarm](container-service-docker-swarm.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o Kubernetes](container-service-kubernetes-walkthrough.md)



<!--HONumber=Feb17_HO1-->


