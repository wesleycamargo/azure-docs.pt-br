---
title: Redefinir chave SSH e senha de VM do Linux da CLI | Microsoft Docs
description: "Como usar a extensão VMAccess da CLI (Interface de linha de comando) do Azure para redefinir uma chave SSH ou uma senha de VM do Linux, corrigir a configuração de SSH e verificar a consistência de disco"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: b820e3011a1cd5472604e8cfa98fc22f9a4ebf77


---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Como redefinir uma senha de VM do Linux ou chave SSH, corrigir a configuração de SSH e verificar a consistência de disco usando a extensão VMAccess
Se você não pode se conectar a uma máquina virtual Linux no Azure devido a uma senha esquecida, uma chave do Secure Shell (SSH) incorreta ou um problema com a configuração do SSH, use a extensão VMAccessForLinux com a CLI do Azure para redefinir a senha ou chave SSH ou corrigir a configuração do SSH e verificar a consistência do disco. 

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Saiba como [executar estas etapas usando o modelo do Resource Manager](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Com a CLI do Azure, você poderá usar o comando **azure vm extension set** na sua interface de linha de comando (Bash, Terminal, Prompt de comando) para acessar comandos. Execute **azure help vm extension set** para ver o uso detalhado da extensão.

Com o CLI do Azure, você pode realizar as seguintes tarefas:

* [Redefinir a senha](#pwresetcli)
* [Redefinir a chave SSH](#sshkeyresetcli)
* [Redefinir a senha e a chave SSH](#resetbothcli)
* [Criar uma nova conta de usuário sudo](#createnewsudocli)
* [Redefinir a configuração de SSH](#sshconfigresetcli)
* [Excluir um usuário](#deletecli)
* [Exibir o status da extensão VMAccess](#statuscli)
* [Verificar a consistência dos discos adicionados](#checkdisk)
* [Reparar discos adicionados na sua VM do Linux](#repairdisk)

## <a name="prerequisites"></a>Pré-requisitos
Você precisará fazer o seguinte:

* Você também precisará [instalar a CLI do Azure](../xplat-cli-install.md) e [conectar-se à sua assinatura](../xplat-cli-connect.md) para usar recursos do Azure associados à sua conta.
* Defina o modo correto para o modelo de implantação clássico digitando o seguinte no prompt de comando:
  
        azure config mode asm
* Tenha uma nova senha ou conjunto de chaves SSH, se quiser redefinir um deles. Você não precisa deles para redefinir a configuração de SSH.

## <a name="a-namepwresetcliareset-the-password"></a><a name="pwresetcli"></a>Redefinir a senha
1. Crie um arquivo chamado PrivateConf.json com essas linhas. Substitua os valores dos colchetes e do &#60espaço reservado&#62; por suas próprias informações.
   
        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }
2. Execute esse comando, substituindo o nome da máquina virtual em &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="a-namesshkeyresetcliareset-the-ssh-key"></a><a name="sshkeyresetcli"></a>Redefinir a chave SSH
1. Crie um arquivo chamado PrivateConf.json com esse conteúdo. Substitua os valores dos colchetes e do &#60espaço reservado&#62; por suas próprias informações.
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }
2. Execute esse comando, substituindo o nome da máquina virtual em &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-nameresetbothcliareset-both-the-password-and-the-ssh-key"></a><a name="resetbothcli"></a>Redefinir a senha e a chave SSH
1. Crie um arquivo chamado PrivateConf.json com esse conteúdo. Substitua os valores dos colchetes e do &#60espaço reservado&#62; por suas próprias informações.
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }
2. Execute esse comando, substituindo o nome da máquina virtual em &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namecreatenewsudocliacreate-a-new-sudo-user-account"></a><a name="createnewsudocli"></a>Criar uma nova conta de usuário sudo
Se você esquecer seu nome de usuário, você pode usar VMAccess para criar um novo com a autoridade sudo. Nesse caso, o nome de usuário e a senha existente não serão modificados.

Para criar um novo usuário sudo com senha de acesso, use o script em [Redefinir a senha](#pwresetcli) e especifique o novo nome de usuário.

Para criar um novo usuário sudo com senha de acesso, use o script em [Redefinir a chave SSH](#sshkeyresetcli) e especifique o novo nome de usuário.

Você também pode usar [Redefinir a senha e a chave SSH](#resetbothcli) para criar um novo usuário com senha e o acesso à chave de SSH.

## <a name="a-namesshconfigresetcliareset-the-ssh-configuration"></a><a name="sshconfigresetcli"></a>Redefinir a configuração de SSH
Se a configuração do SSH está em um estado indesejado, você também pode perder o acesso à VM. Você pode usar a extensão VMAccess para redefinir a configuração para seu estado padrão. Para fazer isso, basta definir a chave "reset_ssh" como "True". A extensão reinicia o servidor SSH, abre a porta SSH na sua VM e redefine a configuração SSH como padrão. A conta de usuário (nome, senha ou chaves SSH) não será alterada.

> [!NOTE]
> O arquivo de configuração SSH está localizado em /etc/ssh/sshd_config.
> 
> 

1. Crie um arquivo chamado PrivateConf.json com esse conteúdo.
   
        {
        "reset_ssh":"True"
        }
2. Execute esse comando, substituindo o nome da máquina virtual em &#60;vm-name&#62;. 
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namedeletecliadelete-a-user"></a><a name="deletecli"></a>Excluir um usuário
Se você deseja excluir uma conta de usuário sem efetuar login à VM diretamente, você pode usar este script.

1. Crie um arquivo chamado PrivateConf.json com esse conteúdo, substituindo o nome de usuário a ser removido em &#60;usernametoremove&#62;. 
   
        {
        "remove_user":"<usernametoremove>"
        }
2. Execute esse comando, substituindo o nome da máquina virtual em &#60;vm-name&#62;. 
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namestatuscliadisplay-the-status-of-the-vmaccess-extension"></a><a name="statuscli"></a>Exibir o status da extensão VMAccess
Para exibir o status da extensão VMAccess, execute este comando.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a><a name='checkdisk'<</a>Verificar consistência dos discos adicionados
Para executar fsck em todos os discos na sua máquina virtual Linux, você precisará fazer o seguinte:

1. Crie um arquivo chamado PublicConf.json com esse conteúdo. A verificação de disco tem um valor booliano para se deseja verificar os discos anexados à sua máquina virtual ou não. 
   
        {   
        "check_disk": "true"
        }
2. Execute esse comando, substituindo o nome da máquina virtual em &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name="a-namerepairdiskarepair-disks"></a><a name='repairdisk'></a>Reparar discos
Para reparar discos que não são de montagem ou tem erros de configuração de montagem, use a extensão VMAccess para redefinir a configuração de montagem em sua máquina virtual do Linux. Substitua o nome do seu disco em &#60;yourdisk&#62;.

1. Crie um arquivo chamado PublicConf.json com esse conteúdo. 
   
        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }
2. Execute esse comando, substituindo o nome da máquina virtual em &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

## <a name="next-steps"></a>Próximas etapas
* Se você quiser usar os cmdlets do Azure PowerShell ou os modelos do Azure Resource Manager para redefinir a senha ou chave SSH, corrigir a configuração de SSH e verificar a consistência do disco, veja a [documentação da extensão VMAccess no GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Você também pode usar o [portal do Azure](https://portal.azure.com) para redefinir a senha ou a chave SSH de uma VM do Linux implantada no modelo de implantação clássico. No momento, não é possível usar o portal para fazer isso para uma VM do Linux implantada no modelo de implantação do Gerenciador de Recursos.
* Veja [Sobre os recursos e extensões de máquina virtual](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre como usar extensões de VM para máquinas virtuais do Azure.




<!--HONumber=Nov16_HO3-->


