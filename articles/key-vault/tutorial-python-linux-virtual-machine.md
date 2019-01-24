---
title: Tutorial – Usar o Azure Key Vault com uma máquina virtual do Azure no Python | Microsoft Docs
description: Neste tutorial, você configurará um aplicativo do Python para que ele leia um segredo de um cofre de chaves
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 1e364003093d5e37a75830386cafe855b0bdcad2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467394"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Tutorial: Usar o Azure Key Vault com uma máquina virtual do Azure no Python

O Azure Key Vault ajuda a proteger segredos, como as chaves de API e as cadeias de conexão de banco de dados necessárias para acessar aplicativos, serviços e recursos de TI.

Neste tutorial, siga as etapas para fazer um aplicativo Web do Azure ler informações do Azure Key Vault usando identidades gerenciadas de recursos do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Crie um cofre da chave.
> * Armazenar um segredo no cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Habilite uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual.
> * Conceder as permissões necessárias para o aplicativo de console ler dados do cofre de chaves.
> * Recuperar um segredo do cofre de chaves.

Antes de prosseguir, leia os [conceitos básicos sobre o Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos
Para todas as plataformas, você precisará:

* Git ([download](https://git-scm.com/downloads)).
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão 2.0.4 ou posterior. Disponível para Windows, Mac e Linux.

### <a name="managed-service-identity-and-how-it-works"></a>Identidade de Serviço Gerenciada e como ela funciona
Este tutorial faz uso da MSI (Identidade de Serviço Gerenciada).

O Azure Key Vault pode armazenar credenciais com segurança para que elas não fiquem no código. Para recuperá-las, você precisará se autenticar no Key Vault. Para fazer a autenticação no Key Vault, você precisa de uma credencial. Esse é um problema clássico de inicialização. Por meio do Azure e do Azure AD (Azure Active Directory), a MSI fornece uma “identidade de inicialização” que simplifica tudo.

Quando você habilita a MSI para um serviço do Azure como Máquinas Virtuais, Serviço de Aplicativo ou Functions, o Azure cria uma [entidade de serviço](key-vault-whatis.md#basic-concepts) para a instância do serviço no Azure AD. O Azure injeta as credenciais da entidade de serviço na instância do serviço. 

![MSI](media/MSI.png)

Em seguida, o código chama um serviço de metadados local disponível no recurso do Azure para obter um token de acesso.
O código usa o token de acesso obtido do ponto de extremidade da MSI local para se autenticar em um serviço do Azure Key Vault. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Selecione um nome para o grupo de recursos e preencha o espaço reservado.
O exemplo a seguir cria um grupo de recursos no local Oeste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Em seguida, você cria um cofre de chaves no grupo de recursos criado na etapa anterior. Forneça as seguintes informações:

* Nome do cofre de chaves: O nome precisa ser uma cadeia de 3 a 24 caracteres e conter apenas 0-9, a-z, A-Z e hifens (-).
* Nome do grupo de recursos.
* Localização: **Oeste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para o aplicativo.

Digite os comandos a seguir para criar um segredo no cofre de chaves chamado *AppSecret*. Esse segredo armazenará o valor **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm).

O exemplo a seguir cria uma VM chamada *myVM* e adiciona uma conta de usuário chamada *azureuser*. O parâmetro `--generate-ssh-keys` gera automaticamente uma chave SSH e coloca-a na localização de chave padrão (*~/.ssh*). Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos. A seguinte saída de exemplo mostra que a criação de VM foi bem-sucedida:

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Anote o seu próprio valor `publicIpAddress` na saída da VM. Este endereço é usado para acessar a VM na próxima etapa.

## <a name="assign-an-identity-to-the-virtual-machine"></a>Atribuir uma identidade à máquina virtual
Nesta etapa, criaremos uma identidade atribuída pelo sistema para a máquina virtual. Execute o seguinte comando na CLI do Azure:

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A saída do comando é mostrada a seguir. Anote o valor de **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Conceder a permissão de identidade da máquina virtual ao cofre de chaves
Agora podemos conceder a permissão de identidade ao cofre de chaves. Execute o comando a seguir:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Fazer logon na máquina virtual

Faça logon na máquina virtual seguindo [este tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Criar e executar o aplicativo de exemplo do Python

O arquivo de exemplo a seguir é chamado *Sample.py*. Ele usa a biblioteca [requests](https://pypi.org/project/requests/2.7.0/) para fazer chamadas HTTP GET.

## <a name="edit-samplepy"></a>Editar Sample.py
Depois de criar Sample.py, abra o arquivo e copie o código a seguir. O código é um processo de duas etapas: 
1. Efetue fetch de um token do ponto de extremidade da MSI local na VM. Em seguida, o ponto de extremidade efetuará fetch de um token do Azure Active Directory.
2. Passe o token para o cofre de chaves e efetue fetch do segredo. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from an MSI-enabled Azure resource      
    # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # Extracting data in JSON format 
    # This request gets an access token from Azure Active Directory by using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access token received from the previous HTTP GET call to the key vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Executando o seguinte comando, você deverá ver o valor do segredo: 

```
python Sample.py
```

O código anterior mostra como realizar operações com o Azure Key Vault em uma máquina virtual do Windows. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
