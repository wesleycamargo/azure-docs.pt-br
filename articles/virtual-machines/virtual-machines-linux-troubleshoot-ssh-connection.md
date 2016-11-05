---
title: Conexão SSH com uma VM Linux é recusada, falha ou apresenta erro | Microsoft Docs
description: Solucione problemas e corrija erros de SSH, como falha de conexão SSH ou conexão SSH recusada, para uma VM do Azure com Linux.
keywords: conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: iainfou

---
# Solucionar problemas de conexão SSH com uma VM do Linux do Azure que falha, apresenta erro ou é recusada
Há vários motivos pelos quais você pode obter erros de SSH (Secure Shell), falha na conexão SSH ou recusa ao tentar se conectar a uma VM (máquina virtual) do Azure baseada em Linux. Este artigo vai ajudar você a localizar e corrigir os problemas.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se você precisar de mais ajuda a qualquer momento neste artigo, poderá contatar os especialistas do Azure nos [fóruns do Azure MSDN e Stack Overflow](http://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](http://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).

## VMs criadas com o modelo de implantação do Resource Manager
Você pode redefinir as credenciais ou SSHD usando qualquer um dos comandos da CLI do Azure diretamente ou usando a [extensão VMAccessForLinux do Azure](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). Após cada etapa de solução de problemas, tente se conectar à VM novamente.

### Pré-requisitos da CLI do Azure
Se você ainda não fez isso, [instale a CLI do Azure e conecte-se à sua assinatura do Azure](../xplat-cli-install.md). Entre usando o comando `azure login` e verifique se você está no modo do Resource Manager (`azure config mode arm`).

Verifique se a versão 2.0.5 ou posterior do [Agente Linux do Microsoft Azure](virtual-machines-linux-agent-user-guide.md) está instalada.

### Redefinir SSHD
A configuração SSHD em si pode estar definida incorretamente ou o serviço encontrou um erro. Você pode redefinir o SSHD para garantir que a configuração de SSH em si é válida.

#### CLI do Azure
```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```

#### Extensão de acesso à VM
As extensões de acesso leem um arquivo json que define qual ação realizar, como redefinir SSH, redefinir uma chave SSH, adicionar um novo usuário, etc. Primeiramente, crie um arquivo chamado PrivateConf.json com o seguinte conteúdo:

```bash
{  
    "reset_ssh":"True"
}
```

Em seguida, execute manualmente a extensão `VMAccessForLinux` para redefinir a conexão SSHD:

```bash
azure vm extension set <resource group> <vm name> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Redefinir credenciais SSH para um usuário
Se SSHD parecer funcionar corretamente, você poderá redefinir a senha para um determinado usuário.

#### CLI do Azure
```bash
azure vm reset-access -g <resource group> <vm name> -u <username> -p <new password>
```

Se usar autenticação de chave SSH, você poderá redefinir a chave SSH para um determinado usuário:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <username> -M <~/.ssh/azure_id_rsa.pub>
```

#### Extensão de acesso à VM
Crie um arquivo chamado PrivateConf.json com os seguintes conteúdos:

```bash
{
    "username":"Username", "password":"NewPassword"
}
```

Ou então, redefina a chave SSH para um determinado usuário e crie um arquivo chamado PrivateConf.json com o conteúdo a seguir:

```bash
{
    "username":"Username", "ssh_key":"ContentsOfNewSSHKey"
}
```

Depois de uma das etapas acima, execute manualmente a extensão `VMAccessForLinux` para redefinir suas credenciais de usuário SSH:

```
azure vm extension set <resource group> <vmname> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Reimplantar uma VM
Você pode reimplantar uma VM para outro nó no Azure, o que pode corrigir possíveis problemas de rede subjacentes. Para reimplantar uma VM usando o Portal do Azure, selecione **Procurar** > **Máquinas virtuais** > *sua máquina virtual do Linux* > **Reimplantar**. Para obter informações sobre como fazer isso, consulte [Reimplantar Máquina Virtual em um novo nó do Azure](virtual-machines-windows-redeploy-to-new-node.md). No momento, não é possível reimplantar uma VM usando a CLI do Azure.

> [!NOTE]
> Observe que, após a conclusão dessa operação, os dados de disco efêmero serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
> 
> 

## VMs criadas com o modelo de implantação clássico
Experimente essas etapas para resolver as falhas de conexão SSH mais comuns em VMs criadas usando o modelo de implantação clássico. Após cada etapa, tente se reconectar à VM.

* Redefina o acesso remoto no [Portal do Azure](https://portal.azure.com). No Portal do Azure, selecione **Procurar** > **Máquinas virtuais (clássicas)** > *sua máquina virtual do Linux* > **Redefinir remoto...**.
* Reinicie a VM. No [Portal do Azure](https://portal.azure.com), selecione **Procurar** > **Máquinas virtuais (clássicas)** > *sua máquina virtual do Linux* > **Reiniciar**.
  
    -OU-
  
    No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **Máquinas virtuais** > **Instâncias** > **Reiniciar**.
* Reimplante a VM em um novo nó do Azure. Para obter informações sobre como fazer isso, consulte [Reimplantar Máquina Virtual em um novo nó do Azure](virtual-machines-windows-redeploy-to-new-node.md).
  
    Observe que, após a conclusão dessa operação, os dados de disco efêmero serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
* Siga as instruções em [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-classic-reset-access.md) para:
  
  * Redefinir a senha ou a chave SSH.
  * Criar uma nova conta de usuário *sudo*.
  * Redefinir a configuração de SSH.
* Verifique se há problemas de plataforma na integridade do recurso da VM.<br> Selecione **Procurar** > **Máquinas Virtuais (clássicas)** > *sua máquina virtual do Linux* > **Configurações** > **Verificar Integridade**.

## Recursos adicionais
* Se ainda não puder se conectar com SSH à VM após seguir essas etapas, você poderá seguir [etapas de solução de problemas mais detalhadas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) para analisar as configurações de rede adicionais e etapas para resolver o problema.
* Para obter mais informações sobre como solucionar problemas de acesso do aplicativo, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-linux-troubleshoot-app-connection.md)
* Para obter mais informações sobre como solucionar problemas de máquinas virtuais que foram criadas usando o modelo de implantação clássico, consulte [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-classic-reset-access.md).

<!---HONumber=AcomDC_0803_2016-->