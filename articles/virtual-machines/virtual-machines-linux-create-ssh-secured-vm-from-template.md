<properties
	pageTitle="Criar uma VM Linux segura usando um modelo do Azure | Microsoft Azure"
	description="Crie uma VM Linux segura no Azure usando um modelo do Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# Criar uma VM Linux protegida usando um modelo do Azure

Os modelos permitem criar VMs no Azure com as configurações que você deseja personalizar durante a inicialização, como nomes de usuário e nomes de host. Para este artigo, nos concentraremos em Iniciar uma VM do Ubuntu usando um modelo do Azure que cria um grupo de segurança de rede (NSG) com apenas uma porta aberta (22 para SSH) e que requer chaves SSH para o logon.

Os modelos do Azure Resource Manager são arquivos JSON que podem ser usados para tarefas simples e únicas – como iniciar uma VM Ubuntu, como feito neste artigo – ou para construir as configurações do Azure complexas de ambientes inteiros como um teste, implantação de produção ou desenvolvimento de rede para o sistema operacional para implantação de pilha do aplicativo.

## Objetivo

- Criar uma VM Linux protegida por SSH no Azure usando um modelo do Azure

## Pré-requisitos

- Uma conta do Azure ([obtenha uma avaliação gratuita!](https://azure.microsoft.com/pricing/free-trial/)), um [o arquivo de chave pública SSH](virtual-machines-linux-mac-create-ssh-keys.md), a [CLI do Azure](../xplat-cli-install.md) e colocar a CLI no modo de recurso ao digitar `azure config mode arm`. Em seguida, faça logon no Azure com a CLI digitando `azure login`.

## Resumo rápido do comando

Essa implantação exige apenas um comando, junto com a seleção de uma opção que indique onde reside o modelo do Azure Resource Manager. Este tópico usa um modelo diretamente do repositório Github de modelo de início rápido do Azure como um exemplo. A segui, as diversas opções indicam meramente onde o modelo – e todos os arquivos de parâmetros-- estão localizados.

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## Criar a VM Linux

O exemplo de código a seguir mostra como chamar o `azure group create` para criar um grupo de recursos e implantar uma VM do Linux protegida por SSH ao mesmo tempo usando [este modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que, em seu exemplo, você precisará usar nomes que sejam exclusivos para seu ambiente. Este exemplo usa `quicksecuretemplate` como o nome do grupo de recursos, `securelinux` como o nome da VM e `quicksecurelinux` como um nome de subdomínio.

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Passo a passo detalhado

Você pode criar um novo grupo de recursos e implantar uma VM usando o parâmetro `--template-uri` ou pode baixar ou criar um modelo localmente e passar o modelo usando o parâmetro `--template-file` com um caminho para o arquivo de modelo como um argumento. A CLI do Azure solicita os parâmetros necessários ao modelo.

## Próximas etapas

Depois de criar VMs Linux com modelos, você desejará ver que outras estruturas de aplicativos estão disponíveis para uso com modelos. Pesquise a [galeria de modelos](https://azure.microsoft.com/documentation/templates/) para descobrir quais estruturas de aplicativos implantar em seguida.

<!---HONumber=AcomDC_0406_2016-->