---
title: Redefinir o acesso em VMs do Linux do Azure usando a extensão VMAccess | Microsoft Docs
description: Redefina o acesso em VMs do Linux do Azure usando a extensão VMAccess
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: v-livech

---
# Gerenciar usuários, SSH e verificar ou reparar discos em VMs do Linux do Azure usando a extensão VMAccess
Este artigo mostra como usar a extensão VMAcesss do Azure para verificar ou reparar um disco, redefinir o acesso do usuário, gerenciar contas de usuário ou redefinir a configuração do SSHD no Linux.

Os pré-requisitos são: [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/), [chaves pública e privada SSH](virtual-machines-linux-mac-create-ssh-keys.md) e a CLI do Azure instalada e alternada para o modo Azure Resource Manager usando `azure config mode arm`.

## Comandos rápidos
Há duas maneiras de usar VMAccess em suas VMs do Linux:

* Usando a CLI do Azure e os parâmetros necessários.
* Usando os arquivos JSON brutos que a VMAccess processa e usa.

Para a seção de comando rápido, vamos usar o método `azure vm reset-access` da CLI do Azure. Nos exemplos de comandos a seguir, substitua os valores que contêm “exemplo” pelos valores de seu próprio ambiente.

## Criar um Grupo de Recursos e uma VM do Linux
```bash
azure group create resourcegroupexample westus
```

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u userexample \
-g resourcegroupexample \
-l westus \
-y Linux \
-n debianexamplevm \
-Q Debian
```

## Redefinir a senha raiz
Para redefinir a senha raiz:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u root -p examplenewPassword
```

## Redefinição de chave SSH
Para redefinir a chave SSH de um usuário não raiz:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -M ~/.ssh/id_rsa.pub
```

## Criar um usuário
Para criar um usuário:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -p examplePassword
```

## Remover um usuário
```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -R userexample
```

## Redefinir SSHD
Para redefinir a configuração de SSH:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -r
```


## Passo a passo detalhado
### VMAccess definido:
O disco em sua VM do Linux está mostrando erros. De alguma forma, você redefiniu a senha raiz para sua VM do Linux ou excluiu acidentalmente sua chave privada SSH. Se isso tiver ocorrido na época do datacenter, você precisará ir até lá e abrir o KVM para acessar o console do servidor. Considere a Extensão VMAccess do Azure como o comutador KVM que permite que você acesse o console para redefinir o acesso para o Linux ou para realizar a manutenção no nível de disco.

Para obter o passo a passo detalhado, vamos usar a forma longa da VMAccess, que usa arquivos JSON brutos. Esses arquivos json da VMAccess também podem ser chamados desde os Modelos do Azure.

### Usar a VMAccess para verificar ou reparar o disco de uma VM do Linux
Usando a VMAccess, você pode executar o fsck no disco em sua VM do Linux. Você também pode fazer uma verificação e um reparo de disco usando uma VMAccess.

Para verificar e reparar o disco, use este script da VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Execute o script VMAccess com:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### Usar a VMAccess para redefinir o acesso do usuário para Linux
Se você tiver perdido o acesso à raiz em sua VM do Linux, poderá iniciar um script da VMAccess para redefinir a senha raiz.

Para redefinir a senha raiz, use este script da VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"exampleNewPassword",   
}
```

Execute o script VMAccess com:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Para redefinir a chave SSH de um usuário não raiz, use este script da VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"exampleUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",   
}
```

Execute o script VMAccess com:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### Usar a VMAccess para gerenciar contas de usuário no Linux
A VMAccess é um script Python que pode ser usado para gerenciar usuários em sua VM do Linux sem logon, usando sudo ou a conta raiz.

Para criar um novo usuário, use este script da VMAccess:

`create_new_user.json`

```json
{
"username":"exampleNewUserName",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",
"password":"examplePassword",
}
```

Execute o script VMAccess com:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Para criar um usuário:

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

Execute o script VMAccess com:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### Usar a VMAccess para redefinir a configuração do SSHD
Se você alterar a configuração do SSHD de VMs do Linux e fechar a conexão SSH antes de verificar as alterações, talvez não consiga usar o SSH novamente. A VMAccess pode ser usada para redefinir a configuração do SSHD para uma configuração válida conhecida sem estar conectado por SSH.

Para redefinir a configuração do SSHD use esse script VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Execute o script VMAccess com:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## Próximas etapas
A atualização do Linux com as Extensões VMAccess do Azure é um método para fazer alterações em uma VM do Linux em execução. Você também pode usar ferramentas como inicialização de nuvem e os Modelos do Azure para modificar a VM do Linux na inicialização.

[Sobre os recursos e extensões de máquina virtual](virtual-machines-linux-extensions-features.md)

[Criando modelos do Azure Resource Manager com extensões de VM do Linux](virtual-machines-linux-extensions-authoring-templates.md)

[Uso de cloud-init para personalizar uma VM do Linux durante a criação](virtual-machines-linux-using-cloud-init.md)

<!---HONumber=AcomDC_0831_2016-->