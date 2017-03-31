---
title: "Usar a Área de Trabalho Remota para uma VM do Linux no Azure | Microsoft Docs"
description: "Saiba como instalar e configurar a Área de Trabalho Remota (xrdp) para conectar-se a uma VM do Linux no Azure usando ferramentas gráficas"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1b8ad526ee59bd1547cbd7f4a208720fb6557fc2
ms.lasthandoff: 03/27/2017


---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalar e configurar a Área de Trabalho Remota para conectar-se uma VM do Linux no Azure
As VMs (máquinas virtuais) do Linux no Azure são normalmente gerenciadas a partir da linha de comando usando uma conexão SSH (secure shell). Para novos usuários Linux, ou para cenários de solução rápida de problemas, o uso da área de trabalho remota pode ser mais fácil. Este artigo fornece detalhes sobre como instalar e configurar um ambiente de área de trabalho ([xfce](https://www.xfce.org)) e área de trabalho remota ([xrdp](http://www.xrdp)) para sua VM do Linux usando o modelo de implantação do Resource Manager. Você também pode [executar essas etapas para VMs que usam o modelo de implantação Clássico](linux/classic/remote-desktop.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


## <a name="prerequisites"></a>Pré-requisitos
Este artigo exige uma VM do Linux no Azure. Se você precisar criar uma VM, use um dos seguintes métodos:

- A [CLI 2.0 do Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou a [CLI 1.0 do Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- O [Portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Você também precisa ter feito logon em uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).


## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha a base de dados de comandos para instalar e configurar a área de trabalho remota em sua VM. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, começando [aqui](#install-graphical-environment-on-linux-vm).

O exemplo a seguir instala o ambiente de área de trabalho leve [xfce4](https://www.xfce.org/) em uma VM do Ubuntu. Outras distribuições e ambientes de área de trabalho apresentam variações. Por exemplo, use **yum** para instalar o Red Hat Enterprise Linux e configurar as regras **selinux** apropriado, ou use **zypper** para instalar no SUSE.

SSH para sua VM. Instale o ambiente de área de trabalho xfce da seguinte maneira:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

Instale o xrdp da seguinte maneira:

```bash
sudo apt-get install xrdp
```

Configure xrdp para usar xfce como seu ambiente de área de trabalho da seguinte maneira:

```bash
echo xfce4-session >~/.xsession
```

Reinicie o serviço xrdp:

```bash
sudo service xrdp restart
```

Defina uma senha para sua conta de usuário se estiver usando atualmente apenas a chave SSH para autenticação:

```bash
sudo passwd ops
```

Saia da sessão do SSH em sua VM do Linux. Use a CLI do Azure em seu computador local para criar uma regra de grupo de segurança de rede para permitir o tráfego da área de trabalho remota. Use [az network nsg rule create](/cli/azure/network/nsg/rule#create) com a CLI 2.0 do Azure. O seguinte exemplo cria uma regra chamada `myNetworkSecurityGroupRule` no `myNetworkSecurityGroup` para permitir o tráfego na porta TCP 3389:
    
```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 3389 \
    --access allow
```

Ou com a CLI do Azure 1.0:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --destination-port-range 3389 --access allow
```

Conecte-se à sua VM do Linux usando seu cliente de área de trabalho remota preferido.

![Conectar-se ao xrdp usando seu cliente de Área de Trabalho Remota](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalar um ambiente de área de trabalho em sua VM do Linux
A maioria das VMs do Linux no Azure não tem um ambiente de área de trabalho instalado por padrão. As VMs do Linux são gerenciadas normalmente usando conexões SSH, em vez de um ambiente de área de trabalho. Há vários ambientes de área de trabalho no Linux para sua escolha. Dependendo de sua escolha de ambiente de área de trabalho, ele pode consumir de um a 2 GB de espaço em disco e demorar de cinco a 10 minutos para instalar e configurar todos os pacotes necessários.

O exemplo a seguir instala o ambiente de área de trabalho leve [xfce4](https://www.xfce.org/) em uma VM do Ubuntu. Os comandos para outras distribuições variam um pouco (use **yum** para instalar o Red Hat Enterprise Linux e configurar as regras **selinux** apropriadas, ou use **zypper** para instalar no SUSE, por exemplo).

Primeiro, SSH para sua VM. O exemplo a seguir conecta-se à VM chamada `myvm.westus.cloudapp.azure.com` com o nome de usuário `ops`:

```bash
ssh ops@myvm.westus.cloudapp.azure.com ~/.ssh/id_rsa.pub
```

Se você estiver usando o Windows e precisar de mais informações sobre como usar o SSH, veja [Como usar chaves do SSH com o Windows](virtual-machines-linux-ssh-from-windows.md).

Em seguida, instale o xfce usando `apt` da seguinte maneira:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalar e configurar um servidor de área de trabalho remoto
Agora que você tem um ambiente de área de trabalho instalado, configure um serviço de área de trabalho remoto para escutar as conexões de entrada. [xrdp](http://xrdp.org) é um servidor RDP (Protocolo de Área de Trabalho Remota) de código-fonte aberto que está disponível na maioria das distribuições Linux e funciona bem com xfce. Instale o xrdp em sua VM do Ubuntu da seguinte maneira:

```bash
sudo apt-get install xrdp
```

Diga ao xrdp o ambiente de área de trabalho a ser usado ao iniciar a sessão. Configure xrdp para usar xfce como seu ambiente de área de trabalho da seguinte maneira:

```bash
echo xfce4-session >~/.xsession
```

Reinicie o serviço xrdp para que as alterações entrem em vigor da seguinte maneira:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Definir a senha da conta de usuário local
Se você tiver criado uma senha para sua conta de usuário durante a criação de sua VM, ignore esta etapa. Se você usar apenas a autenticação de chave SSH e não tiver uma senha de conta local definida, especifique uma senha antes de usar o xrdp para fazer logon em sua VM. O xrdp não pode aceitar chaves SSH para autenticação. O exemplo a seguir especifica uma senha para a conta de usuário `ops`:

```bash
sudo passwd ops
```

> [!NOTE]
> A especificação de uma senha não atualiza sua configuração de sshd para permitir logons com senha, caso não permita no momento. De uma perspectiva de segurança, convém conectar-se à sua VM com um túnel SSH usando a autenticação baseada em chave e, depois, conectar-se ao xrdp. Nesse caso, ignore a etapa a seguir sobre a criação de uma regra de grupo de segurança de rede para permitir o tráfego de área de trabalho remota.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Criar uma regra de Grupo de Segurança de Rede para tráfego de Área de Trabalho Remota
Para permitir que o tráfego da Área de Trabalho Remota alcance sua VM do Linux, é necessário criar uma regra de grupo de segurança de rede que permite ao TCP na porta 3389 acessar sua VM. Para saber mais sobre grupos de segurança de rede, confira [O que é um Grupo de Segurança de Rede?](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Você também pode [usar o Portal do Azure para criar uma regra de grupo de segurança de rede](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Os exemplos a seguir criam uma regra de grupo de segurança de rede denominada `myNetworkSecurityGroupRule` para o tráfego de `allow` no `tcp` na porta `3389`.

- Use [az network nsg rule create](/cli/azure/network/nsg/rule#create) com a CLI 2.0 do Azure:
    
    ```azurecli
    az network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 3389 \
        --access allow
    ```

- Ou use a CLI do Azure 1.0:

    ```azurecli
    azure network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --destination-port-range 3389 --access allow
    ```

## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Conectar-se a sua VM do Linux com um cliente de Área de Trabalho Remota
Abra o cliente da área de trabalho remota local e conecte-se ao endereço IP ou nome DNS de sua VM do Linux. Insira o nome de usuário e a senha da conta de usuário em sua VM da seguinte maneira:

![Conectar-se ao xrdp usando seu cliente de Área de Trabalho Remota](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

Após a autenticação, o ambiente de área de trabalho xfce carregará e será semelhante ao exemplo a seguir:

![Ambiente de área de trabalho xfce por meio de xrdp](./media/virtual-machines-linux-use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Solucionar problemas
Se você não puder se conectar à sua VM do Linux usando um cliente de Área de Trabalho Remota, use `netstat` em sua VM do Linux para verificar se sua VM está escutando conexões RDP da seguinte maneira:

```bash
sudo netstat -plnt | grep rdp
```

O exemplo a seguir mostra a VM escutando na porta TCP 3389, conforme o esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Se o serviço xrdp não estiver escutando, em uma VM do Ubuntu, reinicie o serviço da seguinte maneira:

```bash
sudo service xrdp restart
```

Examine os logs `/var/log` em sua VM do Ubuntu para obter indicações sobre o motivo de o serviço não estar respondendo. Você também pode monitorar o syslog durante uma tentativa de conexão de área de trabalho remota para exibir quaisquer erros:

```bash
tail -f /var/log/syslog
```

Outras distribuições do Linux como Red Hat Enterprise Linux e SUSE podem ter maneiras diferentes de reiniciar serviços e locais alternativos de arquivos de log para examinar.

Se você não receber nenhuma resposta em seu cliente de área de trabalho remota e não ver quaisquer eventos no log do sistema, esse comportamento indicará que o tráfego da área de trabalho remota não consegue alcançar a VM. Examine suas regras de grupo de segurança de rede para garantir que você tenha uma regra para permitir o TCP na porta 3389. Para saber mais, veja [Solucionar problemas de conectividade do aplicativo](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como criar e usar chaves SSH com VMs do Linux, veja [Criar chaves SSH para VMs do Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para saber mais sobre como usar o SSH do Windows, veja [Como usar chaves SSH com o Windows](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


