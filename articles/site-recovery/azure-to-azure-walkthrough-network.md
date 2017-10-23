---
title: "Planejar a rede de VMware para replicação do Azure com o Site Recovery| Microsoft Docs"
description: "Este artigo aborda o planejamento de rede necessário ao replicar VMs do Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: sujayt
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>Etapa 3: planejar a rede para a replicação de VM do Azure

Após verificar os [pré-requisitos de implantação](azure-to-azure-walkthrough-prerequisites.md), leia este artigo para entender as considerações de rede ao replicar e recuperar VMs (máquinas virtuais) do Azure de uma região do Azure para outra usando o serviço Azure Site Recovery. 

- Quando você concluir o artigo, deverá saber como configurar o acesso de saída de VMs do Azure que deseja replicar e como conectar às VMs do seu site local.
- Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
> Atualmente, a replicação de VM do Azure com o Site Recovery está em versão prévia.



## <a name="network-overview"></a>Visão geral da rede

Normalmente, suas VMs do Azure estão localizadas em uma rede virtual/sub-rede do Azure e há uma conexão de seu site local com o Azure usando o Azure ExpressRoute ou uma conexão VPN.

As redes geralmente são protegidas usando firewalls e/ou NSGs (grupos de segurança de rede). Os firewalls podem usar listas baseadas em URL ou baseadas em IP para controlar a conectividade de rede. Os NSGs usam regras baseadas em intervalos de IP. 


Para o Site Recovery trabalhar conforme o esperado, você precisa fazer algumas alterações na conectividade de rede de saída das máquinas virtuais que você deseja replicar. O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede. Se você tem um proxy de autenticação, a replicação não pode ser habilitada. 


O diagrama a seguir ilustra um ambiente típico do Azure para um aplicativo em execução em VMs do Azure.

