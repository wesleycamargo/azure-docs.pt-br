---
title: "Redefinir o acesso com a extensão VMAccess e CLI 2.0 do Azure | Microsoft Docs"
description: "Como gerenciar usuários e redefinir o acesso em VMs Linux usando a extensão VMAccess e a CLI 2.0 do Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e23e2b2d5f30a2ec30564287c96ffc9c671c0dbf
ms.contentlocale: pt-br
ms.lasthandoff: 04/03/2017

---
<a id="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20" class="xliff"></a>

# Gerenciar usuários, SSH e verificar ou reparar discos em VMs Linux do usando a extensão VMAccess com a CLI 2.0 do Azure
O disco em sua VM do Linux está mostrando erros. De alguma forma, você redefiniu a senha raiz para sua VM do Linux ou excluiu acidentalmente sua chave privada SSH. Se isso tiver ocorrido na época do datacenter, você precisará ir até lá e abrir o KVM para acessar o console do servidor. Considere a Extensão VMAccess do Azure como o comutador KVM que permite que você acesse o console para redefinir o acesso para o Linux ou para realizar a manutenção no nível de disco.

Este artigo mostra como usar a extensão VMAcesss do Azure para verificar ou reparar um disco, redefinir o acesso do usuário, gerenciar contas de usuário ou redefinir a configuração do SSHD no Linux. Você também pode executar essas etapas com a [CLI do Azure 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


<a id="ways-to-use-the-vmaccess-extension" class="xliff"></a>

## Modos de usar a extensão VMAccess
Há duas maneiras de usar a extensão VMAccess em VMs Linux:

* Use a CLI 2.0 do Azure e os parâmetros necessários.
* [Use os arquivos JSON brutos processados pela Extensão VMAccess](#use-json-files-and-the-vmaccess-extension) e depois tome atitudes em relação a eles.

Os exemplos a seguir usam [az vm access](/cli/azure/vm/access) juntamente com os parâmetros apropriados. Para realizar essas etapas, é preciso ter a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando o [logon az](/cli/azure/#login).

<a id="reset-ssh-key" class="xliff"></a>

## Redefinir chave SSH
O exemplo a seguir redefine a chave SSH para o usuário `azureuser` na VM denominada `myVM`:

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

<a id="reset-password" class="xliff"></a>

## Redefinir senha
O exemplo a seguir redefine a senha para o usuário `azureuser` na VM denominada `myVM`:

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

<a id="reset-sshd" class="xliff"></a>

## Redefinir SSHD
O exemplo a seguir redefine a configuração SSHD em uma VM denominada `myVM`:

```azurecli
az vm access reset-linux-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

<a id="create-a-user" class="xliff"></a>

## Criar um usuário
O exemplo a seguir cria um usuário chamado `myNewUser` usando uma chave SSH para autenticação na VM denominada `myVM`:

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

<a id="deletes-a-user" class="xliff"></a>

## Exclui um usuário
O exemplo a seguir exclui um usuário chamado `myNewUser` na VM denominada `myVM`:

```azurecli
az vm access delete-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


<a id="use-json-files-and-the-vmaccess-extension" class="xliff"></a>

## Usar arquivos JSON e a extensão VMAccess
Os exemplos a seguir usam arquivos JSON brutos. Use [az vm extension set](/cli/azure/vm/extension#set) para chamar seus arquivos JSON. Esses arquivos JSON também podem ser chamados dos modelos do Azure. 

<a id="reset-user-access" class="xliff"></a>

### Redefinir o acesso do usuário
Se você tiver perdido o acesso à raiz em sua VM do Linux, poderá iniciar um script da VMAccess para redefinir a senha do usuário.

Para redefinir a chave SSH de um usuário, crie um arquivo chamado `reset_ssh_key.json` e adicione o seguinte conteúdo:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Para redefinir uma senha de usuário, crie um arquivo chamado `reset_user_password.json` e adicione o seguinte conteúdo:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

<a id="reset-ssh" class="xliff"></a>

### Redefinir SSH
Se você alterar a configuração do SSHD de VMs do Linux e fechar a conexão SSH antes de verificar as alterações, talvez não consiga usar o SSH novamente.  A VMAccess pode ser usada para redefinir a configuração do SSHD para uma configuração válida conhecida sem estar conectado por SSH.

Para redefinir a configuração SSHD, crie um arquivo chamado `reset_sshd.json` e adicione o seguinte conteúdo:

```json
{
  "reset_ssh": true
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

<a id="manage-users" class="xliff"></a>

### Gerenciar usuários
A VMAccess é um script Python que pode ser usado para gerenciar usuários em sua VM do Linux sem logon, usando sudo ou a conta raiz.

Para criar um usuário, crie um arquivo chamado `create_new_user.json` e adicione o seguinte conteúdo:

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Para excluir um usuário, crie um arquivo chamado `delete_user.json` e adicione o seguinte conteúdo:

```json
{
  "remove_user":"myDeleteUser"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

<a id="check-or-repair-the-disk" class="xliff"></a>

### Verificar ou reparar o disco
Usando a VMAccess, você pode executar o fsck no disco em sua VM do Linux. Você também pode fazer uma verificação e um reparo de disco usando uma VMAccess.

Para verificar e reparar o disco use este script VMAccess, crie um arquivo chamado `disk_check_repair.json` e adicione o seguinte conteúdo:

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Execute o script VMAccess com:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

<a id="next-steps" class="xliff"></a>

## Próximas etapas
A atualização do Linux com a Extensão VMAccess do Azure é um método para fazer alterações em uma VM Linux em execução. Você também pode usar ferramentas como inicialização de nuvem e os modelos do Azure Resource Manager para modificar a VM Linux na inicialização.

[Sobre os recursos e extensões de máquina virtual](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Criando modelos do Azure Resource Manager com extensões de VM do Linux](../windows/extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso de cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


