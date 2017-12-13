---
title: Usar a cloud-init para atualizar e instalar pacotes em uma VM do Linux no Azure | Microsoft Docs
description: "Como usar cloud-init para atualizar e instalar pacotes em uma VM Linux durante a criação com a CLI do Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 4209bc270a6d255c8512dd6ccd5551b556da5a6b
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Usar a cloud-init para atualizar e instalar pacotes em uma VM do Linux no Azure
Este artigo mostra como usar [cloud-init](https://cloudinit.readthedocs.io) para atualizar pacotes em uma VM (máquina virtual) Linux ou VMSS (conjuntos de dimensionamento de máquinas virtuais) no tempo de provisionamento no Azure. Esses scripts cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações de como o cloud-init funciona nativamente no Azure e as distribuições do Linux compatíveis, consulte [Visão geral de cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Atualizar uma VM com a inicialização de nuvem
Para fins de segurança, convém configurar uma VM para aplicar as atualizações mais recentes na primeira inicialização. Como a inicialização de nuvem funciona em diferentes distribuições de Linux, não é necessário especificar `apt` ou `yum` para o gerenciador de pacotes. Em vez disso, você define `package_upgrade` e permite que o processo de inicialização de nuvem determine o mecanismo apropriado para a distribuição em uso. Esse fluxo de trabalho permite que você use os mesmos scripts de inicialização de nuvem entre distribuições.

Para ver o processo de atualização em ação, crie um arquivo em seu shell atual chamado *cloud_init_upgrade.txt* e cole a seguinte configuração. Para este exemplo, crie o arquivo no Cloud Shell, não no computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloud_init_upgrade.txt` para criar o arquivo e ver uma lista de editores disponíveis. Escolha #1 para usar o editor **nano**. Verifique se o arquivo cloud-init inteiro foi copiado corretamente, principalmente a primeira linha.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#create) e especifique o arquivo de inicialização de nuvem com `--custom-data cloud_init_upgrade.txt` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Conecte-se por SSH ao endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Execute a ferramenta de gerenciamento de pacotes e verifique se há atualizações. O exemplo a seguir usa `apt-get` em uma VM do Ubuntu:

```bash
sudo apt-get upgrade
```

Já que a cloud-init realizou a verificação em busca de atualizações e as instalou na inicialização, não deve haver atualizações a serem aplicadas, conforme mostrado na saída de exemplo a seguir:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

Você também pode ver que `httpd` foi instalado executando `yum history` e examinar a saída referenciando `httpd`. 

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais de alterações de configuração do cloud-init, consulte o seguinte:
 
- [Adicionar um usuário do Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar o nome do host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e injetar chaves](tutorial-automate-vm-deployment.md)