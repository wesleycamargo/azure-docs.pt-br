---
title: "Criar uma VM do Linux usando a CLI 2.0 (visualização) do Azure | Microsoft Azure"
description: "Criar uma VM do Linux usando a CLI 2.0 (visualização) do Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2bd363e3c22f4cf4daf2e0fa352fd4a131d1675f
ms.openlocfilehash: 89db2c9f388b8a5496a306ba0a152ab57481ea50


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview"></a>Criar uma VM do Linux usando a CLI 2.0 (visualização) do Azure
Este artigo mostra como implantar rapidamente uma nova VM (máquina virtual) no Azure usando o comando [az vm create](/cli/azure/vm#create) na CLI 2.0 (visualização) do Azure. 

> [!NOTE] 
> A CLI 2.0 de visualização do Azure é a nossa CLI multi-plataforma de próxima geração. Experimente-a e dê sua opinião sobre ela na [página do projeto GitHub](https://github.com/Azure/azure-cli).
>
> O restante dos nossos documentos usa a CLI existente do Azure. Para criar uma VM usando a CLI existente do Azure, e não a CLI 2.0 de visualização, consulte [Criar uma VM com a CLI do Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para criar uma VM, você precisa do seguinte: 

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* a [CLI do Azure v. 2.0 (visualização)](https://github.com/Azure/azure-cli#installation) instalada
* estar conectado à sua conta do Azure (digite [az login](/cli/azure/#login))

(Também é possível implantar rapidamente uma VM Linux usando o [portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

O exemplo a seguir mostra como implantar uma VM Debian e anexar sua chave SSH (Secure Shell) (seus argumentos podem ser diferentes; se quiser outra imagem, você pode [pesquisar uma](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

Primeiro, digite [az resource group create](/cli/azure/resource/group#create) para criar o grupo de recursos que contém todos os recursos implantados:

```azurecli
az resource group create -n myResourceGroup -l westus
```

A saída é tem a seguinte aparência (você pode escolher uma opção `--output` diferente se desejar):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Criar sua VM usando a imagem mais recente do Debian

Agora, você pode criar sua VM e seu ambiente. Lembre-se de substituir o valor `----public-ip-address-dns-name` por um valor exclusivo; o exibido abaixo já pode estar em uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


A saída tem a seguinte aparência. Observe o valor do `publicIpAddress` ou do `fqdn` para a chave **ssh** em sua VM.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Entre na VM usando o endereço IP público listado na saída. Você também pode usar o FQDN (nome de domínio totalmente qualificado) que está listado.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

Você deve esperar ver algo como a seguinte saída, dependendo da distribuição que escolher:

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Próximas etapas
O comando `az vm create` é a maneira de implantar rapidamente uma VM para que você possa fazer logon em um shell bash e começar a trabalhar. No entanto, o uso de `az vm create` não lhe dá amplo controle e não permite que você crie um ambiente mais complexo.  Para implantar uma VM do Linux personalizada para sua infraestrutura, você pode seguir qualquer um destes artigos:

* [Usar um modelo do Azure Resource Manager para criar uma implantação específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux Protegida por SSH no Azure usando modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Você também pode [usar o driver do Azure para `docker-machine` com vários comandos para criar rapidamente uma VM do Linux como um host do docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se estiver usando o Java, experimente o método [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Nov16_HO4-->


