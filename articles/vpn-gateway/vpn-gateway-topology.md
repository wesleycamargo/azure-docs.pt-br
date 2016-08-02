<properties 
   pageTitle="Topologias de conexão de gateway de VPN | Microsoft Azure"
   description="Exiba as topologias de conexão de gateway de VPN e os modelos de implantação e as ferramentas de configuração disponíveis."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="cherylmc" />

# Conexões do Gateway de VPN do Azure

Este artigo mostra as topologias de conexão de gateway de VPN de linha de base. Você pode usar os elementos gráficos e as descrições para ajudar a selecionar a topologia de configuração adequada a seus requisitos. Embora este artigo percorra as topologias de linha de base principais, é possível criar topologias mais complexas usando os diagramas como uma diretriz.

Cada topologia contém uma tabela que lista o modelo de implantação para o qual a topologia está disponível e as ferramentas de implantação que você pode usar para configurar cada topologia e será vinculado diretamente a um artigo, caso disponível. Atualizamos as tabelas com frequência, à medida que novos artigos e ferramentas de implantação se tornam disponíveis para uso.

Depois de decidir qual conexão você deseja criar, as instruções que você usará para criar o gateway de VPN dependerão do modelo de implantação que usou para criar a rede virtual. Por exemplo, se tiver criado a rede virtual usando o modelo de implantação clássico, você usará as diretrizes e instruções do modelo de implantação clássico para criar e configurar o gateway de VPN. Você não pode criar um gateway de VPN do Gerenciador de Recursos para uma rede virtual do modelo de implantação clássico. Confira [Noções básicas sobre o Gerenciador de Recursos e modelos de implantação clássicos](../resource-manager-deployment-model.md) para obter mais informações sobre modelos de implantação.

## Site a Site e Vários Sites

Uma conexão Site a Site é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão requer um dispositivo VPN ou RRAS do Windows Server local. As conexões site a site podem ser usadas para configurações entre instalações e híbridas.


**Diagrama de S2S**

![Conexão S2S](./media/vpn-gateway-topology/site2site.png "site-to-site")

Você poderá criar e configurar mais de uma conexão VPN S2S para redes locais se estiver usando VPNs Baseadas em Rota do Azure. Esse tipo de configuração é frequentemente chamado de conexão de "vários sites".
 

**Diagrama de vários sites**

![Conexão de vários sites](./media/vpn-gateway-topology/multisite.png "vários sites")


**Métodos e modelos de implantação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet a VNet

Conectar uma rede virtual a outra rede virtual é bastante semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN do Azure para fornecer um túnel seguro usando IPsec/IKE. As VNetsque você conecta podem estar em regiões ou em assinaturas diferentes. Você pode até combinar a comunicação de rede virtual com rede virtual a configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

Atualmente, o Azure tem dois modelos de implantação: Gerenciamento de Serviços do Azure (conhecido como clássico) e Azure Resource Manager. Se já usa o Azure há algum tempo, você provavelmente tem VMs do Azure e funções de instância em execução em uma VNet clássica, enquanto as VMs e instâncias de função mais recentes podem estar em execução em uma VNet criada no ARM. Você pode criar uma conexão entre redes virtuais mesmo que elas estejam em modelos de implantação diferentes.


**Diagrama de VNet2VNet**

![Conectando VNet a VNet](./media/vpn-gateway-topology/vnet2vnet.png "vnet a vnet")


**Métodos e modelos de implantação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## Ponto a Site

Uma configuração Ponto a Site permite que você crie uma conexão segura com sua rede virtual em um computador cliente, individualmente. Uma conexão VPN é estabelecida por meio do início da conexão do computador cliente. Essa é uma solução útil quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual.

Uma conexão Ponto a Site é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar.

**Diagrama de P2S**

![Conexão ponto a site](./media/vpn-gateway-topology/point2site.png "point-to-site")

**Métodos e modelos de implantação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


##Rota Expressa

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Para obter mais informações sobre a Rota Expressa, consulte [Visão geral técnica da Rota Expressa](../expressroute/expressroute-introduction.md).



## Conexões coexistentes Site a Site e de Rota Expressa

A Rota Expressa é uma conexão direta e dedicada para Serviços Microsoft, incluindo o Azure, de sua WAN, não pela Internet pública. O tráfego da VPN Site a Site é criptografado ao percorer a Internet pública. Poder configurar conexões VPN Site a Site e Rota Expressa para a mesma rede virtual oferece várias vantagens. Você pode configurar a VPN Site a Site como um caminho de failover seguro para a Rota Expressa ou usar VPNs Site a Site para se conectar a sites que não fazem parte de sua rede, mas estão conectados por meio da Rota Expressa.


**Diagrama de conexões coexistentes**

![Coexistência de conexão](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**Métodos e modelos de implantação disponíveis**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]




## Próximas etapas

Convém familiarizar-se com os itens dos artigos [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md) e [Perguntas frequentes sobre o gateway de VPN](vpn-gateway-vpn-faq.md) para compreender melhor as configurações do Gateway de VPN.





 

<!---HONumber=AcomDC_0727_2016-->