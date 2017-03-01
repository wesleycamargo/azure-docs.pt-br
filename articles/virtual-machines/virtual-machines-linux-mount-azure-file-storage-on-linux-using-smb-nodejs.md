---
title: Montar o Armazenamento de Arquivos do Azure em VMs Linux usando SMB com CLI 1.0 do Azure | Microsoft Docs
description: Como montar o Armazenamento de Arquivos do Azure em VMs Linux usando o SMB.
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
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 4d547f854df5d4801779e37ac5f22fb9551bd6bb
ms.openlocfilehash: 73151dba33e2c05a66176ef6b895339bce167128
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-10"></a>Montar o Armazenamento de Arquivos do Azure em VMs Linux usando a CLI 1.0 do Azure

Este artigo mostra como utilizar o serviço Armazenamento de Arquivos do Azure em uma VM Linux usando uma montagem SMB.  O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão.  Esses requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

- [arquivos de chave SSH pública e privada](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure (Visualização)](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos

Se você precisar executar a tarefa rapidamente, a seção a seguir fornecerá detalhes dos comandos necessários. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Pré-requisitos: Grupo de Recursos, VNet, NSG com entrada de SSH, Sub-rede, Conta de Armazenamento do Azure, chaves da Conta de Armazenamento do Azure, compartilhamento do Armazenamento de Arquivos do Azure e uma VM Linux. Substitua os exemplos por suas próprias configurações.

Criar um diretório para a montagem local

```bash
mkdir -p /mnt/mymountpoint
```

Montar o compartilhamento SMB do Armazenamento de Arquivos do Azure no ponto de montagem

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Para persistir a montagem após uma reinicialização, adicione uma linha a `/etc/fstab`

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão.  E com a última versão do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos em qualquer sistema operacional que dá suporte ao SMB 3.0.  Usar uma montagem SMB no Linux permite backups fáceis em uma localização robusta e permanente de armazenamento de arquivamento com suporte em um SLA.  

Mover arquivos de uma VM para uma montagem SMB hospedada no Armazenamento de Arquivos do Azure é uma ótima maneira de depurar logs, já que o mesmo compartilhamento SMB pode ser montado localmente na estação de trabalho Mac, Linux ou Windows.  O SMB não seria a melhor solução para transmitir logs do Linux ou de aplicativo em tempo real, pois o protocolo SMB não foi desenvolvido para tarefas de log tão grandes assim.  Uma ferramenta de camada de log unificada dedicada com o Fluentd poderá ser uma escolha melhor em relação ao SMB para coletar a saída de log do Linux e de aplicativo.

Para este passo a passo detalhado, criamos os pré-requisitos necessários para primeiro criar o compartilhamento do Armazenamento de Arquivos do Azure e, depois, montá-lo pelo SMB em uma VM Linux.

## <a name="create-the-azure-storage-account"></a>Criar a conta de Armazenamento do Azure

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>Mostrar as chaves da conta de Armazenamento

As chaves da Conta de Armazenamento do Azure são criadas em pares quando a conta de armazenamento é criada.  As chaves da conta de armazenamento são criadas em pares, para que as chaves possam ser giradas sem nenhuma interrupção de serviço.  Depois de girar as chaves para a segunda chave do par, você cria um novo par de chaves.  Novas chaves da conta de armazenamento são sempre criadas em pares, garantindo que você tem, pelo menos, uma chave de armazenamento não utilizada pronta para girar.

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>Criar o compartilhamento do Armazenamento de Arquivos do Azure

Crie o compartilhamento do Armazenamento de Arquivos, que contém o compartilhamento SMB.  A cota é sempre em GBs (GigaBytes).

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Criar o diretório de ponto de montagem

Um diretório local é necessário no sistema de arquivos do Linux no qual o compartilhamento SMB será montado.  Tudo o que gravado ou lido no diretório de montagem local é encaminhado para o compartilhamento SMB hospedado no Armazenamento de Arquivos do Azure.

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Montar o compartilhamento SMB

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Persistir a montagem SMB por meio de reinicializações

Ao reinicializar a VM Linux, o compartilhamento SMB montado é desmontado durante o desligamento.  Para montar novamente o compartilhamento SMB durante a inicialização, é necessário adicionar uma linha a `/etc/fstab` do Linux.  O Linux usa o arquivo `fstab` para listar os sistemas de arquivos de que precisa para a montagem durante a inicialização.  A adição do compartilhamento SMB garante que o compartilhamento do Armazenamento de Arquivos do Azure é um sistema de arquivos montado permanentemente para a VM Linux.  A adição do compartilhamento SMB do Armazenamento de Arquivos do Azure a uma nova VM é possível usando `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Próximas etapas

- [Uso de cloud-init para personalizar uma VM do Linux durante a criação](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adicionar um disco a uma VM do Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Criptografar discos em uma VM do Linux usando a CLI do Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

