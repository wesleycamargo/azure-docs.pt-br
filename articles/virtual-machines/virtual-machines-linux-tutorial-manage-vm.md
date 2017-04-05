---
title: "Gestão de máquinas virtuais do Linux com a CLI do Azure | Microsoft Docs"
description: "Tutorial - Como gerenciar máquinas virtuais do Linux com a CLI do Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 14d3907d9ddbe42c9f919ce4d29ba7d58c7c5296
ms.lasthandoff: 03/29/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>Como gerenciar máquinas virtuais do Linux com a CLI do Azure

Neste tutorial, você criará uma máquina virtual e executará tarefas comuns de gerenciamento, como adição de um disco, automatização da instalação de software e criação de um instantâneo de uma máquina virtual. 

Para concluir este tutorial, certifique-se de que a versão mais recente da [CLI 2.0 do Azure](/cli/azure/install-azure-cli) esteja instalada.

## <a name="step-1--log-in-to-azure"></a>Etapa 1 - Fazer logon no Azure

Primeiro, abra um terminal e faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login).

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>Etapa 2 - Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group#create). 

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual. Neste exemplo, criaremos um grupo de recursos `myResourceGroup` na região `westeurope`. 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>Etapa 3 – Preparar configuração

Ao implantar uma máquina virtual, **cloud-init** pode ser usado para automatizar configurações, como instalação de pacotes, criação de arquivos e execução de scripts. Neste tutorial, as configurações de dois itens são automatizadas:

- Instalação de um servidor de Web NGINX
- Provisionamento de um segundo disco na VM

Como a configuração **cloud-init** ocorre no momento da implantação da VM, você deve definir a configuração **cloud-init** antes de criar a máquina virtual.

Crie um nome de arquivo `cloud-init.txt` e copie o seguinte conteúdo. Essa configuração instala o pacote NGINX e executa comandos para formatar e montar o segundo disco.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>Etapa 4 - Criar uma máquina virtual

Crie uma máquina virtual com o comando [az vm create](https://docs.microsoft.com/cli/azure/vm#create). 

Há várias opções disponíveis ao criar uma máquina virtual, como a imagem do sistema operacional, as credenciais administrativas e o dimensionamento do disco. Neste exemplo, criaremos uma máquina virtual chamada `myVM` no Ubuntu. Use o argumento `--data-disk-sizes-gb` para criar um disco de 50 GB e anexá-lo à VM. O argumento `--custom-data` usa a configuração cloud-init e o prepara para a VM. Por fim, as chaves SSH serão criadas, se não houver nenhuma.

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

Depois de criar a VM, as saídas da CLI do Azure geram as seguintes informações. Anote o endereço IP público, ele será usado para acessar a máquina virtual. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Depois de implantar a VM, a configuração **cloud-init** pode levar alguns minutos para ser concluída. 

## <a name="step-5--configure-firewall"></a>Etapa 5 – Configurar o firewall

Um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG) do Azure controla o tráfego de entrada e saída em uma ou mais máquinas virtuais. As regras de grupo de segurança de rede permitem ou negam o tráfego de rede em uma porta específica ou em um intervalo de porta. Essas regras também podem incluir um prefixo do endereço de origem para que somente o tráfego originado em uma fonte predefinida possa se comunicar com uma máquina virtual.

O servidor de Web NGINX foi instalado na seção anterior. Sem uma regra de grupo de segurança de rede para permitir o tráfego de entrada na porta 80, o servidor de Web não pode ser acessado na Internet. Esta etapa orienta a criação da regra NSG para permitir conexões de entrada na porta 80.

### <a name="create-nsg-rule"></a>Como criar regra NSG

Para criar uma regra NSG de entrada, use o comando [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port). O exemplo a seguir abre a porta `80` para a máquina virtual.

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

Agora, navegue até o endereço IP público da máquina virtual. Com a regra NSG no local, o site padrão do NGINX é exibido.

