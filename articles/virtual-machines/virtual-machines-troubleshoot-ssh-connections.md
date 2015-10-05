<properties
	pageTitle="Solucionar problemas de conexões SSH em uma VM do Linux | Microsoft Azure"
	description="Se você não conseguir conectar sua máquina virtual do Azure baseada em Linux, use essas etapas para isolar a origem do problema."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="dkshir"/>

# Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo abrange conexões SSH de solução de problemas em uma máquina virtual criada com o modelo de implantação clássica ou o modelo de implantação do Gerenciador de Recursos.

Se você não puder se conectar a máquinas virtuais do Azure baseadas em Linux, este artigo descreve uma abordagem metódica para isolar e corrigir o problema.

## Etapa 1: redefinir a configuração, chave de SSH ou senha

Siga as instruções em [Como redefinir uma senha ou SSH para máquinas virtuais Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) na máquina virtual. Com essas instruções, você pode:

- Redefinir a senha ou a chave SSH.
- Criar uma nova conta de usuário sudo.
- Redefinir a configuração de SSH.

Se o cliente SSH ainda não puder acessar o serviço SSH na máquina virtual, pode haver muitas causas. Aqui está o conjunto de componentes envolvidos.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

As seções a seguir explicam como isolar e determinar as várias causas para esse problema e fornecem soluções e soluções alternativas.

## Etapa 2: etapas preliminares antes da solução de problemas detalhada

Primeiramente, verifique o status da máquina virtual no portal de gerenciamento do Azure ou no portal de visualização do Azure.

No portal de gerenciamento do Azure:

1. Clique em **Máquinas virtuais** > *Nome da VM*.
2. Clique no **Painel** da máquina virtual para verificar seu status.
3. Clique em **Monitor** para ver a atividade recente dos recursos de computação, armazenamento e rede.
4. Clique em **Pontos de extremidade** para garantir que haja um ponto de extremidade para o tráfego SSH.

No portal de visualização do Azure:

1. Clique em **Procurar** > **Máquinas virtuais** > *Nome da VM*. Para uma máquina virtual criada no Gerenciador de Recursos do Azure, clique em **Procurar** > **Máquinas virtuais (v2)** > *Nome da VM*. O painel de status da máquina virtual deve mostrar **Executando**. Role para baixo para mostrar a atividade recente dos recursos de computação, armazenamento e rede.
2. Clique em **Configurações** para examinar os pontos de extremidade, os endereços IP e outras configurações.

Para verificar a conectividade de rede, analise os pontos de extremidade configurados e determine se você pode acessar a máquina virtual por meio de outro protocolo, como HTTP ou outro serviço conhecido.

Se necessário, reinicie ou [redimensione a máquina virtual](virtual-machines-size-specs.md).

Após essas etapas, tente estabelecer novamente a conexão SSH.

## Etapa 3: solução de problemas detalhada

A incapacidade de seu cliente SSH acessar o serviços SSH na máquina virtual do Azure pode ser causada pelas seguintes fontes de problema ou configurações incorretas:

- Computador cliente de SSH
- Dispositivo de borda da organização
- Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)
- Grupos de segurança de rede
- Máquina virtual baseada no Linux

### Fonte 1: computador cliente de SSH

Para que seu computador deixe de ser a fonte dos problemas ou erros de configuração, verifique se o computador pode estabelecer conexões SSH com outro computador local baseado no Linux.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Se isso não for possível, verifique se há o seguinte em seu computador:

- Uma configuração de firewall local que está bloqueando o tráfego SSH de entrada ou de saída (TCP 22)
- Um software de proxy cliente instalado localmente que está impedindo conexões de SSH
- Um software de monitoramento de rede instalado localmente que está impedindo conexões SSH
- Outros tipos de software de segurança que monitoram o tráfego ou permitem/não permitem tipos específicos de tráfego e que estão impedindo conexões SSH

Em todos esses casos, tente desabilitar temporariamente o software e tente estabelecer uma conexão SSH com um computador local para determinar a causa. Em seguida, trabalhe com o administrador de rede para corrigir as configurações do software e permitir as conexões SSH.

Se você estiver usando autenticação de certificado, verifique se tem essas permissões para a pasta .ssh em seu diretório base:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (ou qualquer outro arquivo que armazene chaves privadas)
- Chmod 644 ~/.ssh/known\_hosts (contém os hosts aos quais você se conectou via SSH)

### Fonte 2: dispositivo de borda da organização

Para que o dispositivo de borda de sua organização deixe de ser a fonte dos problemas ou de erros de configuração, verifique se um computador conectado diretamente à Internet pode estabelecer conexões SSH com sua máquina virtual do Azure. Se você estiver acessando a máquina virtual em uma conexão VPN site a site ou de Rota Expressa, vá para [Fonte 4: grupos de segurança de rede](#nsg).

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Se não tiver um computador que esteja diretamente conectado à Internet, você poderá criar facilmente uma nova máquina virtual do Azure em seu próprio grupo de recursos ou serviço de nuvem e usá-la. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](virtual-machines-linux-tutorial.md). Exclua o grupo de recursos ou a máquina virtual e o serviço de nuvem ao concluir o teste.

