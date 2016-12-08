---
title: "Implantar um cluster do Serviço de Contêiner do Azure | Microsoft Docs"
description: "Implante um cluster do Serviço de Contêiner do Azure usando o portal do Azure, a CLI do Azure ou o PowerShell."
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
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: a7d957fd4be4c823077b1220dfb8ed91070a0e97
ms.openlocfilehash: d056b9489eba1f97e8fb87f231b03d104c4cab66


---
# <a name="deploy-an-azure-container-service-cluster"></a>Implantar um cluster do Serviço de Contêiner do Azure
O Serviço de Contêiner do Azure fornece implantação rápida de soluções populares de orquestração e clustering de contêiner de software livre. Usando o Serviço de Contêiner do Azure, você pode implantar clusters DC/OS, Kubernetes e Docker Swarm com modelos do Azure Resource Manager ou por meio do Portal do Azure. Você implanta esses clusters usando Conjuntos de Escala de Máquina Virtual do Azure e tiram proveito das ofertas de rede e armazenamento do Azure. Para acessar o Serviço de Contêiner do Azure, você precisa de uma assinatura do Azure. Se não tiver uma, você poderá se inscrever para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento orienta você durante a implantação de um cluster do Serviço de Contêiner do Azure usando o [portal do Azure](#creating-a-service-using-the-azure-portal), a [CLI (interface de linha de comando) do Azure](#creating-a-service-using-the-azure-cli) e o [módulo do Azure PowerShell](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Criar um serviço usando o portal do Azure
Entre no portal do Azure, selecione **Novo** e pesquise no Azure Marketplace o **Serviço de Contêiner do Azure**.

![Criar implantação 1](media/acs-portal1.png)  <br />

Selecione **Serviço de Contêiner do Azure** e clique em **Criar**.

![Criar implantação 2](media/acs-portal2.png)  <br />

Insira as seguintes informações:

* **Nome de Usuário**: é o nome de usuário que será usado para uma conta em cada uma das máquinas virtuais e conjuntos de escala de máquina virtual no cluster do Serviço de Contêiner do Azure.
* **Assinatura**: selecione uma assinatura do Azure.
* **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo.
* **Local**: selecione uma região do Azure para a implantação do Serviço de Contêiner do Azure.
* **Chave pública SSH**: adicione a chave pública que será usada para autenticação em relação a Máquinas Virtuais do Serviço de Contêiner do Azure. É muito importante que a chave não contenha quebras de linha e que inclua o prefixo 'ssh-rsa' e o sufixo 'username@domain'. Ele deve ser algo semelhante ao seguinte: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. Para obter orientação sobre como criar chaves SSH (Secure Shell), confira os artigos sobre [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) e [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/).

Clique em **OK** quando estiver pronto para continuar.

![Criar implantação 3](media/acs-portal3.png)  <br />

Selecione um tipo de Orquestração. As opções são:

* **DC/OS**: implanta um cluster de DC/OS.
* **Swarm**: implanta um cluster Docker Swarm.
* **Kubernetes**: implanta um cluster Kubernetes.

Clique em **OK** quando estiver pronto para continuar.

![Criar implantação 4](media/acs-portal4-new.png)  <br />

Se **Kubernetes** estiver selecionado na lista suspensa, você precisará inserir a ID e o segredo do cliente da entidade de serviço.
Para saber mais sobre como criar uma entidade de serviço, visite [esta](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md) página 

![Criar implantação 4.5](media/acs-portal10.PNG)  <br />

Insira as seguintes informações:

* **Contagem de mestres**: o número de mestres do cluster. Se 'Kubernetes' for selecionada, o número de mestre é definido como um padrão de 1
* **Contagem de agentes**: para Docker Swarm e Kubernetes, esse será o número inicial de agentes no conjunto de dimensionamento de agentes. Para DC/OS, esse será o número inicial de agentes em um conjunto de escala privado. Além disso, é criado um conjunto de escala pública, que contém um número predeterminado de agentes. O número de agentes nesse conjunto de escala público é determinado pelo número de mestres criados no cluster: um agente público para um mestre e dois agentes públicos para três ou cinco mestres.
* **Tamanho da máquina virtual de agente**: o tamanho das máquinas virtuais de agente.
* **Prefixo DNS**: um nome exclusivo no mundo que será usado para prefixar partes-chave dos nomes de domínio totalmente qualificados para o serviço.

Clique em **OK** quando estiver pronto para continuar.

![Criar implantação 5](media/acs-portal5.png)  <br />

Clique em **OK** após a validação de serviço.

![Criar implantação 6](media/acs-portal6.png)  <br />

Clique em **Criar** para iniciar o processo de implantação.

![Criar implantação 7](media/acs-portal7.png)  <br />

Se tiver optado por fixar a implantação no portal do Azure, você poderá ver o status da implantação.

![Criar implantação 8](media/acs-portal8.png)  <br />

Quando a implantação for concluída, o cluster do Serviço de Contêiner do Azure estará pronto para uso.

## <a name="create-a-service-by-using-the-azure-cli"></a>Criar um serviço usando a CLI do Azure
Para criar uma instância do Serviço de Contêiner do Azure usando a linha de comando, você precisará de uma assinatura do Azure. Se não tiver uma, você poderá se inscrever para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Você também precisará ter [instalado](../xplat-cli-install.md) e [configurado](../xplat-cli-connect.md) a CLI do Azure.

Para implantar um cluster DC/OS ou Docker Swarm ou Kubernetes, selecione um dos modelos a seguir no GitHub. 

* [Modelo DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
* [Modelo Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

Em seguida, verifique se a CLI do Azure foi conectada a uma assinatura do Azure. Faça isso usando este comando:

```bash
azure account show
```
Se uma conta do Azure não for retornada, use o comando a seguir para fazer logon da CLI do Azure.

```bash
azure login -u user@domain.com
```

Em seguida, configure as ferramentas da CLI do Azure para usar o Azure Resource Manager.

```bash
azure config mode arm
```

Crie um cluster do grupo de recursos do Azure e um cluster do Serviço de Contêiner com o seguinte comando, em que:

* **RESOURCE_GROUP** é o nome do grupo de recursos que você deseja usar para esse serviço.
* **LOCATION** é a região do Azure e que a implantação do Grupo de Recursos e do Serviço de Contêiner do Azure será criada.
* **TEMPLATE_URI** é o local do arquivo de implantação. Observe que este deve ser o arquivo Bruto, não um ponteiro para a interface do usuário do GitHub. Para localizar essa URL, selecione o arquivo azuredeploy.json no GitHub e clique no botão **Bruto** .

> [!NOTE]
> Quando você executar esse comando, o shell solicitará valores de parâmetros de implantação.
> 
> 

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fornecer parâmetros de modelo
Esta versão do comando requer que você defina os parâmetros interativamente. Se você quiser fornecer parâmetros, como uma cadeia de caracteres formatada em JSON, poderá fazer isso com a opção `-p` . Por exemplo:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Como alternativa, você pode fornecer um arquivo de parâmetros formatado em JSON usando a opção `-e` :

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Para ver um arquivo de parâmetros de exemplo chamado `azuredeploy.parameters.json`, procure-o com os modelos do Serviço de Contêiner do Azure no GitHub.

## <a name="create-a-service-by-using-powershell"></a>Criar um serviço usando o PowerShell
Você também pode implantar um cluster do Serviço de Contêiner do Azure com o PowerShell. Este documento se baseia na versão 1.0 do [módulo do Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Para implantar um cluster DC/OS ou Docker Swarm ou Kubernetes, selecione um dos modelos a seguir. Observe que os modelos são iguais, com a exceção da seleção do orquestrador padrão.

* [Modelo DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
* [Modelo Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

Antes de criar um cluster em sua assinatura do Azure, verifique se sua sessão do PowerShell foi conectada ao Azure. Você pode fazer isso com o comando `Get-AzureRMSubscription` :

```powershell
Get-AzureRmSubscription
```

Se precisar entrar no Azure, use o comando `Login-AzureRMAccount` :

```powershell
Login-AzureRmAccount
```

Se estiver implantando em um grupo de recursos novo, primeiro crie o grupo de recursos. Para criar um novo grupo de recursos, use o comando `New-AzureRmResourceGroup` e especifique um nome de grupo de recursos e uma região de destino:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Depois de criar um grupo de recursos, você poderá criar seu cluster com o comando a seguir. O URI do modelo desejado será especificado para o parâmetro `-TemplateUri` . Quando você executar esse comando, o PowerShell solicitará os valores de parâmetros de implantação.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fornecer parâmetros de modelo
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




<!--HONumber=Nov16_HO5-->


