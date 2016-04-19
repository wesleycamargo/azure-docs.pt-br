<properties
   pageTitle="Implantar um cluster do Serviço de Contêiner do Azure | Microsoft Azure"
   description="Implante um cluster do Serviço de Contêiner do Azure usando o portal do Azure, a CLI do Azure ou o PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contêineres, Microsserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Implantar um cluster do Serviço de Contêiner do Azure

O Serviço de Contêiner do Azure fornece implantação rápida de soluções populares de orquestração e clustering de contêiner de software livre. Ao usar o Serviço de Contêiner do Azure, você pode implantar clusters Marathon Mesos e Docker Swarm com modelos do Azure Resource Manager ou por meio do portal do Azure. Você implanta esses clusters usando Conjuntos de Escala de Máquina Virtual do Azure e tiram proveito das ofertas de rede e armazenamento do Azure. Para acessar o Serviço de Contêiner do Azure, você precisa de uma assinatura do Azure. Se não tiver uma, você poderá se inscrever para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento orienta você durante a implantação de um cluster do Serviço de Contêiner do Azure usando o [Portal do Azure](#creating-a-service-using-the-azure-portal), a [interface de linha de comando (CLI) do Azure](#creating-a-service-using-the-azure-cli) e o [módulo do Azure PowerShell](#creating-a-service-using-powershell).

## Criar um serviço usando o portal do Azure

Para implantar um cluster Mesos ou Docker Swarm, selecione um dos modelos a seguir no GitHub. Observe que os modelos são iguais, com a exceção da seleção do orquestrador padrão.

* [Modelo do Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Quando você seleciona o botão **Implantar no Azure** ou em uma das páginas de modelo, o portal do Azure abre com um formulário semelhante a este: <br />

![Criar a implantação usando um formulário](media/create-mesos-params.png) <br />

Preencha o formulário usando esta orientação e selecione **OK** quando terminar. <br />

Campo | Descrição
----------------|-----------
DNSNAMEPREFIX | Esse deve ser um valor exclusivo no mundo. Ele será usado para criar nomes DNS para cada uma das partes principais do serviço. Veja mais informações posteriormente neste artigo.
AGENTCOUNT | Esse é o número de máquinas virtuais que serão criadas no conjunto de escala de agente do Serviço de Contêiner do Azure.
AGENTVMSIZE | É o tamanho das máquinas virtuais do agente. Tenha cuidado para selecionar um tamanho que forneça recursos suficientes para hospedar os maiores contêineres.
ADMINUSERNAME | É o nome de usuário que será usado para uma conta em cada uma das máquinas virtuais e conjuntos de escala de máquina virtual no cluster do Serviço de Contêiner do Azure.
ORCHESTRATORTYPE| É o tipo de orquestrador a ser usado em seu cluster do Serviço de Contêiner do Azure.
MASTERCOUNT | É o número de máquinas virtuais a serem configuradas como mestres para o cluster. Você pode selecionar uma, mas isso não fornecerá resiliência no cluster e é recomendado apenas para fins de teste. É recomendável usar três ou cinco para um cluster de produção.
SSHRSAPUBLICKEY | Você deve usar o SSH (Secure Shell) para autenticação nas máquinas virtuais. Este é o local onde você adiciona a chave pública. É muito importante tomar cuidado ao colar o valor da chave nessa caixa. Alguns editores inserem quebras de linha no conteúdo e isso invalidará a chave. Verifique se a chave não tem quebras de linha e se inclui o prefixo 'ssh-rsa' e o sufixo 'nomedeusuário@domínio'. Ele deve ser semelhante a 'ssh-rsa AAAAB3Nz...CONTEÚDODOTRECHO...UcyupgH azureuser@linuxvm'. Se precisar criar uma chave SSH, você encontrará diretrizes para o [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) e o [Linux](../virtual-machines/virtual-machines-linux-ssh-from-linux.md) no site de documentação do Azure.

Após definir os valores apropriados para os parâmetros, selecione **OK**. Em seguida, forneça um nome de grupo de recursos, selecione uma região e examine e concorde com os termos legais.

> [AZURE.NOTE] Durante a visualização, não há encargos para o Serviço de Contêiner do Azure. Há apenas encargos de computação padrão, como de máquinas virtuais, de armazenamento e de rede.

![Selecionar o grupo de recursos](media/resourcegroup.png)

Por fim, selecione **Criar**. Volte para o painel. Supondo que você não tenha desmarcado a caixa de seleção **Fixar no painel** na folha implantação, você verá um bloco animado semelhante a este:

![Implantação do bloco de modelo](media/deploy.png)

Agora, relaxe enquanto o cluster é criado. Após a criação do cluster, você verá algumas folhas que mostram os recursos que fazem parte do cluster do Serviço de Contêiner do Azure.

![Concluído](media/final.png)

## Criar um serviço usando a CLI do Azure

Para criar uma instância do Serviço de Contêiner do Azure usando a linha de comando, você precisará de uma assinatura do Azure. Se não tiver uma, você poderá se inscrever para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Você também precisará ter instalado e configurado a CLI do Azure.

Para implantar um cluster Mesos ou Docker Swarm, selecione um dos modelos a seguir no GitHub. Observe que os modelos são iguais, com a exceção da seleção do orquestrador padrão.

* [Modelo do Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Em seguida, verifique se a CLI do Azure foi conectada a uma assinatura do Azure. Faça isso usando este comando:

```bash
Azure account show
```
Se uma conta do Azure não for retornada, use o comando a seguir para fazer logon da CLI do Azure.

```bash
azure login -u user@domain.com
```

Em seguida, configure as ferramentas da CLI do Azure para usar o Azure Resource Manager.

```bash
azure config mode arm
```

Se desejar criar o cluster em um novo grupo de recursos, primeiro crie o grupo de recursos. Use o comando a seguir, em que `GROUP_NAME` é o nome do grupo de recursos que você deseja criar e `REGION` é a região onde deseja criar o grupo de recursos:

```bash
azure group create GROUP_NAME REGION
```

Depois de ter criado um grupo de recursos, você poderá criar seu cluster com este comando, em que:

- **RESOURCE\_GROUP** é o nome do grupo de recursos que você deseja usar para esse serviço.
- **DEPLOYMENT\_NAME** é o nome da implantação.
- **TEMPLATE\_URI** é o local do arquivo de implantação. Observe que este deve ser o arquivo BRUTO, *não* um ponteiro para a interface do usuário do GitHub. Para localizar essa URL, selecione o arquivo azuredeploy.json no GitHub e selecione o botão **BRUTO**.

> [AZURE.NOTE] Quando você executar esse comando, o shell solicitará valores de parâmetros de implantação.

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```

### Fornecer parâmetros de modelo

Esta versão do comando requer que você defina os parâmetros interativamente. Se você quiser fornecer parâmetros, como uma cadeia de caracteres formatada por JSON, poderá fazer isso com a opção `-p`. Por exemplo:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Como alternativa, você pode fornecer um arquivo de parâmetros formatado por JSON usando a opção `-e`:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Para ver um arquivo de parâmetros de exemplo chamado `azuredeploy.parameters.json`, procure-o com os modelos do Serviço de Contêiner do Azure no GitHub.

## Criar um serviço usando o PowerShell

Você também pode implantar um cluster do Serviço de Contêiner do Azure com o PowerShell. Este documento se baseia na versão 1.0 do [módulo do Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Para implantar um cluster Mesos ou Docker Swarm, selecione um dos modelos a seguir. Observe que os modelos são iguais, com a exceção da seleção do orquestrador padrão.

* [Modelo do Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de criar um cluster em sua assinatura do Azure, verifique se sua sessão do PowerShell foi conectada ao Azure. Faça isso com o parâmetro `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Se precisar entrar no Azure, use o comando `Login-AzureRMAccount`:

```powershell
Login-AzureRmAccount
```

Se estiver implantando em um grupo de recursos novo, primeiro crie o grupo de recursos. Para criar um novo grupo de recursos, use o comando `New-AzureRmResourceGroup`, especificando um nome de grupo de recursos e uma região de destino:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Depois de criar um grupo de recursos, você poderá criar seu cluster com o comando a seguir. O URI do modelo desejado será especificado para o parâmetro `-TemplateUri`. Quando você executar esse comando, o PowerShell solicitará os valores de parâmetros de implantação.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### Fornecer parâmetros de modelo

Se estiver familiarizado com o PowerShell, você saberá que pode percorrer os parâmetros disponíveis para um cmdlet digitando um sinal de subtração (-) e, em seguida, pressionando a tecla TAB. Essa mesma funcionalidade também funciona com os parâmetros definidos no modelo. Assim que você digitar o nome do modelo, o cmdlet buscará o modelo, o analisará os parâmetros e adicionará dinamicamente os parâmetros do modelo ao comando. Isso facilita muito a especificação dos valores de parâmetros do modelo. E, se você esquecer um valor de parâmetro necessário, o PowerShell solicitará o valor.

A seguir, o comando completo com os parâmetros incluídos. Você pode fornecer seus próprios valores para os nomes dos recursos.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Próximas etapas

Agora que você tem um cluster em funcionamento, veja estes artigos para obter detalhes sobre conexão e gerenciamento.

- [Conectar a um cluster do Serviço de Contêiner do Azure](./container-service-connect.md)
- [Trabalhar com o Mesos e o Serviço de Contêiner do Azure](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0413_2016-->