Se você puder criar uma conexão SSH com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda da organização:

- Um firewall interno que está bloqueando o tráfego SSH com a Internet
- Seu servidor proxy que está impedindo conexões SSH
- Software de detecção de invasão ou de monitoramento de rede em execução em dispositivos em sua rede de borda e que está impedindo conexões SSH

Trabalhe com o administrador da rede para corrigir as configurações dos dispositivos de borda de sua organização a fim de permitir o tráfego SSH com a Internet.

### Fonte 3: ponto de extremidade de serviço de nuvem e ACL

Para eliminar o ponto de extremidade do serviço de nuvem e a ACL como a fonte dos problemas ou má configuração das máquinas virtuais criadas na API de Gerenciamento de Serviços, verifique se outra máquina virtual do Azure, que esteja na mesma rede virtual, pode estabelecer conexões SSH com sua máquina virtual do Azure.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Para máquinas virtuais criadas no Gerenciador de Recursos do Azure, pule para [Fonte 4: grupos de segurança de rede](#nsg).

Se não houver outra máquina virtual na mesmo rede virtual, você poderá criar facilmente uma nova. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](virtual-machines-linux-tutorial.md). Exclua a máquina virtual extra ao concluir o teste.

Se você puder criar uma conexão SSH com uma máquina virtual na mesma rede virtual, verifique:

- A configuração de ponto de extremidade para o tráfego de SSH na máquina virtual de destino. A porta TCP privada do ponto de extremidade deve corresponder à porta TCP na qual o serviço SSH na máquina virtual está escutando, que, por padrão, é 22. Para máquinas virtuais criadas no Gerenciador de Recursos do Azure usando modelos, verifique o número da porta SSH TCP no portal de visualização do Azure em **Procurar** > **Máquinas virtuais (v2)** > *Nome da VM* > **Configurações** > **Pontos de Extremidade**.
- A ACL para o ponto de extremidade de tráfego de SSH na máquina virtual de destino. As ACLs permitem que você especifique tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de SSH para o ponto de extremidade. Examine suas ACLs para verificar se o tráfego de entrada dos endereços IP públicos de seu proxy ou outro servidor de borda é permitido. Para obter mais informações, consulte [Sobre ACLs (listas de controle de acesso) de rede](../virtual-network/virtual-networks-acl.md).

Para que o ponto de extremidade deixe de ser a fonte do problema, remova o ponto de extremidade atual e crie um novo, especificando o nome **SSH** (porta TCP 22 como o número de porta pública e privada). Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](virtual-machines-set-up-endpoints.md).

### <a id="nsg"></a>Fonte 4: grupos de segurança de rede

Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Examine as regras do grupo de segurança de rede para verificar se o tráfego de entrada e saída de SSH da Internet é permitido. Para obter mais informações, consulte [Sobre grupos de segurança de rede](../traffic-manager/virtual-networks-nsg.md).

### Fonte 5: máquina virtual do Azure baseada no Linux

A última fonte possível de problemas é a própria máquina virtual do Azure.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Caso ainda não tenha feito isso, siga as instruções em [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) na máquina virtual.

Tente estabelecer a conexão de seu computador novamente. Se você não tiver êxito, estes são alguns dos problemas possíveis:

- O serviço SSH não está em execução na máquina virtual de destino.
- O serviço SSH não está escutando na porta TCP 22. Para testar isso, instale um cliente telnet no computador local e execute "telnet *nomedoServiçoDeNuvem*.cloudapp.net 22". Isso determinará se a máquina virtual permite a comunicação de entrada e saída para o ponto de extremidade do SSH.
- O firewall local na máquina virtual de destino tem regras que estão impedindo o tráfego SSH de entrada ou de saída.
- Um software de detecção de invasão ou de monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de SSH.


## Etapa 4: enviar seu problema aos fóruns de suporte do Azure

Para obter ajuda de especialistas do Azure do mundo inteiro, você pode enviar seu problema aos fóruns do Azure do MSDN ou do Stack Overflow. Consulte [Fóruns do Microsoft Azure](http://azure.microsoft.com/support/forums/) para obter mais informações.

## Etapa 5: enviar um incidente de suporte do Azure

Se você tiver concluído as etapas de 1 a 4 deste artigo e enviado seu problema para os fóruns de suporte do Azure, mas ainda não conseguir criar uma conexão SSH, uma alternativa é a possibilidade de recriar a máquina virtual.

Se você não puder recriar a máquina virtual, talvez seja hora de enviar um incidente de suporte do Azure.

Para registrar um incidente, vá para o [Site de Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

Para obter informações sobre como usar o Suporte do Azure, consulte as [Perguntas frequentes de suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Recursos adicionais

[Como redefinir uma senha ou SSH para máquinas virtuais Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Sept15_HO4-->