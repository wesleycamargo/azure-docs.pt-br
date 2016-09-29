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
   ms.date="07/20/2016"
   ms.author="iainfou"/>

# Usando a extensão de VM do Docker para implantar seu ambiente

> [AZURE.NOTE] Se tiver alguns minutos, ajude-nos a melhorar a documentação das VMs do Linux do Azure respondendo a essa [pesquisa rápida](https://aka.ms/linuxdocsurvey) sobre suas experiências. Cada resposta nos ajuda a realizar seu trabalho.

O Docker é uma plataforma popular de geração de imagens e gerenciamento de contêineres que permite que você trabalhe rapidamente com contêineres no Linux (e também no Windows). Com o Azure, você tem a flexibilidade para implantar o Docker de algumas maneiras diferentes dependendo das suas necessidades:

- Para fazer rapidamente o protótipo de um aplicativo, você pode [usar o driver do Computador Docker do Azure](./virtual-machines-linux-docker-machine.md) para implantar hosts do Docker no Azure.
- A Extensão de VM do Docker para máquinas virtuais do Azure é usada para implantações baseadas em modelo. Essa abordagem pode se integrar a implantações de modelo do Azure Resource Manager e inclui todos os benefícios relacionados, como acesso baseado em função, diagnóstico e configuração após a implantação.
- A Extensão de VM do Docker também dá suporte ao Docker Compose. O Docker Compose usa um arquivo YAML declarativo para utilizar um aplicativo modelado por desenvolvedores em qualquer ambiente e gerar uma implantação consistente.
- Você também pode [implantar um cluster completo do Docker Swarm nos Serviços de Contêiner do Azure](../container-service/container-service-deployment.md) para implantações escalonáveis prontas para produção que aproveitam as ferramentas de gerenciamento e agendamento adicionais fornecidas pelo Swarm.

Este artigo se concentra no uso de modelos do Resource Manager para implantar a Extensão de VM do Docker em um ambiente personalizado e pronto para produção definido por você.

## Extensão de VM do Docker do Azure para implantações de modelo

A Extensão de VM do Docker do Azure instala e configura o daemon do Docker, o cliente do Docker e o Docker Compose em sua máquina virtual Linux. A extensão também é usada para definir e implantar aplicativos de contêiner usando o Docker Compose. Você tem controles adicionais que vão além de usar o Computador Docker ou criar o host do Docker por conta própria, tornando a extensão adequada para ambientes de desenvolvimento mais robustos.

Usando o Azure Resource Manager, você pode criar e implantar modelos que definem a estrutura inteira do seu ambiente. Os modelos permitem que você defina armazenamento, RBAC (Controle de Acesso Baseado em Função), diagnóstico, hosts do Docker etc. Você pode [ler mais sobre o Gerenciador de Recursos](../resource-group-overview.md) e os modelos para entender melhor alguns dos benefícios. Usando modelos do Resource Manager, você também poderá reproduzir as implantações conforme for necessário no futuro.

## Implantar um modelo com a Extensão de VM do Docker:

Vamos usar um modelo de início rápido existente para mostrar como implantar uma VM do Ubuntu que tem a Extensão de VM do Docker instalada. Você pode conferir o modelo aqui: [Simple deployment of an Ubuntu VM with Docker (Implantação simples de uma VM do Ubuntu com o Docker)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Você também precisará do [CLI do Azure mais recente](../xplat-cli-install.md) no modo de Gerenciador de Recursos (`azure config mode arm`).

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
Depois que a implantação for concluída, faça a conexão SSH com seu novo host do Docker usando o nome DNS que você forneceu durante a implantação. Vamos tentar executar um contêiner nginx:

```
sudo docker run -d -p 80:80 nginx
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

Examine o contêiner em execução no host usando `sudo docker ps`:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver seu contêiner em ação, abra um navegador da Web e digite o nome DNS especificado durante a implantação:

![Contêiner ngnix em execução](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Você poderá configurar a porta TCP do daemon do Docker, a segurança, ou implantar contêineres usando o Docker Compose. Confira o [a extensão da máquina virtual do Azure para o projeto de GitHub do Docker](https://github.com/Azure/azure-docker-extension/) para obter mais informações.

## Referência de modelo JSON da Extensão de VM do Docker

Este exemplo usou um modelo de início rápido. Para implantar a extensão de VM do Docker do Azure com seus próprios modelos do Resource Manager, acrescente o seguinte:

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

Você pode encontrar um passo a passo mais detalhado de como usar modelos do Gerenciador de Recursos lendo a [Visão geral do Azure Resource Manager](../resource-group-overview.md)

## Próximas etapas

Leia etapas mais detalhadas para as diferentes opções de implantação:

1. [Usar o computador Docker com o driver do Azure](./virtual-machines-linux-docker-machine.md)
2. [Usando a extensão de VM Docker da interface de linha de comando do Azure (CLI do Azure)](./virtual-machines-linux-classic-cli-use-docker.md)
3. [Introdução ao Docker e Compose para definir e executar um aplicativo de vários contêineres em uma máquina virtual do Azure](virtual-machines-linux-docker-compose-quickstart.md).
3. [Implantar um cluster do Serviço de Contêiner do Azure](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0914_2016-->