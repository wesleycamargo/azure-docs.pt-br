---
title: Montar o Armazenamento de Arquivos do Azure em VMs Linux usando o SMB | Microsoft Docs
description: Como montar o Armazenamento de Arquivos do Azure em VMs Linux usando a CLI 2.0 do Azure (Preview)
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: dbdebe120bd6166854f3494169d9c0d5c8f7bf69
ms.openlocfilehash: 6851faf301175ed851e53088862e6f8b50ad8b3e
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-20-preview"></a>Montar o Armazenamento de Arquivos do Azure em VMs Linux usando a CLI 2.0 do Azure (Preview)

Este artigo mostra como utilizar o serviço Armazenamento de Arquivos do Azure em uma VM Linux usando uma montagem SMB. O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão.  Esses requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

- [arquivos de chave SSH pública e privada](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI 2.0 do Azure (Visualização)](#quick-commands) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos (este artigo)


## <a name="quick-commands"></a>Comandos rápidos

Se você precisar executar a tarefa rapidamente, a seção a seguir fornecerá detalhes dos comandos necessários. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Pré-requisitos: Grupo de Recursos, VNet, NSG com entrada de SSH, Sub-rede, Conta de Armazenamento do Azure, chaves da Conta de Armazenamento do Azure, compartilhamento do Armazenamento de Arquivos do Azure e uma VM Linux. Substitua os exemplos por seus próprios valores.

Criar um diretório para a montagem local da seguinte maneira:

```bash
mkdir -p /mnt/mymountpoint
```

Montar o compartilhamento SMB do Armazenamento de Arquivos do Azure no ponto de montagem, da seguinte maneira:

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Para persistir a montagem após uma reinicialização, adicione uma linha a `/etc/fstab`, da seguinte maneira:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. E com a última versão do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos em qualquer sistema operacional que dá suporte ao SMB 3.0. Usar uma montagem SMB no Linux permite backups fáceis em uma localização robusta e permanente de armazenamento de arquivamento com suporte em um SLA.  

Mover arquivos de uma VM para uma montagem SMB hospedada no Armazenamento de Arquivos do Azure é uma ótima maneira de depurar logs, já que o mesmo compartilhamento SMB pode ser montado localmente na estação de trabalho Mac, Linux ou Windows. O SMB não seria a melhor solução para transmitir logs do Linux ou de aplicativo em tempo real, pois o protocolo SMB não foi desenvolvido para tarefas de log tão grandes assim. Uma ferramenta de camada de log unificada dedicada com o Fluentd poderá ser uma escolha melhor em relação ao SMB para coletar a saída de log do Linux e de aplicativo.

Para este passo a passo detalhado, criamos os pré-requisitos necessários para primeiro criar o compartilhamento do Armazenamento de Arquivos do Azure e, depois, montá-lo pelo SMB em uma VM Linux.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create) para armazenar nosso compartilhamento de arquivo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `West US`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-azure-storage-account"></a>Criar a conta de Armazenamento do Azure
Em seguida, crie uma conta de armazenamento com [az storage account create](/cli/azure/storage/account#create) para armazenar os arquivos reais. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount` usando o SKU de armazenamento `Standard_LRS`:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location westus \
    --sku Standard_LRS
```

## <a name="show-the-storage-account-keys"></a>Mostrar as chaves da conta de Armazenamento

As chaves da Conta de Armazenamento do Azure são criadas em pares quando a conta de armazenamento é criada. As chaves da conta de armazenamento são criadas em pares, para que as chaves possam ser giradas sem nenhuma interrupção de serviço. Depois de girar as chaves para a segunda chave do par, você cria um novo par de chaves. Novas chaves da conta de armazenamento são sempre criadas em pares, garantindo que você tem, pelo menos, uma chave de armazenamento não utilizada pronta para girar.

Veja as chaves da conta de armazenamento com a [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#list). O exemplo a seguir lista as chaves de conta de armazenamento para a conta de armazenamento chamada `mystorageaccount`:

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount
```

Para extrair uma única chave, use o sinalizador `--query`. O exemplo a seguir extrai a primeira chave (`[0]`):

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query '[0].{Key:value}' --output tsv
```


## <a name="create-the-azure-file-storage-share"></a>Criar o compartilhamento do Armazenamento de Arquivos do Azure

Criar o compartilhamento de Armazenamento de Arquivos que contém o compartilhamento SMB com [az storage share create](/cli/azure/storage/share#create). A cota é sempre em GBs (GigaBytes). Passe uma das chaves do comando anterior **az storage account keys list**. O exemplo a seguir cria um compartilhamento denominado `mystorageshare` com uma cota de `10` GB:

```azurecli
az storage share create --name mystorageshare \
    --quota 10 \
    --account-name mystorageaccount \
    --account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Criar o diretório de ponto de montagem

Um diretório local é necessário no sistema de arquivos do Linux no qual o compartilhamento SMB será montado. Tudo o que gravado ou lido no diretório de montagem local é encaminhado para o compartilhamento SMB hospedado no Armazenamento de Arquivos do Azure. O exemplo a seguir cria um diretório local em `/mnt/mymountdirectory`:

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Montar o compartilhamento SMB
Monte o compartilhamento SMB para o diretório local, que você cria da seguinte maneira. Forneça seu próprio nome de usuário de conta de armazenamento e chave de conta de armazenamento para as credenciais de montagem, da seguinte maneira:

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Persistir a montagem SMB por meio de reinicializações

Ao reinicializar a VM Linux, o compartilhamento SMB montado é desmontado durante o desligamento. Para montar novamente o compartilhamento SMB durante a inicialização, adicione uma linha a `/etc/fstab` do Linux. O Linux usa o arquivo `fstab` para listar os sistemas de arquivos de que precisa para a montagem durante a inicialização. A adição do compartilhamento SMB garante que o compartilhamento do Armazenamento de Arquivos do Azure é um sistema de arquivos montado permanentemente para a VM Linux. A adição do compartilhamento SMB do Armazenamento de Arquivos do Azure a uma nova VM é possível usando `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Próximas etapas

- [Uso de cloud-init para personalizar uma VM do Linux durante a criação](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adicionar um disco a uma VM do Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Criptografar discos em uma VM do Linux usando a CLI do Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

