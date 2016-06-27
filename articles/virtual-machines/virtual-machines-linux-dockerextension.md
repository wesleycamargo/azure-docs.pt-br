<properties
   pageTitle="Saiba mais sobre a Extensão de VM do Docker no Azure | Microsoft Azure"
   description="Saiba como usar a Extensão de VM do Docker para implantar de maneira rápida e segura um ambiente Docker no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/04/2016"
   ms.author="iainfou"/>

# Usando a extensão de VM do Docker para implantar seu ambiente

O Docker é uma plataforma popular de geração de imagens e gerenciamento de contêineres que permite que você trabalhe rapidamente com contêineres no Linux (e também no Windows). Com o Azure, você tem a flexibilidade para implantar o Docker de algumas maneiras diferentes dependendo das suas necessidades:

- Para fazer rapidamente um protótipo de um aplicativo ou se você já conhece e usa o Docker Machine, você pode [usar o driver do Azure do Docker Machine](./virtual-machines-linux-docker-machine.md) para implantar hosts do Docker no Azure.
- Para uma implantação baseada em modelo, a extensão de VM do Docker para máquinas virtuais do Azure pode ser usada. Essa abordagem pode se integrar a implantações de modelo do Azure Resource Manager e inclui todos os benefícios relacionados, como acesso baseado em função, diagnóstico e configuração após a implantação.
- A extensão de VM do Docker também oferece suporte ao Docker Compose, que usa um arquivo YAML declarativo para utilizar um aplicativo modelado por desenvolvedores em qualquer ambiente e gerar uma implantação consistente.  
- Você também pode [implantar um cluster completo do Docker Swarm nos Serviços de Contêiner do Azure](../container-service/container-service-deployment.md) para implantações escalonáveis prontas para produção que aproveitam as ferramentas de gerenciamento e agendamento adicionais fornecidas pelo Swarm.

Este artigo se concentra no uso de modelos do Resource Manager para implantar a Extensão de VM do Docker em um ambiente personalizado e pronto para produção definido por você.

## Extensão de VM do Docker do Azure para implantações de modelo

A Extensão de VM do Docker do Azure instala e configura o daemon do Docker, o cliente do Docker e o Docker Compose em sua máquina virtual Linux. A extensão também pode ser usada para definir e implantar aplicativos de contêiner usando o Docker Compose. Usando modelos do Resource Manager, o ambiente pode, então, ser implantado novamente de forma consistente. O uso da Extensão de VM do Docker do Azure é adequado para ambientes de produção ou desenvolvimento mais robustos, pois você tem alguns controles adicionais que vão além de simplesmente usar o Docker Machine ou criar o host do Docker por conta própria.

Usando o Azure Resource Manager, você pode criar e implantar modelos que definem toda a estrutura do seu ambiente, como o armazenamento, o RBAC (Controle de acesso baseado em função), o diagnóstico, os hosts do Docker etc. Você pode [ler mais sobre o Gerenciador de Recursos](../resource-group-overview.md) e os modelos para entender melhor alguns dos benefícios. A vantagem de usar modelos do Resource Manager em vez de simplesmente usar o Docker Machine é que você pode definir hosts do Docker adicionais, armazenamento, controles de acesso etc. e pode reproduzir as implantações conforme necessário no futuro.

## Implantar um modelo com a Extensão de VM do Docker:

Vamos usar um modelo de início rápido existente para mostrar como implantar uma VM do Ubuntu que tem a Extensão de VM do Docker instalada. Você pode ver o modelo aqui: [Simple deployment of an Ubuntu VM with Docker (Implantação simples de uma VM do Ubuntu com Docker)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)

Implante o modelo usando a CLI do Azure, especificando um nome para o novo grupo de recursos (veja `myDockerResourceGroup`) em conjunto com o URI do modelo:

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda aos prompts para fornecer sua conta de armazenamento, o nome DNS, o nome de usuário etc. e, em seguida, aguarde alguns minutos para concluir a implantação. Você deverá ver uma saída semelhante ao seguinte:

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## Implantar seu primeiro contêiner nginx

Depois que a implantação for concluída, faça a conexão SSH com seu novo host do Docker usando o nome DNS que você forneceu durante a implantação. As ferramentas do Docker já estarão instaladas, então vamos tentar executar um contêiner nginx:

```
docker run -d -p 80:80 nginx
```

Você deverá ver uma saída semelhante ao seguinte:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Examine o contêiner em execução no seu host usando `docker ps`:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   nostalgic_murdock
```

Abra um navegador da Web e digite o nome DNS especificado durante a implantação para ver o contêiner em ação:

![Contêiner ngnix em execução](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Para obter informações adicionais sobre a extensão de VM do Docker, como configurar a porta TCP do daemon do Docker, configurar a segurança e implantar contêineres usando o Docker Compose, consulte a [Extensão da máquina virtual do Azure para o projeto do GitHub do Docker](https://github.com/Azure/azure-docker-extension/).

## Referência de modelo JSON da Extensão de VM do Docker

Este exemplo usou um modelo de início rápido. Você pode usar seus próprios modelos do Resource Manager existentes para instalar a Extensão de VM do Docker em VMs definidas no seu modelo, adicionando o seguinte ao seu arquivo de definição de JSON:

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Você pode encontrar um passo a passo mais detalhado de como usar modelos do Resource Manager lendo a [Visão geral do Azure Resource Manager](../resource-group-overview.md)

## Próximas etapas

Leia etapas mais detalhadas para as diferentes opções de implantação:

1. [Usar o computador Docker com o driver do Azure](./virtual-machines-linux-docker-machine.md)  
2. [Usando a extensão de VM Docker da interface de linha de comando do Azure (CLI do Azure)](./virtual-machines-linux-classic-cli-use-docker.md)  
3. [Introdução ao Docker e Redigir para definir e executar um aplicativo de contêiner múltiplos em uma máquina virtual do Azure](virtual-machines-linux-docker-compose-quickstart.md).
3. [Implantar um cluster do Serviço de Contêiner do Azure](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0615_2016-->