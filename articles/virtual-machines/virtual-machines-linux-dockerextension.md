---
title: Saiba mais sobre a Extensão de VM do Docker no Azure | Microsoft Docs
description: Saiba como usar a Extensão de VM do Docker para implantar de maneira rápida e segura um ambiente Docker no Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/10/2016
ms.author: iainfou

---
# <a name="using-the-docker-vm-extension-to-deploy-your-environment"></a>Usando a extensão de VM do Docker para implantar seu ambiente
O Docker é uma plataforma popular de geração de imagens e gerenciamento de contêineres que permite que você trabalhe rapidamente com contêineres no Linux (e também no Windows). Com o Azure, você tem a flexibilidade para implantar o Docker de algumas maneiras diferentes dependendo das suas necessidades:

* Para fazer rapidamente o protótipo de um aplicativo, você pode [usar o driver do Computador Docker do Azure](virtual-machines-linux-docker-machine.md) para implantar hosts do Docker no Azure.
* A Extensão de VM do Docker para máquinas virtuais do Azure é usada para implantações baseadas em modelo. Essa abordagem pode se integrar a implantações de modelo do Azure Resource Manager e inclui todos os benefícios relacionados, como acesso baseado em função, diagnóstico e configuração após a implantação.
* A Extensão de VM do Docker também dá suporte ao Docker Compose. O Docker Compose usa um arquivo YAML declarativo para utilizar um aplicativo modelado por desenvolvedores em qualquer ambiente e gerar uma implantação consistente.
* Você também pode [implantar um cluster completo do Docker Swarm nos Serviços de Contêiner do Azure](../container-service/container-service-deployment.md) para implantações escalonáveis prontas para produção que aproveitam as ferramentas de gerenciamento e agendamento adicionais fornecidas pelo Swarm.

Este artigo se concentra no uso de modelos do Resource Manager para implantar a Extensão de VM do Docker em um ambiente personalizado e pronto para produção definido por você.

## <a name="azure-docker-vm-extension-for-template-deployments"></a>Extensão de VM do Docker do Azure para implantações de modelo
A Extensão de VM do Docker do Azure instala e configura o daemon do Docker, o cliente do Docker e o Docker Compose em sua máquina virtual Linux. A extensão também é usada para definir e implantar aplicativos de contêiner usando o Docker Compose. Você tem controles adicionais que vão além de usar o Computador Docker ou criar o host do Docker por conta própria, tornando a extensão adequada para ambientes de desenvolvimento mais robustos.

Usando o Azure Resource Manager, você pode criar e implantar modelos que definem a estrutura inteira do seu ambiente. Os modelos permitem que você defina armazenamento, RBAC (Controle de Acesso Baseado em Função), diagnóstico, hosts do Docker etc. Você pode [ler mais sobre o Resource Manager](../resource-group-overview.md) e os modelos para entender melhor alguns dos benefícios. Usando modelos do Resource Manager, você também pode reproduzir as implantações conforme for necessário no futuro.

## <a name="deploy-a-template-with-the-docker-vm-extension:"></a>Implantar um modelo com a Extensão de VM do Docker:
Vamos usar um modelo de início rápido existente para mostrar como implantar uma VM do Ubuntu que tem a Extensão de VM do Docker instalada. Você pode conferir o modelo aqui: [Simple deployment of an Ubuntu VM with Docker (Implantação simples de uma VM do Ubuntu com o Docker)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Você também precisará da [CLI do Azure mais recente](../xplat-cli-install.md) no modo Resource Manager (`azure config mode arm`).

Implante o modelo usando a CLI do Azure, especificando um nome para o novo grupo de recursos (veja `myDockerResourceGroup`) em conjunto com o URI do modelo:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda aos prompts para nomear sua conta de armazenamento, nome de usuário e a senha e o nome DNS. Você deverá ver uma saída semelhante ao exemplo a seguir:

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

A CLI do Azure retorna ao prompt após alguns segundos, mas em o modelo é implantado em segundo plano no grupo de recursos que você criou. Aguarde alguns minutos para que a implantação seja concluída antes de tentar SSH na VM.

Você pode obter detalhes sobre a implantação e o nome DNS da VM usando o comando `azure vm show` . No exemplo a seguir, substitua `myResourceGroup` pelo nome especificado na etapa anterior (o nome da VM padrão do modelo é `myDockerVM`; deixe esse nome como está):

```bash
azure vm show -g myResourceGroup -n myDockerVM
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/mydockerresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Na parte superior da saída, você vê o `ProvisioningState` da VM. Quando `Succeeded`for exibido, a implantação terá sido concluída e você poderá SSH na VM.

No final da saída, o `FQDN` exibe o nome de domínio totalmente qualificado com base no nome DNS fornecido e o local selecionado. Esse FQDN é usado para SSH na VM nas etapas restantes.

## <a name="deploy-your-first-nginx-container"></a>Implantar seu primeiro contêiner nginx
Depois que a implantação for concluída, faça a conexão SSH com seu novo host do Docker usando o nome DNS que você forneceu durante a implantação:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Vamos tentar executar um contêiner nginx:

```
sudo docker run -d -p 80:80 nginx
```

Você deverá ver uma saída semelhante ao exemplo a seguir:

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

Você poderá configurar a porta TCP do daemon do Docker, a segurança, ou implantar contêineres usando o Docker Compose. Para saber mais, confira a [Extensão da máquina virtual do Azure para o projeto GitHub do Docker](https://github.com/Azure/azure-docker-extension/).

## <a name="docker-vm-extension-json-template-reference"></a>Referência de modelo JSON da Extensão de VM do Docker
Este exemplo usou um modelo de início rápido. Para implantar a extensão de VM do Docker do Azure com seus próprios modelos do Resource Manager, acrescente o seguinte JSON:

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

## <a name="next-steps"></a>Próximas etapas
Leia etapas mais detalhadas para as diferentes opções de implantação:

1. [Usar o computador Docker com o driver do Azure](virtual-machines-linux-docker-machine.md)  
2. [Usando a extensão de VM Docker da interface de linha de comando do Azure (CLI do Azure)](virtual-machines-linux-classic-cli-use-docker.md)  
3. [Introdução ao Docker e Compose para definir e executar um aplicativo de vários contêineres em uma máquina virtual do Azure](virtual-machines-linux-docker-compose-quickstart.md).
4. [Implantar um cluster do Serviço de Contêiner do Azure](../container-service/container-service-deployment.md)

<!--HONumber=Oct16_HO2-->


