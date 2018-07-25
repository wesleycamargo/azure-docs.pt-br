---
title: Implantar o aplicativo de um registro privado à Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como implantar um aplicativo que usa um registro de contêiner privado para a Malha do Service Fabric usando a CLI do Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089479"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Implantar um aplicativo de Malha do Service Fabric de um registro de imagem de contêiner privado

Este artigo mostra como implantar um aplicativo que usa um registro de contêiner privado para a Malha do Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric 
Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir essa tarefa. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

### <a name="install-docker"></a>Instalar o Docker

Instale o Docker para dar suporte a aplicativos do Service Fabric em contêineres usados pela Malha do Service Fabric.

### <a name="windows-10"></a>Windows 10

Baixe e instale a versão mais recente do [Docker Community Edition para Windows][download-docker]. 

Durante a instalação, selecione **contêineres do Windows de uso em vez de contêineres do Linux** quando for solicitado. Você precisará, em seguida, sair e entrar novamente. Depois de entrar novamente, se você não ativou anteriormente o Hyper-V, você precisará habilitar o Hyper-V. Habilitar o Hyper-V e, em seguida, reinicie o computador.

Depois que o computador for reiniciado, Docker solicitará que você habilite o recurso de **contêineres**, habilite-o e reinicie o computador.

### <a name="windows-server-2016"></a>Windows Server 2016

Use os seguintes comandos do PowerShell para instalar Docker. Para saber mais, confira a [Edição Docker Enterprise para Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Reiniciar seu computador.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Fazer logon no Azure e definir a assinatura do Azure:

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Criar um registro de contêiner e enviar uma imagem para ele

Criar um registro de contêiner do Azure seguindo as instruções em [criar um registro de Docker privado no Azure com a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md). Execute as etapas até a etapa [fazer logon no ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr). 

### <a name="push-image-to-acr"></a>Enviar imagem por push para o ACR

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se você ainda não tiver as imagens de contêiner locais, execute o comando a seguir para efetuar pull de uma imagem existente de Hub do Docker.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Antes de poder enviar uma imagem por push no registro, você deve marcá-lo com o nome totalmente qualificado do seu servidor de logon ACR. Execute o comando a seguir para obter o nome completo do servidor de logon da instância do ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Marque a imagem usando o comando [docker tag][docker-tag]. Substitua o `<acrLoginServer>` pelo nome do servidor de logon da sua instância do ACR.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Listar imagens de contêiner

O exemplo a seguir lista os repositórios em um registro:

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```bash
Result
----------------
azure-mesh-helloworld
```

O exemplo a seguir lista as marcas no repositório **azure-mesh-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Saída:

```bash
Result
--------
1.1-alpine
```
A saída anterior confirma a presença de `azure-mesh-helloworld:1.1-alpine` no registro de contêiner privado.

## <a name="retrieve-credentials-for-the-registry"></a>Recuperar credenciais para o registro

Para implantar uma instância de contêiner do registro que foi criado, as credenciais devem ser fornecidas durante a implantação. Permitir o usuário administrador no seu registro com o seguinte comando:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Obtenha o nome do servidor de registro, o nome de usuário e a senha usando os comandos a seguir:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Os valores fornecidos, precedendo comandos são referenciados como `<acrLoginServer>`, `<acrUserName>`, e `<acrPassword>` no comando a seguir.


## <a name="deploy-the-template"></a>Implantar o modelo

Criar o aplicativo e os recursos relacionados usando o comando a seguir e forneça as credenciais da etapa anterior.

O parâmetro `registry-password` no modelo é um `securestring`. Ele não será exibido no status de implantação e comandos `az mesh service show`. Certifique-se de que ele está corretamente especificado no comando a seguir.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

Em alguns minutos, seu comando deve retornar com:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Abra o aplicativo
Depois que o aplicativo é implantado com êxito, obtenha o endereço IP público para o ponto de extremidade de serviço e abra-o em um navegador. Uma página da Web com o logotipo da Malha do Azure Service Fabric é exibida.

O comando de implantação retorna o endereço IP público do ponto de extremidade de serviço. Opcionalmente, você também pode consultar o recurso de rede para localizar o endereço IP público do ponto de extremidade de serviço. 
 
O nome do recurso de rede para este aplicativo é `helloWorldPrivateRegistryNetwork`, buscar informações sobre ele usando o comando a seguir. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Excluir os recursos

Para conservar os recursos limitados atribuídos no programa de visualização, exclua os recursos com frequência. Para excluir os recursos relacionados a este exemplo, exclua o grupo de recursos no qual eles foram implantados.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Próximas etapas
- Exiba o aplicativo de exemplo de Olá, Mundo no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/