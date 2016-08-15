<properties
    pageTitle="Redefinir o acesso em VMs do Linux do Azure usando a extensão VMAccess | Microsoft Azure"
    description="Redefina o acesso em VMs do Linux do Azure usando a extensão VMAccess"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# Gerenciar usuários, SSH e verificar ou reparar discos em VMs do Linux do Azure usando a extensão VMAccess

Este artigo mostra como usar a extensão de VM VMAcesss [(Github)](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) para verificar ou reparar o disco, redefinir o acesso do usuário, gerenciar contas de usuário ou a configuração SSHD no Linux. Este artigo exige [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/), [chaves SSH](virtual-machines-linux-mac-create-ssh-keys.md), uma máquina Virtual do Linux do Azure e a CLI do Azure instalada e alternada para o modo ARM usando `azure config mode arm`.

## Comandos rápidos

Há duas maneiras de usar VMAccess em suas VMs do Linux:

- Usando a CLI do Azure e os parâmetros necessários.
- Usando os arquivos JSON brutos que a VMAccess processará e usará.

Para a seção de comando rápido, usaremos o método `azure vm reset-access` da CLI do Azure. Nos exemplos de comandos a seguir, substitua os valores entre &lt; e &gt; pelos valores de seu próprio ambiente.

## Redefinir a senha raiz

Para redefinir a senha raiz:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u root -p <examplePassword>
```

## Redefinição de chave SSH

Para redefinir a chave SSH de um usuário não raiz:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleUser> -M <~/.ssh/azure_id_rsa.pub>
```

## Criar um usuário

Para criar um novo usuário:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleNewUserName> -p <examplePassword>
```

## Remover um usuário

```bash
azure vm reset-access -g <resource group> -n <vm name> -R <exampleNewUserName>
```

## Redefinir SSHD

Para redefinir a configuração de SSH:

```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```


## Passo a passo detalhado

### VMAccess definido:

O disco em sua VM do Linux está mostrando erros. De alguma forma, você redefiniu a senha raiz para sua VM do Linux ou excluiu acidentalmente sua chave privada SSH. Se isso acontecesse nos primeiros anos dos datacenters, você teria dirigido até lá, teria destrancado a porta usando suas digitais, entrado no compartimento para abrir o KVM e chegar até o console do servidor. Considere a Extensão VMAccess do Azure como o comutador KVM que permite que você acesse o console para redefinir o acesso para o Linux ou para realizar a manutenção no nível de disco.

Para obter o passo a passo detalhado, usaremos a forma longa da VMAccess que usa arquivos json brutos. Esses arquivos json da VMAccess também podem ser chamados desde os Modelos do Azure.

### Usar a VMAccess para verificar ou reparar o disco de uma VM do Linux

Usando a VMAccess, você pode executar o fsck no disco em sua VM do Linux. Você pode fazer uma verificação de disco e então um reparo de disco, se encontrar erros.

Para verificar e então reparar o disco, use este script VMAccess:

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

Se você tiver perdido o acesso à raiz na sua VM do Linux, poderá iniciar um script VMAccess para redefinir a senha raiz.

Para redefinir a senha raiz, use este script VMAccess:

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

Para redefinir a chave SSH de um usuário não raiz, use este script VMAccess:

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

Para criar um novo usuário, use este script VMAccess:

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

Para criar um novo usuário, use este script VMAccess:

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

### Usar a VMAccess para redefinir a configuração do SSHD no Linux

Se você alterar a configuração do SSHD de VMs do Linux e fechar a conexão SSH antes de verificar as alterações, talvez não consiga usar o SSH novamente. A VMAccess pode ser usada para redefinir a configuração do SSHD para uma configuração válida.

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

<!---HONumber=AcomDC_0803_2016-->