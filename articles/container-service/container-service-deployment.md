<properties
   pageTitle="Implantar um cluster do Serviço de Contêiner do Azure | Microsoft Azure"
   description="Implante um cluster do Serviço de Contêiner do Azure usando o Portal do Azure, a CLI do Azure ou o PowerShell."
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

O Serviço de Contêiner do Azure fornece implantação rápida de soluções populares de orquestração e clustering de contêiner de software livre. Usando o Serviço de Contêiner do Azure, clusters Marathon Mesos e Docker Swarm podem ser implantados com modelos do Gerenciador de Recursos do Azure ou por meio do portal. Esses clusters são implantados usando Conjuntos de Escala de Máquina Virtual do Azure e tiram proveito das ofertas de rede e armazenamento do Azure. Para acessar o Serviço de Contêiner do Azure, você precisará de uma assinatura do Azure. Se não tiver uma, inscreva-se para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento explica a implantação e o cluster do Serviço de Contêiner do Azure usando o [Portal do Azure](#creating-a-service-using-the-azure-portal), a [CLI do Azure](#creating-a-service-using-the-azure-cli) e o [módulo Azure PowerShell](#creating-a-service-using-powershell).
   
## Como criar um serviço usando o Portal do Azure
 
Selecione um dos modelos a seguir para implantar um cluster Mesos ou Docker Swarm. **Observação** - ambos os modelos são iguais, exceto pela seleção do orquestrador padrão.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
Todas as páginas do modelo têm um botão 'implantar no Azure'. Se você clicar nesse botão, iniciará o portal do Azure com um formulário semelhante a este. <br />

![Criar implantação](media/create-mesos-params.png) <br />

Preencha o formulário usando esta orientação e selecione OK quando terminar. <br />

Campo | Descrição
----------------|-----------
DNSNAMEPREFIX | Esse deve ser um valor exclusivo no mundo. Ele será usado para criar nomes DNS para cada uma das partes principais do serviço. Há mais informações a seguir.
AGENTCOUNT | Esse é o número de máquinas virtuais que serão criadas no conjunto de escala do agente de ACS.
AGENTVMSIZE | Especifica o tamanho das máquinas virtuais do agente. Tenha cuidado para selecionar um tamanho que forneça recursos suficientes para hospedar os maiores contêineres.
ADMINUSERNAME | Esse é o nome de usuário que será usado para uma conta em cada uma das máquinas virtuais e conjuntos de escala de máquina virtual no cluster do ACS.
ORCHESTRATORTYPE| Selecione o orquestrador que você deseja usar no cluster do ACS.
MASTERCOUNT | Esse é o número de Máquinas Virtuais a serem configuradas como mestres para o cluster. Você pode selecionar uma, mas isso não fornecerá resiliência no cluster e é recomendado apenas para fins de teste. O número recomendado para um cluster de produção seria três ou cinco. 
SSHRSAPUBLICKEY | É necessário que você use o SSH para autenticação em relação às Máquinas Virtuais. Este é o local onde você adiciona a chave pública. É muito importante tomar cuidado ao colar o valor da chave nessa caixa. Alguns editores inserem quebras de linha no conteúdo; isso invalidará a chave. Verifique se a chave não tem quebras de linha e se inclui o prefixo 'ssh-rsa' e o sufixo 'username@domain'. Ele deve ser semelhante a 'ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'. Se precisar criar uma chave SSH, você encontrará orientação para o [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) e o [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md) no site de documentação do Azure.
  
Após definir os valores apropriados para os parâmetros, clique em OK. Em seguida, forneça um nome de grupo de recursos, selecione uma região e examine e concorde com os termos legais.

> Durante a visualização, não há uma taxa para o Serviço de Contêiner do Azure, apenas encargos de computação padrão, como máquinas virtuais, armazenamento, rede etc.
 
![Selecionar o grupo de recursos](media/resourcegroup.png)
 
Finalmente, clique em "Criar". Você retornará ao painel e, supondo que não tenha desmarcado "Fixar no painel" na folha de implantação, verá um bloco animado semelhante a:

![implantando](media/deploy.png)
 
Agora, relaxe enquanto o cluster é criado. Uma vez concluído, você verá algumas folhas que mostram os recursos que fazem parte do cluster do ACS.
 
![Concluído](media/final.png)

## Criar um serviço usando a CLI do Azure

Para criar uma instância do Serviço de Contêiner do Azure usando a linha de comando, você precisará de uma assinatura do Azure. Se não tiver uma, você poderá se inscrever para obter uma avaliação gratuita. Você também precisará ter instalado e configurado a CLI do Azure.
 
Selecione um dos modelos a seguir para implantar um cluster Mesos ou Docker Swarm. **Observação** - ambos os modelos são iguais, exceto pela seleção do orquestrador padrão.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
Em seguida, verifique se a CLI do Azure foi conectada a uma assinatura do Azure. Isso pode ser feito usando o comando a seguir.

```bash
Azure account show
```
Se uma conta do Azure não for retornada, use o item a seguir para fazer logon da CLI do Azure.
 
```bash
azure login -u user@domain.com
```

Em seguida, configure as ferramentas da CLI do Azure para usar o Gerenciador de Recursos do Azure.
 
```bash
azure config mode arm
```
 
Se desejar criar o cluster em um novo Grupo de Recursos, primeiro crie o Grupo de Recursos. Use este comando, em que `GROUP_NAME` é o nome do grupo de recursos que você deseja criar e `REGION` é a região onde deseja criá-lo.
 
```bash
azure group create GROUP_NAME REGION
```
 
Depois que tiver um Grupo de Recursos, você poderá criar o cluster com este comando, em que:

- **RESOURCE\_GROUP** é o nome do Grupo de Recursos que você deseja usar para esse serviço.
- **DEPLOYMENT\_NAME** é o nome da implantação.
- **TEMPLATE\_URI** é o local do arquivo de implantação. **Observação** - esse deve ser o arquivo BRUTO, não um ponteiro para a interface do usuário do GitHub. Para localizar essa URL, selecione o arquivo azuredeploy.json no GitHub e clique no botão BRUTO:

> Observação - quando você executar esse comando, o shell solicitará valores de parâmetros de implantação.
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### Fornecer parâmetros de modelo
 
Esta versão do comando requer que o usuário defina os parâmetros interativamente. Se você quiser fornecer parâmetros como uma cadeia de caracteres json formatada, poderá fazer isso com a opção `-p`. Por exemplo:
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Como alternativa, você pode fornecer um arquivo de parâmetros formatado json usando a opção `-e`:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
Um arquivo de parâmetros de exemplo chamado `azuredeploy.parameters.json` pode ser encontrado com os modelos do ACS no GitHub.
 
## Criar um serviço usando o PowerShell

Um cluster do ACS também pode ser implantado com o PowerShell. Este documento é baseado na versão 1.0 e posterior do [módulo PowerShell](https://azure.microsoft.com/blog/azps-1-0/) do Azure.

Selecione um dos modelos a seguir para implantar um cluster Mesos ou Docker Swarm. **Observação** - ambos os modelos são iguais, exceto pela seleção do orquestrador padrão.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de criar um cluster em sua assinatura do Azure, verifique se sua sessão do PowerShell foi conectada no Azure. Isso pode ser feito com o comando `Get-AzureRMSubscription`.

```powershell
Get-AzureRmSubscription
```

Se precisar fazer logon no Azure, use o comando `Login-AzureRMAccount`.

```powershell
Login-AzureRmAccount
```
 
Se estiver implantando em um novo Grupo de Recursos, primeiro crie o Grupo de Recursos. Para criar um novo Grupo de Recursos, use o comando `New-AzureRmResourceGroup`, especificando um nome de grupo de recursos e uma região de destino.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Quando tiver um Grupo de Recursos, você poderá criar o cluster com o comando a seguir. O URI do modelo desejado será especificado para o parâmetro `-TemplateUri`. Quando você executar esse comando, o PowerShell solicitará valores de parâmetros de implantação.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### Provide template parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses the parameters, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## Próximas etapas
 
Agora que você tem um cluster em funcionamento, acesse estes documentos para obter detalhes sobre conexão e gerenciamento.
 
- [Conectar-se com o cluster do ACS](./container-service-connect.md)
- [Como trabalhar com ACS e Mesos](./container-service-mesos-marathon-rest.md)

 

<!---HONumber=AcomDC_0224_2016-->