![Site padrão NGINX](./media/virtual-machines-linux-tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>Etapa 6 – Instantâneo da máquina virtual

Criar um instantâneo cria uma cópia, de apenas leitura, de um ponto no tempo do disco. Nesta etapa, criaremos um instantâneo do disco do sistema operacional das VMs. Com um instantâneo do disco do sistema operacional, a máquina virtual pode ser restaurada rapidamente para um estado específico, ou o instantâneo pode ser usado para criar uma nova máquina virtual com um estado idêntico.

### <a name="create-snapshot"></a>Como criar um instantâneo

Antes de criar um instantâneo, você deve ter o Id ou o nome do disco. Use o comando [az vm show](https://docs.microsoft.com/cli/azure/vm#show) para obter o Id do disco. Neste exemplo, o Id do disco é armazenada em uma variável e usado em uma etapa posterior.

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Agora que você tem o Id do disco, o comando a seguir criará o instantâneo.

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Como criar o disco a partir de um instantâneo

Esse instantâneo pode ser convertido em um disco, que pode ser usado para recriar a máquina virtual.

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Como restaurar a máquina virtual a partir de um instantâneo

Para demonstrar a recuperação da máquina virtual, exclua a máquina virtual existente. 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

Ao recriar a máquina virtual, o adaptador de rede existente será reutilizado. Isso garante que as configurações de segurança de rede são mantidas.

Obtenha o nome do adaptador de rede usando o comando [az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list). Este exemplo coloca o nome em uma variável chamada `nic`, que será usada na próxima etapa.

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

Crie uma nova máquina virtual a partir do disco de instantâneo.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

Anote o novo endereço IP público e o navegador para esse endereço com um navegador da Internet. Você verá que o NGINX está em execução na máquina virtual restaurada. 

### <a name="reconfigure-data-disk"></a>Reconfigure o disco de dados

O disco de dados já pode ser anexado novamente à máquina virtual. 

Primeiro, use o comando [az disk list](https://docs.microsoft.com/cli/azure/disk#list) para encontrar o nome dos discos de dados. Este exemplo coloca o nome do disco em uma variável chamada `datadisk`, que será usada na próxima etapa.

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Use o comando [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) para anexar o disco.

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

O disco também precisa ser montado para o sistema operacional. Para montar o disco, conecte-se à máquina virtual e execute `sudo mount /dev/sdc1 /datadrive`, ou a operação de montagem no disco preferencial. 

## <a name="step-7--management-tasks"></a>Etapa 7 – Tarefas de gerenciamento

Durante o ciclo de vida de uma máquina virtual, é possível que você queira executar tarefas de gerenciamento, como inicialização, interrupção ou exclusão de uma máquina virtual. Além disso, é possível que você queira criar scripts para automatizar tarefas repetitivas ou complexas. Usando a CLI do Azure, muitas tarefas comuns de gerenciamento podem ser executadas em linha de comando ou em scripts. 

### <a name="get-ip-address"></a>Como obter o endereço IP

Esse comando retorna os endereços IP públicos e privados de uma máquina virtual.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>Redimensionar a máquina virtual

Para redimensionar uma máquina virtual do Azure, você precisa saber o nome dos tamanhos disponíveis na região escolhida do Azure. Esses tamanhos podem ser encontrados com o comando [az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes).

```azurecli
az vm list-sizes --location westeurope --output table
```

A máquina virtual pode ser redimensionada usando o comando [az vm resize](https://docs.microsoft.com/cli/azure/vm#resize). 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>Como interromper uma máquina virtual

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>Como iniciar uma máquina virtual

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>Excluir grupo de recursos

Excluir um grupo de recursos exclui todos os recursos contidos nele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Tutorial – [Criação de máquinas virtuais com balanceamento de carga](./virtual-machines-linux-tutorial-load-balance-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Exemplos – [Scripts de exemplo da CLI do Azure](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
