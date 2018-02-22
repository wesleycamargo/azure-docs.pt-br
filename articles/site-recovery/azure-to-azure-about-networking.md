---
title: "Informações sobre rede na recuperação de desastre do Azure para o Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Fornece uma visão geral da rede para a replicação de VMs do Azure usando o Azure Site Recovery."
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: sujayt
ms.openlocfilehash: 5ce85761df4e0ad62c22a829f67464a3145fd827
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Informações sobre rede para replicação do Azure para o Azure

>[!NOTE]
> Atualmente, a replicação do Site Recovery para máquinas virtuais do Azure está em versão prévia.

Este artigo fornece diretrizes de rede para quando você está replicando e recuperando VMs do Azure de uma região para outra usando o [Azure Site Recovery](site-recovery-overview.md). 

## <a name="before-you-start"></a>Antes de começar

Saiba como o Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infraestrutura de rede típica

O diagrama a seguir ilustra um ambiente típico do Azure para aplicativos em execução em VMs do Azure:

![ambiente do cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se você estiver usando uma conexão VPN ou o Azure ExpressRoute da rede local do Azure, o ambiente terá esta aparência:

![ambiente do cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

As redes geralmente são protegidas usando firewalls e NSGs (grupos de segurança de rede). Os firewalls usam lista branca baseada em IP ou em URL para controlar a conectividade de rede. Os NSGs fornecem regras que usam intervalos de endereços IP para controlar a conectividade de rede.

>[!IMPORTANT]
> O uso de um proxy autenticado para controlar a conectividade de rede não é compatível com o Site Recovery e a replicação não pode ser habilitada.


## <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se você está usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita estas URLs do Site Recovery:


**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | Necessário para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM.
login.microsoftonline.com | Necessário para autorização e autenticação para as URLs do serviço de recuperação de Site.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação de serviço de recuperação de Site possa ocorrer da VM.
* .servicebus.windows.net | Necessário para que os dados de monitoramento e diagnóstico de recuperação de Site possam ser gravados da VM.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se você está usando regras de NSG ou de proxy de firewall baseadas em IP para controlar a conectividade de saída, os intervalos de IP a seguir precisam ser permitidos.

- Todos os intervalos de endereços IP que correspondem ao local de origem.
    - Você pode baixar os [intervalos de endereços IP](https://www.microsoft.com/download/confirmation.aspx?id=41653).
    - Você precisa permitir esses endereços para que os dados possam ser gravados da VM para a conta de armazenamento de cache.
- Todos os intervalos de endereços IP que correspondam aos [pontos de extremidade de IP V4 de identidade e de autenticação](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) do Office 365.
    - Se novos endereços forem adicionados aos intervalos de IP do Office 365 no futuro, crie novas regras de NSG.
- Endereços IP de ponto de extremidade de serviço do Site Recovery. Eles estão disponíveis em um [arquivo XML](https://aka.ms/site-recovery-public-ips) e dependem do seu local de destino.
-  Você pode [baixar e usar esse script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) para criar automaticamente as regras necessárias no NSG. 
- É recomendável que você crie as regras de NSG necessárias em um NSG de teste e verifique se não há nenhum problema antes de criar as regras em um NSG de produção.
- Para criar o número necessário de regras NSG, certifique-se de que sua assinatura está na lista de permissões. Entre em contato com o suporte do Azure para aumentar o limite de regras de NSG em sua assinatura.

Os intervalos de endereços IP são como demonstrado a seguir:

>
   **Destino** | **IP do Site Recovery** |  **Monitoramento de Recuperação de site IP**
   --- | --- | ---
   Ásia Oriental | 52.175.17.132 | 13.94.47.61
   Sudeste Asiático | 52.187.58.193 | 13.76.179.223
   Índia Central | 52.172.187.37 | 104.211.98.185
   Sul da Índia | 52.172.46.220 | 104.211.224.190
   Centro-Norte dos EUA | 23.96.195.247 | 168.62.249.226
   Norte da Europa | 40.69.212.238 | 52.169.18.8
   Europa Ocidental | 52.166.13.64 | 40.68.93.145
   Leste dos EUA | 13.82.88.226 | 104.45.147.24
   Oeste dos EUA | 40.83.179.48 | 104.40.26.199
   Centro-Sul dos Estados Unidos | 13.84.148.14 | 104.210.146.250
   Centro dos EUA | 40.69.144.231 | 52.165.34.144
   Leste dos EUA 2 | 52.184.158.163 | 40.79.44.59
   Leste do Japão | 52.185.150.140 | 138.91.1.105
   Oeste do Japão | 52.175.146.69 | 138.91.17.38
   Sul do Brasil | 191.234.185.172 | 23.97.97.36
   Leste da Austrália | 104.210.113.114 | 191.239.64.144
   Sudeste da Austrália | 13.70.159.158 | 191.239.160.45
   Canadá Central | 52.228.36.192 | 40.85.226.62
   Leste do Canadá | 52.229.125.98 | 40.86.225.142
   Centro-Oeste dos EUA | 52.161.20.168 | 13.78.149.209
   Oeste dos EUA 2 | 52.183.45.166 | 13.66.228.204
   Oeste do Reino Unido | 51.141.3.203 | 51.141.14.113
   Sul do Reino Unido | 51.140.43.158 | 51.140.189.52
   Sul do Reino Unido 2 | 13.87.37.4| 13.87.34.139
   Norte do Reino Unido | 51.142.209.167 | 13.87.102.68
   Coreia Central | 52.231.28.253 | 52.231.32.85
   Sul da Coreia | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>Exemplo de Configuração do NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada. 

- Se você está usando regras de NSG para controlar a conectividade de saída, use regras de "Permitir HTTPS de saída" para todos os intervalos de endereços IP necessários.
- O exemplo presume que o local de origem da VM é "Leste dos EUA" e que o local de destino é o "EUA Central".

### <a name="nsg-rules---east-us"></a>Regras de NSG – Leste dos EUA

1. Criar regras que correspondam aos [intervalos de endereços IP do Leste dos EUA](https://www.microsoft.com/download/confirmation.aspx?id=41653). Isso é necessário para que os dados possam ser gravados para a conta de armazenamento de cache da VM.
2. Criar regras para todos os intervalos de endereços IP que correspondam aos [pontos de extremidade de IP V4 de identidade e de autenticação](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) do Office 365.
3. Criar regras que correspondam ao local de destino:

   **Localidade** | **Endereço IP do Site Recovery** |  **Endereço IP de monitoramento do Site Recovery**
    --- | --- | ---
   Centro dos EUA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regras de NSG – EUA Central 

Essas regras são necessárias para que a replicação possa ser ativada da região de destino para a região de origem após o failover:

* Regras que correspondam aos [intervalos de IP do Centro dos EUA](https://www.microsoft.com/download/confirmation.aspx?id=41653). Isso é necessário para que os dados possam ser gravados para a conta de armazenamento de cache da VM.

* Regras para todos os intervalos IP que correspondam ao Office 365 [identidade e autenticação de pontos de extremidade do IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Regras que correspondam ao local de origem:
    - Leste dos EUA
    - Endereço IP do Site Recovery: 13.82.88.226
    - Endereço IP de monitoramento do Site Recovery: 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

Se você tiver uma conexão de ExpressRoute ou VPN entre o seu local e o local do Azure, siga as diretrizes nesta seção.

### <a name="forced-tunneling"></a>Túnel forçado

Normalmente, você define uma rota padrão (0.0.0.0/0) que força o tráfego de Internet de saída a fluir pelo local. Não recomendamos isso. O tráfego de replicação e comunicação de serviço de recuperação de Site não devem deixar o limite do Azure. A solução é adicionar rotas definidas pelo usuário (UDRs) para [esses intervalos IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) para que o tráfego de replicação não seja local.

### <a name="connectivity"></a>Conectividade 

Siga estas diretrizes para conexões entre o local de destino e o local:
- Se seu aplicativo precisa se conectar a máquinas locais ou se houver clientes que se conectam ao aplicativo do local por VPN/ExpressRoute local, certifique-se de que você tenha pelo menos uma [conexão site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre o datacenter local e sua região do Azure de destino.

- Se você estiver esperando muito tráfego fluindo entre o datacenter local e sua região do Azure de destino, você deve criar outra [conexão ExpressRoute](../expressroute/expressroute-introduction.md) entre a região do Azure de destino e o datacenter local.

- Se você deseja manter IPs para as máquinas virtuais depois do failover, mantenha a conexão de site para site/ExpressRoute da região de destino em um estado desconectado. Isso é para certificar-se de que não há nenhum conflito de intervalo entre os intervalos de IP da região de origem e os intervalos de IP da região de destino.

### <a name="expressroute-configuration"></a>Configuração do ExpressRoute
Siga estas práticas recomendadas para configuração de ExpressRoute:

- Você precisa criar um circuito de ExpressRoute nas regiões de origem e de destino. Em seguida, você precisa criar uma conexão entre:
  - A Rede Virtual de origem e o circuito ExpressRoute.
  - A Rede Virtual de destino e o circuito ExpressRoute.

- Como parte do padrão de ExpressRoute, você pode criar circuitos na mesma região geopolítica. Para criar circuitos ExpressRoute em diferentes regiões geopolíticas, o Azure ExpressRoute Premium é necessário, o que envolve um custo incremental. (Se você já estiver usando o ExpressRoute Premium, não há nenhum custo extra.) Para obter mais detalhes, consulte o [documento de locais de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

- É recomendável que você use intervalos IP diferentes em regiões de origem e destino. O circuito de ExpressRoute não será capaz de se conectar com duas redes virtuais do Azure de mesmo intervalo de IP ao mesmo tempo.

- Você pode criar redes virtuais com os mesmos intervalos IP em ambas as regiões e, em seguida, criar circuitos de ExpressRoute em ambas as regiões. No caso de um evento de failover, desconecte o circuito da rede virtual de origem e conecte o circuito na rede virtual de destino.

 >[!IMPORTANT]
 > Se a região principal estiver completamente inativa, a operação de desconectar pode falhar. Que impedirá a rede virtual de destino de conseguir conectividade de ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
Inicie a proteção de suas cargas de trabalho ao [replicar máquinas virtuais do Azure](site-recovery-azure-to-azure.md).
