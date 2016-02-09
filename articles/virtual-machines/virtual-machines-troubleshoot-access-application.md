<properties
	pageTitle="Solucionar problemas de acesso do aplicativo em uma VM | Microsoft Azure"
	description="Se você não conseguir acessar um aplicativo em execução em uma máquina virtual do Azure, use essas etapas para isolar a origem do problema."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="dkshir"/>

# Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Se você não conseguir acessar um aplicativo em execução em uma máquina virtual do Azure, este artigo descreve uma abordagem metódica para isolar a origem do problema e corrigi-lo.

> [AZURE.NOTE]  Para obter ajuda para se conectar a uma máquina virtual do Azure, consulte [Solucionar problemas de conexões de Área de Trabalho Remota para uma Máquina Virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md) ou [Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](virtual-machines-troubleshoot-ssh-connections.md).

Há quatro áreas principais nas quais é possível solucionar problemas de acesso de um aplicativo que está sendo executado em uma máquina virtual do Azure.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access1.png)

1.	O aplicativo em execução na máquina virtual do Azure.
2.	A máquina virtual do Azure.
3.	Pontos de extremidade do Azure para o serviço de nuvem que contém a máquina virtual (para máquinas virtuais criadas usando a API de Gerenciamento de Serviços), regras NAT de entrada (para máquinas virtuais criadas no Gerenciador de Recursos) e Grupos de Segurança de Rede.
4.	Seu dispositivo de borda da Internet.

Para computadores cliente que acessam o aplicativo em uma conexão VPN site a site ou da Rota Expressa, as principais áreas que podem causar problemas são o aplicativo e a máquina virtual do Azure. Para determinar a origem do problema e sua correção, siga estas etapas.

## Etapa 1: Você consegue acessar o aplicativo na máquina virtual de destino?

Tente acessar o aplicativo com o programa cliente apropriado na máquina virtual na qual o aplicativo está em execução. Use o nome de host local, o endereço IP local ou o endereço de loopback (127.0.0.1).

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access2.png)

Por exemplo, se o aplicativo for um servidor Web, execute um navegador na máquina virtual e tente acessar uma página da Web hospedada na máquina virtual.

Se você conseguir acessar o aplicativo, vá para a [Etapa 2](#step2).

Se não conseguir acessar o aplicativo, verifique o seguinte:

- O aplicativo está em execução na máquina virtual de destino.
- O aplicativo está escutando nas portas TCP e UDP esperadas.

Em máquinas virtuais baseadas em Linux e Windows, use o comando **netstat -a** para mostrar as portas de escuta ativas. Examine a saída para as portas esperadas no qual seu aplicativo deve estar escutando. Reinicie o aplicativo ou configure-o para usar as portas esperadas, conforme necessário.

## <a id="step2"></a>Etapa 2: você consegue acessar o aplicativo em outra máquina virtual na mesma rede virtual?

Tente acessar o aplicativo por meio de uma máquina virtual diferente na mesma rede virtual da máquina virtual em que o aplicativo está sendo executado usando o nome do host da máquina virtual ou seu endereço IP do provedor, público ou provado atribuído pelo Azure. Para máquinas virtuais criadas usando a API de Gerenciamento de Serviços, não use o endereço IP público do serviço de nuvem.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access3.png)

Por exemplo, se o aplicativo for um servidor Web, tente acessar uma página da Web em um navegador em uma máquina virtual diferente da mesma rede virtual.

Se você conseguir acessar o aplicativo, vá para a [Etapa 3](#step3).

Se não conseguir acessar o aplicativo, verifique o seguinte:

- O firewall do host na máquina virtual de destino está permitindo o tráfego de solicitação de entrada e de resposta de saída.
- O software de detecção de invasão ou de monitoramento de rede em execução na máquina virtual de destino está permitindo o tráfego.
- Grupos de segurança de rede estão permitindo o tráfego.
- Um componente separado em execução em sua rede virtual no caminho entre a máquina virtual de teste e a máquina virtual, como um balanceador de carga ou firewall, está permitindo o tráfego.

Em uma máquina virtual baseada no Windows, use o Firewall do Windows com Segurança avançada para determinar se as regras de firewall excluem o tráfego de entrada e de saída do seu aplicativo.

## <a id="step3"></a>Etapa 3: você consegue acessar o aplicativo em um computador que está fora da rede virtual, mas não conectado à mesma rede que o seu computador?

Tente acessar o aplicativo em um computador fora da rede virtual em que está a máquina virtual na qual o aplicativo está sendo executado, mas que não esteja na mesma rede que o computador cliente original.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access4.png)

