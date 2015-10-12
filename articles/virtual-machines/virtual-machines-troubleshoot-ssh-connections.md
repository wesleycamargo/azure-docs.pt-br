<properties
	pageTitle="Não é possível se conectar a uma VM do Azure por SSH | Microsoft Azure"
	description="Solucione problemas de conexões SSH (Secure Shell) de uma máquina virtual do Azure baseada em Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="dkshir"/>

# Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo abrange conexões SSH de solução de problemas em uma máquina virtual criada com o modelo de implantação clássica ou o modelo de implantação do Gerenciador de Recursos.


Pode haver várias causas de falhas de SSH para uma máquina virtual do Azure baseada em Linux. Este artigo ajudará você a descobrir as causas e corrigir as falhas.

> [AZURE.NOTE]Este artigo aplica-se somente às máquinas virtuais do Azure que executam o Linux. Para solucionar problemas de conexões às máquinas virtuais do Azure que executam o Windows, confira [este artigo](virtual-machines-troubleshoot-remote-desktop-connections.md).

## Contate o Suporte ao Cliente do Azure

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](http://azure.microsoft.com/support/forums/).

Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para obter informações sobre como usar o Suporte do Azure, leia as [Perguntas Frequentes de Suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Etapas básicas

Tente realizar estas etapas para resolver as falhas mais comuns de conexão SSH:

1. Redefina o Acesso Remoto no [portal do Azure](https://portal.azure.com). Clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Windows > **Redefinir Acesso Remoto**.

	![Redefinir Acesso Remoto](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. Reinicie a máquina virtual. No [portal de visualização do Azure](https://portal.azure.com), clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Windows > **Reiniciar**. No [portal de gerenciamento do Azure](https://manage.windowsazure.com), abra as **Máquinas virtuais** > **Instâncias** e clique em **Reiniciar**.

3. [Redimensione a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

4. Siga as instruções em [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) na máquina virtual, para:

	- Redefinir a senha ou a chave SSH.
	- Criar uma nova conta de usuário sudo.
	- Redefinir a configuração de SSH.

## Solução de problemas detalhada

Se o cliente SSH ainda não conseguir acessar o serviço SSH na máquina virtual, isso pode ser devido a várias causas. Estes são os componentes envolvidos.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

As seções a seguir ajudarão você a identificar a origem da falha e encontrar soluções ou soluções alternativas.

### Etapas antes da solução de problemas

Primeiro, verifique o status da máquina virtual no portal do Azure.

No [portal de gerenciamento do Azure](https://manage.windowsazure.com):

1. Clique em **Máquinas virtuais** > *Nome da VM*.
2. Clique no **Painel** da VM para verificar seu status.
3. Clique em **Monitor** para ver a atividade recente dos recursos de computação, armazenamento e rede.
4. Clique em **Pontos de extremidade** para garantir que haja um ponto de extremidade para o tráfego SSH.

No [portal de visualização do Azure](https://portal.azure.com):

1. Clique em **Procurar** > **Máquinas virtuais** > *Nome da VM*. Para uma máquina virtual criada no Gerenciador de Recursos do Azure, clique em **Procurar** > **Máquinas virtuais (v2)** > *Nome da VM*. O painel de status da máquina virtual deve mostrar **Executando**. Role para baixo para mostrar a atividade recente dos recursos de computação, armazenamento e rede.
2. Clique em **Configurações** para examinar os pontos de extremidade, os endereços IP e outras configurações.

Para verificar a conectividade de rede, verifique os pontos de extremidade configurados e se você pode acessar a VM por meio de outro protocolo, como HTTP ou outro serviço.

Após essas etapas, tente estabelecer novamente a conexão SSH.


### Etapas para solucionar problemas

O cliente SSH no computador poderá não conseguir acessar o serviço SSH na máquina virtual do Azure devido a essas possíveis fontes de problemas ou configurações incorretas:

- Computador cliente de SSH
- Dispositivo de borda da organização
- Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)
- Grupos de segurança de rede
- Máquina virtual baseada no Linux

#### Fonte 1: computador cliente de SSH

Para que o seu computador deixe de ser a fonte da falha, verifique se ele pode estabelecer conexões SSH com outro computador local baseado em Linux.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Se isso não for possível, verifique se há o seguinte em seu computador:

- Uma configuração de firewall local que está bloqueando o tráfego SSH de entrada ou de saída (TCP 22)
- Um software de proxy cliente instalado localmente que está impedindo conexões de SSH
- Um software de monitoramento de rede instalado localmente que está impedindo conexões SSH
- Outros tipos de software de segurança que monitoram o tráfego ou que permitem/não permitem tipos específicos de tráfego

Em todos esses casos, tente desabilitar temporariamente o software e tente estabelecer uma conexão SSH com um computador local para descobrir a causa. Em seguida, trabalhe com o administrador de rede para corrigir as configurações do software e permitir as conexões SSH.

Se você estiver usando autenticação de certificado, verifique se tem essas permissões para a pasta .ssh em seu diretório base:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (ou quaisquer outros arquivos que têm suas chaves privadas armazenadas)
- Chmod 644 ~/.ssh/known\_hosts (contém os hosts aos quais você se conectou via SSH)

#### Fonte 2: dispositivo de borda da organização

Para que o dispositivo de borda de sua organização deixe de ser a fonte da falha, verifique se um computador conectado diretamente à Internet pode estabelecer conexões SSH com sua VM do Azure. Se estiver acessando a VM em uma conexão VPN site a site ou de Rota Expressa, vá para [Fonte 4: grupos de segurança de rede](#nsg).

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Se não tiver um computador que esteja diretamente conectado à Internet, você poderá criar facilmente uma nova máquina virtual do Azure em seu próprio grupo de recursos ou serviço de nuvem e usá-la. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](virtual-machines-linux-tutorial.md). Exclua o grupo de recursos ou a máquina virtual e o serviço de nuvem ao concluir o teste.

Se você puder criar uma conexão SSH com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda da organização:

- Um firewall interno que está bloqueando o tráfego SSH com a Internet
- Seu servidor proxy que está impedindo conexões SSH
- Software de detecção de invasão ou de monitoramento de rede em execução em dispositivos em sua rede de borda e que está impedindo conexões SSH

Trabalhe com o administrador da rede para corrigir as configurações dos dispositivos de borda de sua organização a fim de permitir o tráfego SSH com a Internet.

#### Fonte 3: ponto de extremidade de serviço de nuvem e ACL

Para que o ponto de extremidade de serviço de nuvem e uma ACL deixem de ser a fonte da falha, para as VMs criadas com o [modelo de implantação clássico](../resource-manager-deployment-model.md), verifique que outra VM do Azure na mesma rede virtual pode fazer conexões SSH com a sua VM.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Para as máquinas virtuais criadas com o Gerenciador de Recursos, vá para [fonte 4: grupos de segurança de rede](#nsg).

Se não houver outra VM na mesma rede virtual, você poderá facilmente criar uma nova. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](virtual-machines-linux-tutorial.md). Exclua a VM extra ao concluir o teste.

Se for possível criar uma conexão SSH com uma VM na mesma rede virtual, verifique:

- A configuração do ponto de extremidade para o tráfego SSH na VM de destino. A porta TCP privada do ponto de extremidade deve corresponder à porta TCP na qual o serviço SSH na VM está escutando (o padrão é 22). Para as VMs criadas no modelo de implantação do Gerenciador de Recursos com o uso de modelos, verifique o número da porta SSH TCP no portal de visualização do Azure em **Procurar** > **Máquinas virtuais (v2)** > *Nome da VM* > **Configurações** > **Pontos de Extremidade**.
- A ACL para o ponto de extremidade de tráfego de SSH na máquina virtual de destino. As ACLs permitem que você especifique tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de SSH para o ponto de extremidade. Examine suas ACLs para verificar se o tráfego de entrada dos endereços IP públicos de seu proxy ou de outro servidor de borda é permitido. Para obter mais informações, veja [Sobre ACLs (listas de controle de acesso) de rede](../virtual-network/virtual-networks-acl.md).

Para que o ponto de extremidade deixe de ser a fonte do problema, remova o ponto de extremidade atual e crie um novo, especificando o nome **SSH** (porta TCP 22 como o número da porta pública e privada). Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Fonte 4: grupos de segurança de rede

Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Examine as regras do grupo de segurança de rede para verificar se o tráfego SSH de entrada e saída da Internet é permitido. Para obter mais informações, veja [Sobre grupos de segurança de rede](../traffic-manager/virtual-networks-nsg.md).

#### Fonte 5: máquina virtual do Azure baseada no Linux

A última fonte possível de problemas é a própria máquina virtual do Azure.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Caso ainda não tenha feito isso, siga as instruções em [como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) na máquina virtual.

Tente se conectar novamente do seu computador. Se você ainda não conseguir, estes são alguns dos possíveis problemas:

- O serviço SSH não está em execução na máquina virtual de destino.
- O serviço SSH não está escutando na porta TCP 22. Para testar isso, instale um cliente telnet no computador local e execute "telnet *nomedoServiçoDeNuvem*.cloudapp.net 22". Isso descobrirá se a máquina virtual permite a comunicação de entrada e de saída para o ponto de extremidade do SSH.
- O firewall local na máquina virtual de destino tem regras que estão impedindo o tráfego SSH de entrada ou de saída.
- Um software de detecção de invasão ou de monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de SSH.


## Recursos adicionais

[Como redefinir uma senha ou SSH para máquinas virtuais Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO1-->