![ambiente do cliente](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Ambiente de VM do Azure**

Você também pode configurar uma conexão com o Azure de seu site local usando o Azure ExpressRoute ou uma conexão VPN. 

![ambiente do cliente](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**Conexão local com o Azure**



## <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se você está usando um proxy de firewall baseado em URL para controlar a conectividade de saída, verifique se você permitiu as URLs usadas pelo Site Recovery

**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | Permite que os dados sejam gravados da VM para conta de armazenamento de cache da região de origem.
login.microsoftonline.com | Fornece autorização e autenticação para as URLs do serviço Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Permite a comunicação com o serviço Site Recovery da VM.
*.servicebus.windows.net | Necessário para que os dados de monitoramento e diagnóstico de recuperação de Site possam ser gravados da VM.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

- Você pode criar as regras necessárias automaticamente no NSG baixando e executando [este script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).
- É recomendável que você crie as regras NSG necessárias em um NSG de teste e verifique se não há nenhum problema antes de criar as regras em um NSG de produção.
- Para criar o número necessário de regras NSG, certifique-se de que sua assinatura está na lista de permissões. Contate o suporte para aumentar o limite de regra NSG em sua assinatura.
Se você estiver usando qualquer proxy firewall baseado em IP ou regras NSG para controlar a conectividade de saída, os seguintes intervalos IP devem estar na lista de permissões, dependendo dos locais de origem e destino das máquinas virtuais:

    - Todos os intervalos de endereços IP que correspondem ao local de origem. Baixe os [intervalos](https://www.microsoft.com/download/confirmation.aspx?id=41653).) A colocação na lista de permissões é necessária para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM.
    - Todos os intervalos IP que correspondam ao Office 365 [identidade e autenticação de pontos de extremidade do IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity). Se novos IPs forem adicionados aos intervalos de IP do Office 365, você precisará criar novas regras NSG.
-     Endereços IP de ponto de extremidade do Site Recovery ([disponíveis em um arquivo XML](https://aka.ms/site-recovery-public-ips)), que depende de seu local de destino: 

   **Local de destino** | **Serviço de Recuperação do Site IPs** |  **Monitoramento de Recuperação de site IP**
   --- | --- | ---
   Ásia Oriental | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   Sudeste Asiático | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   Índia Central | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   Sul da Índia | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   Centro-Norte dos EUA | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   Norte da Europa | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   Europa Ocidental | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   Leste dos EUA | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   Oeste dos EUA | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   Centro-Sul dos Estados Unidos | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   Centro dos EUA | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   Leste dos EUA 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   Leste do Japão | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   Oeste do Japão | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   Sul do Brasil | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   Leste da Austrália | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   Sudeste da Austrália | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   Canadá Central | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   Leste do Canadá | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   Centro-Oeste dos EUA | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   Oeste dos EUA 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   Oeste do Reino Unido | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   Sul do Reino Unido | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>Exemplo de Configuração do NSG

Esta seção mostra como configurar regras de NSG, para que as replicações funcionem para uma máquina virtual. Se você está usando regras NSG para controlar a conectividade de saída, use regras de "Permitir HTTPS de saída" para todos os intervalos IP necessários.

Neste exemplo, o local de origem da VM é "Leste dos EUA". O local de destino de replicação é EUA Central.


### <a name="example"></a>Exemplo

#### <a name="east-us"></a>Leste dos EUA

1. Criar regras que correspondam aos [intervalos de IP do Leste dos EUA](https://www.microsoft.com/download/confirmation.aspx?id=41653). Isso é necessário para que os dados possam ser gravados para a conta de armazenamento de cache da VM.
2. Criar regras para todos os intervalos IP que correspondam ao Office 365 [identidade e autenticação de pontos de extremidade do IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Criar regras que correspondam ao local de destino:

   **Localidade** | **Serviço de Recuperação do Site IPs** |  **Monitoramento de Recuperação de site IP**
    --- | --- | ---
   Centro dos EUA | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>Centro dos EUA

Essas regras são necessárias para que a replicação possa ser ativada da região de destino para a região de origem após o failover.

1. Crie regras que correspondam aos [intervalos de IP do EUA Central](https://www.microsoft.com/download/confirmation.aspx?id=41653).
2. Criar regras para todos os intervalos IP que correspondam ao Office 365 [identidade e autenticação de pontos de extremidade do IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Crie regras que correspondam ao local de origem:

   **Localidade** | **Serviço de Recuperação do Site IPs** |  **Monitoramento de Recuperação de site IP**
    --- | --- | ---
   Leste dos EUA | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>Conexão local existente

Se você tem uma conexão de ExpressRoute ou VPN entre seu site local e o local de origem no Azure, siga estas orientações:

**Configuração** | **Detalhes**
--- | ---
**Túnel forçado** | Normalmente, uma rota padrão (0.0.0.0/0) força o tráfego de Internet de saída a fluir pelo local. Não recomendamos isso. O tráfego de replicação e as comunicações do Site Recovery devem permanecer no Azure.<br/><br/> Como solução, adicione UDRs (rotas definidas pelo usuário) para [esses intervalos IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) para que o tráfego não seja local.
**Conectividade** | Se os aplicativos precisam se conectar a computadores locais ou se os clientes locais se conectam ao aplicativo localmente por VPN/ExpressRoute, verifique se você tem pelo menos uma [conexão site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre o região do Azure de destino e o site local.<br/><br/> Se os volumes de tráfego estão altos entre a região do Azure de destino e o site local, crie outra [conexão do ExpressRoute](../expressroute/expressroute-introduction.md) entre a região de destino e o local.<br/><br/> Se você deseja manter IPs para as VMs depois do failover, mantenha a conexão de site para site/ExpressRoute da região de destino em um estado desconectado. Isso faz com que não haja nenhum conflito de intervalos entre os intervalos de endereços IP de origem e destino.
**ExpressRoute** | Crie um circuito de ExpressRoute nas regiões de origem e de destino.<br/><br/> Crie uma conexão entre a rede de origem e o circuito do ExpressRoute e entre a rede de destino e o circuito.<br/><br/> É recomendável que você use intervalos IP diferentes em regiões de origem e destino. O circuito não será capaz de se conectar a mais de uma rede do Azure com os mesmos intervalos IP ao mesmo tempo.<br/><br/> Você pode criar redes virtuais com os mesmos intervalos IP em ambas as regiões e, em seguida, criar circuitos de ExpressRoute em ambas as regiões. No caso de um failover, desconecte o circuito da rede virtual de origem e conecte o circuito na rede virtual de destino.<br/><br/> Se a região principal estiver completamente inativa, a operação de desconectar poderá falhar. Nesse caso, a rede virtual de destino não terá a conectividade do ExpressRoute.
**ExpressRoute Premium** | Você pode criar circuitos na mesma região geopolítica.<br/><br/> Para criar circuitos em diferentes regiões geopolíticas, é necessário ter o Azure ExpressRoute Premium.<br/><br/> Com o Premium, o custo é incremental. Se você já o está usando, não há nenhum custo adicional.<br/><br/> Saiba mais sobre [locais](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [preço](https://azure.microsoft.com/pricing/details/expressroute/).



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 4: criar um cofre](azure-to-azure-walkthrough-vault.md)

