<properties
    pageTitle="Como planejar sua rede virtual para uma coleção do RemoteApp do Azure | Microsoft Azure"
    description="Como planejar sua rede virtual para uma coleção do RemoteApp do Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/07/2016"
    ms.author="elizapo" />

# Como planejar sua rede virtual para o RemoteApp do Azure

Este documento descreve como configurar sua VNET (rede virtual) do Azure e a sub-rede do RemoteApp do Azure. Se você não estiver familiarizado com redes virtuais do Azure, essa é uma funcionalidade que ajuda a virtualizar sua infraestrutura de rede para a nuvem e a criar soluções híbridas com o Azure e seus recursos locais. Saiba mais sobre isso [aqui](virtual-networks-overview.md).

Se você quiser definir políticas de segurança para tráfego (entrada e saída) em sua rede virtual na qual você está implantando o RemoteApp do Azure, é altamente recomendável criar uma sub-rede para o RemoteApp do Azure separada do restante de suas implantações na rede virtual do Azure. Para obter mais informações sobre como definir políticas de segurança em sua sub-rede de rede virtual do Azure, leia [O que é um NSG (Grupo de Segurança de Rede)?](virtual-networks-nsg.md).

## Tipos de coleções do RemoteApp do Azure com redes virtuais do Azure

Os gráficos a seguir mostram as duas diferentes opções de coleção quando você quer usar uma rede virtual.

### Coleção de nuvem do RemoteApp do Azure com VNET

 ![RemoteApp do Azure - Coleção de nuvem do RemoteApp do Azure com uma VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Isso representa uma coleção do RemoteApp do Azure, na qual todos os recursos que os hosts da sessão do Remoteapp precisam acessar são implantados no Azure. Eles podem estar na mesma VNET como a VNET do RemoteApp ou em um VNET diferente no Azure.

### Coleção híbrida do RemoteApp do Azure com VNET

![RemoteApp do Azure - Coleção híbrida com uma VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Isso representa uma coleção do RemoteApp do Azure, na qual alguns dos recursos que os hosts da sessão do Remoteapp precisam acessar são implantados localmente. A VNET do RemoteApp está vinculada à rede local usando tecnologias híbridos do Azure, como VPN de site a site ou de Rota Expressa.


## Como o sistema funciona

Nos bastidores, o RemoteApp do Azure implanta máquinas virtuais do Azure (com sua imagem carregada) na sub-rede da rede virtual que você escolheu durante o provisionamento. Se você tiver optado por uma coleção híbrida, tentamos resolver o FQDN do controlador de domínio que você inseriu no provisionamento do fluxo de trabalho com o servidor DNS fornecido na rede virtual. Se você estiver conectando-se a uma rede virtual existente, certifique-se de expor as portas necessárias em seus grupos de segurança de rede em sua sub-rede do RemoteApp do Azure.

Recomendamos que você use uma [sub-rede suficientemente grande para o RemoteApp do Azure](remoteapp-vnetsizing.md). A maior rede Virtual do Azure para a qual há suporte é /8 (usando definições de sub-rede CIDR). Sua sub-rede deve ser suficientemente grande para acomodar todas as VMs de RemoteApp do Azure durante a expansão quando mais usuários estão acessando os aplicativos.

A seguir estão as coisas que você precisará habilitar em sua sub-rede de rede virtual:

2.	Para se comunicar com um dos serviços internos do RemoteApp do Azure, o tráfego de saída da sub-rede deve ser permitido no intervalo de portas 10101-10175.
3.	Para se conectar ao armazenamento do Azure na porta 443, o tráfego de saída desde sua sub-rede deve ser permitido
4.	Se você tem o Active Directory hospedado no Azure, certifique-se de que qualquer máquina virtual dentro da sub-rede de rede virtual para o RemoteApp do Azure é capaz de se conectar ao controlador de domínio. O DNS na rede virtual deve ser capaz de resolver o FQDN deste controlador de domínio.


## Rede virtual com túnel forçado

Agora há suporte para o [túnel forçado](vpn-gateway-about-forced-tunneling.md) para todas as novas coleções do RemoteApp do Azure. Atualmente, não há suporte para a migração de uma coleção existente para dar suporte a túnel forçado. Você precisará excluir todas as coleções existentes usando a VNET que você está vinculando ao RemoteApp do Azure e criar uma nova coleção para habilitar o túnel forçado em suas coleções.

<!---HONumber=AcomDC_0114_2016-->