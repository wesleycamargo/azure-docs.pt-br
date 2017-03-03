---
title: Instalar o MongoDB em uma VM Linux usando a CLI do Azure 1.0 | Microsoft Docs
description: "Aprenda a instalar e configurar o MongoDB em uma máquina virtual do Linux no Azure usando o modelo de implantação do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/20/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: dee0ff8431652e8d68bc3e70146b55d8cf94bb64
ms.openlocfilehash: 577fc318865ff065f107e01ff642a75454c2feb4
ms.lasthandoff: 02/15/2017


---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Como instalar e configurar o MongoDB em uma VM Linux usando a CLI do Azure 1.0
[O MongoDB](http://www.mongodb.org) é um popular banco de dados NoSQL de código-fonte aberto e de alto desempenho. Este artigo mostra a você como instalar e configurar o MongoDB em uma VM do Linux no Azure usando o modelo de implantação do Resource Manager. São mostrados exemplos que explicam em detalhes como:

* [Instalar e configurar uma instância básica do MongoDB manualmente](#manually-install-and-configure-mongodb-on-a-vm)
* [Criar uma instância básica do MongoDB usando um Modelo do Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Criar um cluster fragmentado complexo MongoDB com conjuntos de réplicas usando um modelo do Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- CLI 1.0 do Azure – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI do Azure 2.0 (Visualização)](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar e configurar manualmente o MongoDB em uma VM
O MongoDB [fornece instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para distribuições de Linux incluindo Red Hat/CentOS, SUSE, Ubuntu e Debian. O exemplo a seguir cria uma VM `CentOS` usando uma chave SSH armazenada no `~/.ssh/id_rsa.pub`. Responda aos prompts para o nome da conta de armazenamento, nome DNS e credenciais de administrador:

```azurecli
azure vm quick-create --ssh-publickey-file ~/.ssh/id_rsa.pub --image-urn CentOS
```

Faça logon na VM usando o endereço IP público exibido no final da etapa anterior de criação da VM:

```bash
ssh azureuser@40.78.23.145
```

Para adicionar fontes de instalação para o MongoDB, crie um arquivo de repositório `yum` da seguinte maneira:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Abra o arquivo de repositório do MongoDB para edição. Adicione as linhas a seguir:

```sh
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Instale o MongoDB usando `yum` da seguinte maneira:

```bash
sudo yum install -y mongodb-org
```

Por padrão, SELinux é imposto nas imagens do CentOS que impedem que você acesse o MongoDB. Instale as ferramentas de gerenciamento de política e configure o SELinux para permitir que o MongoDB opere na porta TCP padrão 27017, conforme descrito a seguir. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço MongoDB da seguinte maneira:

```bash
sudo service mongod start
```

Verifique a instalação do MongoDB ao se conectar usando o cliente `mongo` local:

```bash
mongo
```

Agora teste a instância do MongoDB adicionando alguns dados e, em seguida, pesquisando por:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se desejar, configure o MongoDB para iniciar automaticamente durante uma reinicialização do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Criar uma instância básica do MongoDB em CentOS usando um modelo
Você pode criar uma instância básica do MongoDB em uma única VM CentOS usando o seguinte modelo de início rápido do Azure do Github. Este modelo usa a extensão de Script personalizado para Linux para adicionar um repositório `yum` para sua VM CentOS recém-criada e então instalar o MongoDB.

* [Instância básica do MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) –https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` na região `WestUS`. Insira seus próprios valores da seguinte maneira:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> A CLI do Azure retorna um prompt dentro de alguns segundos de criação da implantação, mas a instalação e configuração levam alguns minutos para serem concluídas. Verifique o status da implantação com `azure group deployment show myResourceGroup`, inserindo o nome do seu grupo de recursos adequadamente. Aguarde até que o `ProvisioningState` mostra 'Êxito' antes de tentar acessar a VM via SSH.
> 
> 

Depois que a implantação for concluída, acesse a VM via SSH. Obtenha o endereço IP da sua VM usando o comando `azure vm show` como no exemplo a seguir:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Próximo ao final da saída, o `Public IP address` é exibido. Acesse a VM via SSH com o endereço IP da VM:

```bash
ssh azureuser@138.91.149.74
```

Verifique a instalação do MongoDB ao se conectar usando o cliente `mongo` local conforme demonstrado a seguir:

```bash
mongo
```

Agora teste a instância adicionando alguns dados e, em seguida, pesquisando conforme demonstrado a seguir:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Criar um Cluster Fragmentado MongoDB complexo no CentOS usando um modelo
Você pode criar um cluster fragmentado MongoDB complexo usando o seguinte modelo de início rápido do Azure do Github. Esse modelo segue as [melhores práticas do cluster fragmentado MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer alta disponibilidade e redundância. O modelo cria dois fragmentos, com três nós em cada conjunto de réplicas. Um conjunto de réplicas de servidor de configuração com três nós também é criado, mais dois servidores do roteador `mongos` para fornecer consistência a aplicativos entre os fragmentos.

* [Cluster Fragmentado MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) –https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Implantar este cluster fragmentado MongoDB complexo requer mais de 20 núcleos, que é normalmente a contagem padrão de núcleos por região para uma assinatura. Abra uma solicitação de suporte do Azure para aumentar a contagem de núcleos.
> 
> 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` na região `WestUS`. Insira seus próprios valores da seguinte maneira:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> A CLI do Azure retorna um prompt dentro de alguns segundos de criação da implantação, mas a instalação e configuração podem levar mais de uma hora para serem concluídas. Verifique o status da implantação com `azure group deployment show myResourceGroup`, ajustando o nome do seu grupo de recursos adequadamente. Aguarde até que o `ProvisioningState` mostra 'Êxito' antes de conectar-se às VMs.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Nesses exemplos, você se conecta à instância do MongoDB localmente da VM. Se você desejar conectar-se à instância do MongoDB de outra VM ou de rede, certifique-se de que as devidas [regras de Grupo de Segurança de Rede tenham sido criadas](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre a criação de modelos, veja a [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

Os modelos do Azure Resource Manager usam a Extensão de Script Personalizado para baixar e executar scripts em suas VMs. Para obter mais informações, veja [Uso da extensão de script personalizado do Azure com máquinas virtuais do Linux](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


