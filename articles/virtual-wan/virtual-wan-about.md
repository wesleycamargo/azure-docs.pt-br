---
title: Visão geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre conectividade escalonável e automatizada entre branches da WAN Virtual.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/18/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 67dd6ba9b94ed9d58d91fb644ce9ee9e44ae9e45
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159162"
---
# <a name="what-is-azure-virtual-wan-preview"></a>O que é a WAN Virtual do Azure? (Visualização)

A WAN Virtual do Azure é um serviço de rede que fornece conectividade otimizada e automatizada entre branches por meio do Azure. A WAN Virtual permite que você conecte e configure dispositivos de branch para se comunicar com o Azure. Isso pode ser feito manualmente ou usando dispositivos de provedor preferencial por meio de um parceiro de WAN Virtual. O uso de dispositivos de provedor preferencial facilitam o uso, simplificam a conectividade e permitem o gerenciamento da configuração. O painel interno de WAN do Azure fornece informações instantâneas de solução de problemas que podem ajudar você a economizar tempo e proporcionam uma maneira fácil de exibir conectividade site a site em larga escala.

> [!IMPORTANT]
> A WAN Virtual do Azure é atualmente uma versão prévia pública gerenciada. Para usar a WAN Virtual, você deve [registrar-se na versão prévia](#enroll).
>
> Essa versão prévia pública é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Este artigo fornece uma visão rápida sobre a conectividade de rede de seu Azure e de cargas de trabalho não Azure. A WAN Virtual oferece as seguintes vantagens:

* **Soluções de conectividade integradas em hub e spoke:** automatize a configuração site a site e a conectividade entre sites locais e um hub do Azure de diversas fontes, incluindo soluções de parceiro WAN Virtual.
* **Instalação e configuração automatizadas de spoke:** conecte suas redes virtuais e cargas de trabalho diretamente ao hub do Azure.
* **Solução de problemas intuitiva:** veja o fluxo ponta a ponta dentro do Azure e use essas informações para executar as ações necessárias.

![Diagrama de WAN Virtual](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Trabalhar com um parceiro de WAN Virtual

1. O controlador do dispositivo de branch (VPN/SDWAN) é autenticado para exportar informações do Site no Azure usando uma Entidade de Serviço do Azure.
2. O controlador do dispositivo de branch (VPN/SDWAN) obtém a configuração de conectividade do Azure e atualiza o dispositivo local. Isso automatiza o download da configuração, a edição e a atualização do dispositivo VPN local.
3. Quando o dispositivo tiver a configuração do Azure correta, uma conexão site a site (dois túneis ativos) será estabelecida com a WAN do Azure. O Azure exige que o controlador de branch (VPN/SDWAN) dê suporte a IKEv2. BGP é opcional.

## <a name="resources"></a>Recursos da WAN Virtual

Para configurar uma WAN virtual de ponta a ponta, crie os seguintes recursos:

* **virtualWAN:** o recurso virtualWAN representa uma sobreposição virtual de sua rede do Azure, e é uma coleção de vários recursos. Ele contém links para todos os seus hubs virtuais que você gostaria de ter dentro da WAN Virtual. Os recursos da WAN Virtual ficam isolados uns dos outros e não podem conter um hub comum. Os hubs virtuais na WAN Virtual não se comunicam entre si.

* **Site:** o recurso de site conhecido como vpnsite representa o dispositivo VPN local e suas configurações. Ao trabalhar com um parceiro de WAN Virtual, você terá uma solução interna para exportar automaticamente essas informações para o Azure.

* **Hub:** um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade de sua rede local (vpnsite). O hub é o núcleo da sua rede em uma região. Pode haver apenas um hub por região do Azure. Quando você cria um hub usando o Portal do Azure, ele cria automaticamente uma VNet de hub virtual e um hub virtual vpngateway.

  Um gateway de hub não é o mesmo que um gateway de rede virtual que você pode usar para ExpressRoute e Gateway de VPN. Por exemplo, ao usar a WAN Virtual, você não cria uma conexão site a site do seu site local diretamente com sua VNet. Em vez disso, você cria uma conexão site a site com o hub. O tráfego sempre passa pelo gateway do hub. Isso significa que suas VNets não precisam de um gateway de rede virtual próprio. A WAN Virtual permite que suas VNets tirem proveito do dimensionamento fácil por meio do hub virtual e do gateway do hub virtual. 

* **Conexão de rede virtual do hub:** o recurso de conexão de rede virtual do Hub é usado para conectar o hub perfeitamente à sua rede virtual. Neste momento, você só pode se conectar a redes virtuais que estejam na mesma região do hub.

##<a name="enroll"></a>Inscrever-se na versão prévia

Antes de configurar a WAN Virtual, você precisa inscrever sua assinatura na versão prévia. Caso contrário, você não poderá trabalhar com a WAN Virtual no portal. Você se inscrever, envie um email para <azurevirtualwan@microsoft.com> com a ID da sua assinatura. Após a inscrição da sua assinatura, você receberá um email.

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>Comentários sobre a versão prévia

Agradecemos por seus comentários. Envie um email para <azurevirtualwan@microsoft.com> para relatar possíveis problemas ou fornecer comentários (positivos ou negativos) sobre a WAN Virtual. Inclua nome da sua empresa entre "[ ]" na linha do assunto. Também inclua sua ID de assinatura se estiver relatando um problema.

## <a name="next-steps"></a>Próximas etapas

Para criar uma conexão site a site usando a WAN Virtual, você pode usar um [parceiro de WAN Virtual](https://aka.ms/virtualwan) ou criar a conexão manualmente. Para criar a conexão manualmente, consulte [Criar uma conexão site a site usando a WAN Virtual](virtual-wan-site-to-site-portal.md).
