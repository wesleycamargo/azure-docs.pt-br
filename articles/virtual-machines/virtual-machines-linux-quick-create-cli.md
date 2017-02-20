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
ms.date: 01/13/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 0fd7aa8f941adaeb9961fd0e4724161b9fe2eeee


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Criar uma VM do Linux usando a CLI 2.0 Preview do Azure (az.py)
Este artigo mostra como implantar rapidamente uma máquina virtual do Linux (VM) no Azure usando o comando [az vm create](/cli/azure/vm#create), usando a CLI do Azure 2.0 (visualização), usando os dois discos gerenciados como discos em contas de armazenamento nativo.

> [!NOTE] 
> A CLI do Azure 2.0 Visualização do Azure é a nossa CLI multi-plataforma de próxima geração. [Experimente.](https://docs.microsoft.com/cli/azure/install-az-cli2)
>
> Para criar uma VM usando a CLI 1.0 existente do Azure, e não a CLI do Azure 2.0 Visualização, veja [Criar uma VM com a CLI do Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para criar uma VM, você precisa do seguinte: 

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* a [CLI do Azure v. 2.0 (visualização)](/cli/azure/install-az-cli2) instalada
* estar conectado à sua conta do Azure (digite [az login](/cli/azure/#login))

(Você também pode implantar uma VM do Linux usando o [portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

O exemplo a seguir mostra como implantar uma VM Debian e como conectá-la usando a chave SSH (Secure Shell). Seus argumentos podem ser diferentes; se quiser uma imagem diferente, você [poderá procurar uma](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Uso de discos gerenciados

Para usar Azure Managed Disks, você deve usar uma região que ofereça suporte a eles. Primeiro, digite [az group create](/cli/azure/group#create) para criar o grupo de recursos que contém todos os recursos implantados:

```azurecli
 az group create -n myResourceGroup -l westus
```

A saída é semelhante à seguinte (você pode especificar uma outra opção de `--output` se quiser ver um formato diferente):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>Criar sua VM 
Agora, você pode criar sua VM e seu ambiente. Lembre-se de substituir o valor `--public-ip-address-dns-name` por um valor exclusivo; o exibido abaixo já pode estar em uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


A saída tem a seguinte aparência. Observe o valor do `publicIpAddress` ou do `fqdn` para a chave **ssh** em sua VM.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Faça logon na VM usando o endereço IP público ou o nome de domínio totalmente qualificado (FQDN) listado na saída.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

Você deve esperar ver algo como a seguinte saída, dependendo da distribuição que escolher:

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Veja [Próximas etapas](#next-steps) para outras coisas que você pode fazer com sua nova VM usando discos gerenciados.

## <a name="using-unmanaged-disks"></a>Uso de discos não gerenciados 

As máquinas virtuais que usam discos de armazenamento não gerenciado têm contas de armazenamento não gerenciado e primeiro gigite [az group create](/cli/azure/group#create) para criar o grupo de recursos para conter todos os recursos implantados:

```azurecli
az group create --name nativedisks --location westus
```

A saída é tem a seguinte aparência (você pode escolher uma opção `--output` diferente se desejar):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>Criar sua VM 

Agora, você pode criar sua VM e seu ambiente. Lembre-se de substituir o valor `--public-ip-address-dns-name` por um valor exclusivo; o exibido abaixo já pode estar em uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-native-disk
```

A saída tem a seguinte aparência. Observe o valor do `publicIpAddress` ou do `fqdn` para a chave **ssh** em sua VM.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Faça logon sua máquina virtual usando o endereço IP público ou o nome de domínio totalmente qualificado (FQDN) que estão listados na saída acima.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

Você deve esperar ver algo como a seguinte saída, dependendo da distribuição que escolher:

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Próximas etapas
O comando `az vm create` é a maneira de implantar rapidamente uma VM para que você possa fazer logon em um shell bash e começar a trabalhar. No entanto, o uso de `az vm create` não lhe dá amplo controle e não permite que você crie um ambiente mais complexo.  Para implantar uma VM do Linux personalizada para sua infraestrutura, você pode seguir qualquer um destes artigos:

* [Usar um modelo do Azure Resource Manager para criar uma implantação específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux Protegida por SSH no Azure usando modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Você também pode [usar o driver do Azure para `docker-machine` com vários comandos para criar rapidamente uma VM do Linux como um host do docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se estiver usando o Java, experimente o método [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Feb17_HO2-->


