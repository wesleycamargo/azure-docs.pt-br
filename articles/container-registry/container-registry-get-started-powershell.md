---
title: Guia de início rápido – criar um registro particular do Docker no Azure com o PowerShell
description: Aprenda rapidamente a criar um registro de contêiner particular do Docker no Azure com o PowerShell.
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 282cd4bc9256fc483014b53626c02106d0de236a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Início Rápido: criar um Registro de Contêiner do Azure usando o PowerShell

O Registro de Contêiner do Azure é um serviço de registro de contêiner do Docker privado e gerenciado para gerar, armazenar e fornecer imagens de contêiner de Docker. Neste início rápido, você aprende a criar um registro de contêiner do Azure usando o PowerShell. Depois de criar o registro, você efetua push de uma imagem de contêiner para ele e implanta o contêiner do registro em Instâncias de Contêiner do Azure (ACI).

## <a name="prerequisites"></a>pré-requisitos

Este início rápido requer o módulo Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para determinar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

Você também deve ter o Docker instalado localmente. O docker fornece pacotes para sistemas [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Como o Azure Cloud Shell não inclui todos os componentes de Docker necessários (o daemon `dockerd`), você não pode usar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça logon em sua assinatura do Azure com o comando [Connect-AzureRmAccount][Connect-AzureRmAccount] e siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Depois que você estiver autenticado com o Azure, crie um grupo de recursos com [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Um grupo de recursos é um contêiner lógico no qual você implanta e gerenciar os recursos do Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar um registro de contêiner

Em seguida, crie um registro de contêiner em seu novo grupo de recursos com o comando [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. O exemplo a seguir cria um registro chamado "myContainerRegistry007". Substitua *myContainerRegistry007* no comando a seguir e execute-o para criar o registro:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>Fazer logon no registro

Antes de efetuar push e pull nas imagens de contêiner, você deverá fazer logon no seu registro. Use o comando [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] para primeiro obter as credenciais de administrador para o registro:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Em seguida, execute [docker login][docker-login] para fazer logon:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Um logon bem-sucedido retorna `Login Succeeded`:

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Efetuar push de imagem para registro

Agora que está conectado ao registro, você pode efetuar push de imagens de contêiner para ele. Para obter uma imagem, você pode efetuar push para o registro, efetuar pull da imagem [aci-helloworld][aci-helloworld-image] pública do Hub do Docker. A imagem [aci-helloworld][aci-helloworld-github] é um pequeno aplicativo Node.js que serve a uma página HTML estática que mostra o logotipo das Instâncias de Contêiner do Azure.

```powershell
docker pull microsoft/aci-helloworld
```

Conforme o pull da imagem é efetuado, a saída retornada é semelhante à seguinte:

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Para que você possa efetuar o push de uma imagem para o registro de contêiner do Azure, você deverá marcá-la com o nome de domínio totalmente qualificado (FQDN) do seu registro. O FQDN dos registros de contêiner do Azure estão no formato *\<registry-name\>.azurecr.io*.

Preencha uma variável com a marcação de imagem completa. Inclua o servidor de logon, o nome do repositório ("aci-helloworld") e a imagem de versão ("v1"):

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

Agora, marque a imagem com [docker tag][docker-tag]:

```powershell
docker tag microsoft/aci-helloworld $image
```

E, por fim, execute seu [docker push][docker-push] para o registro:

```powershell
docker push $image
```

Conforme o cliente do Docker efetua o push da imagem, a saída deverá ser semelhante a:

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Parabéns! Você efetuou o push da primeira imagem de contêiner no registro.

## <a name="deploy-image-to-aci"></a>Implantar imagem nas ACI

Com a imagem agora no registro, implante um contêiner diretamente nas Instâncias de Contêiner do Azure para vê-la em execução no Azure.

Primeiro, converta a credencial de registro em um *PSCredential*. O comando `New-AzureRmContainerGroup`, que você usa para criar a instância de contêiner, exige-a nesse formato.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Além disso, o rótulo de nome DNS do contêiner deve ser exclusivo dentro da região do Azure onde você o cria. Execute o comando a seguir para preencher uma variável com um nome gerado:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Por fim, execute [New-AzureRmContainerGroup][New-AzureRmContainerGroup] para implantar um contêiner a partir da imagem no registro com 1 núcleo de CPU e 1 GB de memória:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Você deve obter uma resposta inicial do Azure com detalhes sobre o contêiner, e seu estado é, inicialmente, "Pendente":

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Para monitorar seu status e determinar quando ele está em execução, execute o comando [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] algumas vezes. O contêiner deve levar menos de um minuto para iniciar.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Aqui, você pode ver que o ProvisioningState do contêiner é inicialmente *Criando* e depois muda para o estado *Êxito* quando ele está em execução:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Exibir aplicativo em execução

Depois que a implantação nas ACI tiver sido bem-sucedida e o contêiner estiver em execução, navegue até seu nome de domínio totalmente qualificado (FQDN) no navegador para ver o aplicativo em execução no Azure.

Obtenha o FQDN para o contêiner com [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

A saída do comando é o FQDN da sua instância de contêiner:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Com o FQDN em mãos, navegue até ele em seu navegador:

![Aplicativo Olá, Mundo no navegador][qs-psh-01-running-app]

Parabéns! Você tem um contêiner executando um aplicativo no Azure, implantado diretamente a partir de uma imagem de contêiner no registro de contêiner do Azure privado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de trabalhar com os recursos criados neste guia de início rápido, use o comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] para remover o grupo de recursos, o registro de contêiner e a instância de contêiner:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com a CLI do Azure e iniciou uma instância dele nas Instâncias de Contêiner do Azure. Continue com o tutorial das Instâncias de Contêiner do Azure a fim de ver as ACI com mais detalhes.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