Por exemplo, se o aplicativo for um servidor Web, tente acessar uma página da Web em um navegador em execução em um computador que não está na rede virtual.

Se não conseguir acessar o aplicativo, verifique o seguinte:

- Para máquinas virtuais criadas usando a API de Gerenciamento de Serviços, cuja configuração do ponto de extremidade da máquina virtual está permitindo tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta pública e privada. Para obter mais informações, confira [Como configurar pontos de extremidade para uma Máquina Virtual](virtual-machines-set-up-endpoints.md)
- Para máquinas virtuais criadas usando a API de Gerenciamento de Serviços, as ACLs (Listas de Controle de Acesso) no ponto de extremidade não estão impedindo o tráfego de entrada da Internet. Para obter mais informações, confira [Como configurar pontos de extremidade para uma Máquina Virtual](virtual-machines-set-up-endpoints.md)
- Para máquinas virtuais criadas no Gerenciador de Recursos, que a configuração da regra NAT de entrada da máquina virtual está permitindo o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta pública e privada.
- Se os Grupos de segurança de rede permitem o tráfego de saída de respostar e de entrada de solicitações. Para obter mais informações, consulte [O que é um NSG (Grupo de Segurança de Rede)?](virtual-networks-nsg.md).

Se a máquina virtual ou ponto de extremidade for um membro de um conjunto com balanceamento de carga:

- Verifique se o protocolo de teste (TCP ou UDP) e o número da porta estão corretos.
- Se a porta e protocolo de teste forem diferentes do protocolo e da porta configurados com balanceamento de carga:
	- Verifique se o aplicativo está escutando no protocolo de investigação (TCP ou UDP) e no número da porta (use **netstat –a** na máquina virtual de destino).
	- O firewall do host na máquina virtual de destino está permitindo o tráfego de solicitação de teste entrada e de resposta de teste de saída.

Se você puder acessar o aplicativo, certifique-se de que seu dispositivo de borda de Internet esteja permitindo:

- O tráfego de solicitação de saída do aplicativo no computador cliente para a máquina virtual do Azure.
- O tráfego de resposta do aplicativo de entrada da máquina virtual do Azure.

## Solucionando problemas de conectividade do ponto de extremidade

Se você tiver problemas ao se conectar a um ponto de extremidade como ponto de extremidade de área de trabalho remota, você pode tentar as seguintes etapas gerais de solução de problemas:

- Reiniciar máquina virtual
- Recriar ponto de extremidade
- Conectar-se de um local diferente
- Redimensionar a máquina virtual
- Recriar máquina virtual

Para obter mais informações, consulte [Solução de problemas de conectividade de ponto de extremidade (RDP/SSH/HTTP, falhas etc.)](https://social.msdn.microsoft.com/Forums/azure/pt-BR/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## Próximas etapas

Se tiver executado as etapas acima neste artigo e precisar de mais ajuda para corrigir o problema, você pode:

- Obter ajuda de especialistas do Azure no mundo inteiro. Enviar sua dúvida nos fóruns do MSDN Azure ou Stack Overflow. Consulte [Fóruns do Microsoft Azure](https://azure.microsoft.com/support/forums/) para obter mais informações.
- Registrar um incidente de suporte do Azure. Vá até o [Site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte** em **Suporte técnico e de cobrança**.

## Recursos adicionais

[Solucionar problemas de conexões de Área de Trabalho Remota para uma Máquina Virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](virtual-machines-troubleshoot-ssh-connections.md)

<!---HONumber=AcomDC_0128_2016-->