<properties
	pageTitle="Solução de problemas de SSH detalhada para uma VM do Azure | Microsoft Azure"
	description="Etapas da solução de problemas de SSH mais detalhada para problemas de conexão para uma máquina virtual do Azure"
	keywords="conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="06/16/2016"
	ms.author="iainfou"/>

# Etapas detalhadas de solução de problemas de SSH

Há muitas razões possíveis para que o cliente de SSH não possa acessar o serviço SSH na VM. Se tiver seguido as [etapas geral de solução de problemas de SSH](virtual-machines-linux-troubleshoot-ssh-connection.md), será necessário solucionar o problema de conexão. Este artigo guiará você pelas etapas detalhadas de solução de problemas para determinar onde a conexão SSH está falhando e como resolver isso.

## Realizar etapas preliminares

O diagrama a seguir mostra os componentes que estão envolvidos.

![Diagrama que mostra os componentes de serviço SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

As etapas a seguir ajudarão você a isolar a origem da falha e encontrar soluções ou soluções alternativas.

Primeiro, verifique o status da VM no portal.

No [Portal Clássico do Azure](https://manage.windowsazure.com), para VMs que foram criadas usando o modelo de implantação clássico:

1. Selecione **Máquinas virtuais** > *Nome da VM*.
2. Selecione o **Painel** da VM para verificar o status.
3. Selecione **Monitor** para ver a atividade recente dos recursos de computação, armazenamento e rede.
4. Selecione **Pontos de extremidade** para garantir que haja um ponto de extremidade para o tráfego SSH.

No [portal do Azure](https://portal.azure.com):

1. Para VMs criadas usando o modelo de implantação clássico, selecione **Procurar** > **Máquinas virtuais (clássicas)** > *Nome da VM*.

	-OU-

	Para VMs criadas usando o modelo do Resource Manager, selecione **Procurar** > **Máquinas virtuais** > *Nome da VM*.

	O painel de status da VM deve mostrar **Executando**. Role para baixo para mostrar a atividade recente dos recursos de computação, armazenamento e rede.

2. Selecione **Configurações** para examinar os pontos de extremidade, os endereços IP e outras configurações.

	Para identificar pontos de extremidade em VMs que foram criadas usando o Resource Manager, verifique se um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) foi definido. Verifique também se as regras foram aplicadas ao grupo de segurança de rede e se elas são referenciadas na sub-rede.

Para verificar a conectividade de rede, verifique os pontos de extremidade configurados e se você pode acessar a VM por meio de outro protocolo, como HTTP ou outro serviço.

Após essas etapas, tente estabelecer novamente a conexão SSH.


## Descobrir a origem do problema

O cliente SSH no computador poderá não conseguir acessar o serviço SSH na VM do Azure devido aos problemas ou configurações incorretas a seguir:

- Computador cliente de SSH
- Dispositivo de borda da organização
- Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)
- Grupos de segurança de rede
- VM baseada em Linux no Azure

## Fonte 1: computador cliente de SSH

Para que o seu computador deixe de ser a fonte da falha, verifique se ele pode estabelecer conexões SSH com outro computador local baseado em Linux.

![Diagrama que realça os componentes do computador cliente SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se isso não for possível, verifique se há o seguinte em seu computador:

- Uma configuração de firewall local que está bloqueando o tráfego SSH de entrada ou de saída (TCP 22)
- Um software de proxy cliente instalado localmente que está impedindo conexões de SSH
- Um software de monitoramento de rede instalado localmente que está impedindo conexões SSH
- Outros tipos de software de segurança que monitoram o tráfego ou que permitem/não permitem tipos específicos de tráfego

Se uma das seguintes condições se aplicar, desabilite temporariamente o software e tente novamente uma conexão SSH em um computador local para descobrir o motivo pelo qual a conexão está sendo bloqueada no seu computador. Em seguida, trabalhe com o administrador de rede para corrigir as configurações do software e permitir as conexões SSH.

Se você estiver usando autenticação de certificado, verifique se tem essas permissões para a pasta .ssh em seu diretório base:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (ou quaisquer outros arquivos que têm suas chaves privadas armazenadas)
- Chmod 644 ~/.ssh/known\_hosts (contém os hosts aos quais você se conectou via SSH)

## Fonte 2: dispositivo de borda da organização

Para que o dispositivo de borda de sua organização deixe de ser a fonte da falha, verifique se um computador conectado diretamente à Internet pode estabelecer conexões SSH a sua VM do Azure. Se estiver acessando a VM em uma conexão VPN site a site ou de Rota Expressa do Azure, vá para [Fonte 4: grupos de segurança de rede](#nsg).

![Diagrama que realça o dispositivo de borda da organização](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se não tiver um computador que esteja diretamente conectado à Internet, você poderá criar facilmente uma nova VM do Azure em seu próprio grupo de recursos ou serviço de nuvem e usá-la. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](virtual-machines-linux-quick-create-cli.md). Exclua o grupo de recursos ou a VM e o serviço de nuvem ao concluir o teste.

Se você puder criar uma conexão SSH com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda da organização:

- Um firewall interno que está bloqueando o tráfego SSH com a Internet
- Um servidor proxy que está impedindo conexões SSH
- Software de detecção de invasão ou de monitoramento de rede em execução em dispositivos em sua rede de borda que está impedindo conexões SSH

Trabalhe com o administrador da rede para corrigir as configurações dos dispositivos de borda de sua organização a fim de permitir o tráfego SSH com a Internet.

## Fonte 3: ponto de extremidade de serviço de nuvem e ACL

> [AZURE.NOTE] Essa fonte aplica-se somente às VMs que foram criadas usando o modelo de implantação clássico. Para as VMs criadas com o Resource Manager, vá para [fonte 4: grupos de segurança de rede](#nsg).

Para que o ponto de extremidade de serviço de nuvem e uma ACL deixem de ser a fonte da falha, verifique que outra VM do Azure na mesma rede virtual possa fazer conexões SSH com a sua VM.

![Diagrama que realça a ACL e o ponto de extremidade do serviço de nuvem](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se não houver outra VM na mesma rede virtual, você poderá facilmente criar uma nova. Para obter mais informações, consulte [Criar uma VM do Linux no Azure usando a CLI](virtual-machines-linux-quick-create-cli.md). Exclua a VM extra ao concluir o teste.

Se for possível criar uma conexão SSH com uma VM na mesma rede virtual, verifique o seguinte:

- **A configuração do ponto de extremidade para o tráfego SSH na VM de destino.** A porta TCP particular do ponto de extremidade deve corresponder à porta TCP na qual o serviço SSH na VM está escutando. (A porta padrão é 22). Para as VMs criadas no modelo de implantação do Resource Manager usando modelos, verifique o número da porta SSH TCP no Portal do Azure em **Procurar** > **Máquinas virtuais (v2)** > *Nome da VM* > **Configurações** > **Pontos de extremidade**.

- **A ACL para o ponto de extremidade de tráfego de SSH na máquina virtual de destino.** Uma ACL permite que você especifique tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de SSH para o ponto de extremidade. Verifique suas ACLs para assegurar que o tráfego de entrada dos endereços IP públicos de seu proxy ou de outro servidor de borda é permitido. Para obter mais informações, veja [Sobre ACLs (listas de controle de acesso) de rede](../virtual-network/virtual-networks-acl.md).

Para que o ponto de extremidade deixe de ser a fonte do problema, remova o ponto de extremidade atual, crie um novo e especifique o nome do SSH (porta TCP 22 como o número da porta pública e privada). Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## Fonte 4: grupos de segurança de rede

Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Examine as regras do grupo de segurança de rede para verificar se o tráfego SSH de entrada e saída da Internet é permitido. Para saber mais, confira [Sobre grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

## Fonte 5: máquina virtual do Azure baseada no Linux

A última fonte possível de problemas é a própria máquina virtual do Azure.

![Diagrama que realça uma VM do Azure baseada em Linux](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se você ainda não fez isso, siga as instruções [para redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-classic-reset-access.md).

Tente se conectar novamente do seu computador. Caso ainda não consiga, estes são alguns dos possíveis problemas:

- O serviço SSH não está em execução na máquina virtual de destino.
- O serviço SSH não está escutando na porta TCP 22. Para testar isso, instale um cliente telnet no computador local e execute "telnet *nomedoServiçoDeNuvem*.cloudapp.net 22". Isso determinará se a máquina virtual permite a comunicação de entrada e saída para o ponto de extremidade do SSH.
- O firewall local na máquina virtual de destino tem regras que estão impedindo o tráfego SSH de entrada ou de saída.
- Um software de detecção de invasão ou de monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de SSH.


## Recursos adicionais
Para obter mais informações sobre como solucionar problemas de acesso do aplicativo, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0622_2016-->