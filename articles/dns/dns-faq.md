---
title: Perguntas frequentes do DNS do Azure | Microsoft Docs
description: Perguntas frequentes sobre o DNS do Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 66e04e7f0b272f19788e79805ef06d11e2eda572
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948019"
---
# <a name="azure-dns-faq"></a>Perguntas frequentes do DNS do Azure

## <a name="about-azure-dns"></a>Sobre o DNS do Azure

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure.

Domínios DNS no DNS do Azure são hospedados na rede global do Azure de servidores de nomes DNS. Isso usa a rede Anycast, para que cada consulta DNS seja atendida pelo servidor DNS mais próximo disponível. O DNS do Azure fornece desempenho rápido e alta disponibilidade para seu domínio.

O serviço DNS do Azure baseia-se no Azure Resource Manager. Sendo assim, ele aproveita recursos do Resource Manager, como o controle de acesso baseado em função, os logs de auditoria e o bloqueio de recursos. Seus domínios e registros podem ser gerenciados por meio do portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de plataforma cruzada. Aplicativos que requerem gerenciamento automático de DNS podem se integrar com o serviço por meio da API REST e dos SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o DNS do Azure?

O modelo de preço do DNS do Azure é baseado no número de zonas DNS hospedadas no DNS do Azure e o número de consultas DNS que eles recebem. Descontos são fornecidos com base no uso.

Para saber mais, veja a [página de preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>O que é o SLA para DNS do Azure?

O Azure garante que as solicitações DNS válidas receberão uma resposta de pelo menos um servidor de nome DNS do Azure, no mínimo 99,99% do tempo.

Para saber mais, confira a [página de SLA do DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma 'zona DNS'? É a mesma coisa que um domínio DNS? 

Um domínio é um nome exclusivo no sistema de nomes de domínio, por exemplo, 'contoso.com'.


Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Por exemplo, o domínio "contoso.com" pode conter vários registros DNS, como ‘mail.contoso.com’ (para um servidor de email) e ‘www.contoso.com’ (para um site da Web). Esses registros seriam hospedados na zona DNS 'contoso.com'.

Um nome de domínio é *apenas um nome*, enquanto que uma zona DNS é um recurso de dados que contém os registros de DNS para um nome de domínio. O Azure DNS permite hospedar uma zona DNS e gerenciar os registros DNS para um domínio no Azure. Ele também fornece servidores de nomes de DNS para resolver consultas de DNS da Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Preciso comprar um nome de domínio DNS para usar o DNS do Azure? 

Não necessariamente.

Você não precisa comprar um domínio para hospedar uma zona DNS no DNS do Azure. É possível criar uma zona DNS a qualquer momento sem ser proprietário do nome de domínio. As consultas de DNS para esta zona somente resolverão se elas estiverem direcionadas para os servidores de nome de DNS do Azure atribuídos à zona.

Você precisará obter o nome de domínio se desejar vincular sua zona DNS na hierarquia DNS global; essa vinculação permite que consultas DNS de qualquer lugar no mundo encontrem sua zona DNS e sejam respondidos em seus registros DNS.

## <a name="azure-dns-features"></a>Recursos do DNS do Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existem restrições ao usar registros de alias para um apex de nome de domínio com o Gerenciador de Tráfego?

Sim. Você deve usar endereços IP públicos com o Gerenciador de Tráfego. Configure o **Ponto de extremidade externo** usando um endereço IP de estático. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O DNS do Azure dá suporte a roteamento de tráfego com base em DNS ou failover de ponto de extremidade?

O roteamento de tráfego com base em DNS e o failover de ponto de extremidade são fornecidos pelo Gerenciador de Tráfego do Azure. O Gerenciador de Tráfego do Microsoft Azure é um serviço do Azure separado que pode ser usado junto com o DNS do Azure. Para saber mais, confira a [Visão Geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

O DNS do Azure só oferece suporte à hospedagem de domínios de DNS 'estáticos', em que cada consulta DNS para um determinado registro DNS sempre recebe a mesma resposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O DNS do Azure dá suporte ao registro de nome de domínio?

Não. O DNS do Azure não dá suporte a compra de nomes de domínio. Se você deseja adquirir domínios, será necessário usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

A compra de domínio é um recurso que está sendo rastreado na lista de pendências do Azure. Você pode usar o site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>O DNS do Azure oferece suporte a DNSSEC?

Não. Atualmente não há suporte para DNSSEC no DNS do Azure.

O DNSSEC é um recurso que está sendo rastreado na lista de pendências do DNS do Azure. Você pode usar o site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O DNS do Azure dá suporte para transferências de zona (AXFR/IXFR)?

Não. Atualmente não há suporte para transferências de zona. As zonas DNS podem ser [importadas para o DNS do Azure usando a CLI do Azure](dns-import-export.md). Os registros de DNS podem ser gerenciados por meio do [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), nossa [API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou [ferramenta CLI](dns-operations-recordsets-cli.md).

A transferência de zona é um recurso que está sendo rastreado na lista de pendências do DNS do Azure. Você pode usar o site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O DNS do Azure oferece suporte a redirecionamentos de URL?

Não. Os serviços de redirecionamento de URL não são, na verdade, um serviço do DNS: eles funcionam no nível de HTTP, em vez de no nível de DNS. Alguns provedores de DNS incluem um serviço de redirecionamento de URL como parte da oferta geral. Atualmente, isso não tem o suporte do DNS do Azure.

O recurso Redirecionamento de URL está sendo rastreado na lista de pendências do DNS do Azure. Você pode usar o site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset"></a>O DNS do Azure dá suporte à codificação ASCII estendida (8 bits) definida para Conjuntos de Registros TXT?

Sim. O DNS do Azure dá suporte ao conjunto de codificação ASCII estendido para Conjuntos de Registros TXT, se você usa a versão mais recente das APIs REST do Azure, SDKs, PowerShell e CLI (versões anteriores a 01/10/2017 ou SDK 2.1 não dão suporte ao conjunto ASCII estendido). Por exemplo, se o usuário fornecer uma cadeia de caracteres como o valor de um registro TXT que tenha o caractere ASCII estendido \128 (por exemplo: "abcd\128efgh"), o DNS do Azure usará o valor de byte desse caractere (que é 128) na representação interna. No momento da resolução de DNS, esse valor de byte será retornado na resposta. Observe também que "abc" e "\097\098\099" são intercambiáveis com relação à resolução. 

Seguimos as regras de escape de formatação mestre de arquivo da zona [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) para registros TXT. Por exemplo, ‘\’ agora realmente escapa tudo pelo RFC. Se você especificar "A\B" como o valor do registro TXT, ele será representado e resolvido como apenas "AB". Se você realmente quer que o registro TXT tenha "A\B" na resolução, será necessário novamente o escape "\", ou seja, especifique como "A\\B". 

Esse suporte não está disponível atualmente para registros TXT criados no portal do Azure. 

## <a name="alias-records"></a>Registro de alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quais são alguns cenários em que os registros de alias são úteis?
Consulte a seção de cenários em [Visão geral de registros de alias do DNS do Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Quais tipos de registro têm suporte para conjuntos de registros de alias?
Conjuntos de registros de alias têm suporte para os seguintes tipos de registros em uma zona DNS do Azure: A, AAAA e CNAME. 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Quais recursos têm suporte como destinos para os conjuntos de registros de alias?
- **Apontar para um recurso de IP público de um conjunto de registros A/AAAA do DNS**. Você pode criar um conjunto de registros A/AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso IP público.
- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros A/AAAA/CNAME do DNS**. Além da capacidade para apontar para o CNAME de um perfil do Gerenciador de Tráfego (por exemplo: contoso.trafficmanager.net) de um conjunto de registros CNAME do DNS, você agora também pode apontar para um perfil do Gerenciador de Tráfego que tenha pontos de extremidade externos, de um conjunto de registros A ou AAAA na sua zona DNS.
- **Apontar para outro conjunto de registros DNS dentro da mesma zona**. Registros de alias podem fazer referência a outros conjuntos de registros do mesmo tipo. Por exemplo, você pode ter um conjunto de registros CNAME do DNS definir um alias para outro conjunto de registros CNAME do mesmo tipo. Isso será útil se você quiser que alguns conjuntos de registros sejam aliases e outros que não sejam aliases em termos de comportamento.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Posso criar e atualizar os registros de alias do portal do Azure?
Sim. Registros de alias podem ser criados ou gerenciados no portal do Azure além de APIs REST do Azure, Azure PowerShell, CLI e SDKs.

### <a name="will-alias-records-help-ensure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Registros de alias ajudarão a garantir que meu conjunto de registros de DNS seja excluído quando o IP público subjacente for excluído?
Sim. Na verdade, esse é um dos principais recursos de registros de alias. Eles ajudam você a evitar possíveis interrupções para os usuários finais de seu aplicativo.

### <a name="will-alias-records-help-ensure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Registros de alias ajudarão a garantir que meu conjunto de registros de DNS seja atualizado para o endereço IP correto quando o endereço IP público subjacente for alterado?
Sim. Como na pergunta anterior, esse é um dos principais recursos de registros de alias e ajuda a evitar a possibilidade de interrupções ou riscos de segurança para seu aplicativo.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-an-a-or-aaaa-records-to-point-to-traffic-manager"></a>Quais são as restrições ao usar o registro de alias conjuntos para uma registros A ou AAAA apontar para o Gerenciador de Tráfego?
Sim. Se você quiser apontar para um perfil do Gerenciador de tráfego como um alias do conjunto de registros A ou AAAA, garanta que o perfil do Gerenciador de Tráfego use apenas os pontos de extremidade externos. Quando você cria pontos de extremidade externos no Gerenciador de Tráfego, forneça os endereços IP reais dos pontos de extremidade.

### <a name="is-there-an-additional-charge-for-using-alias-records"></a>Há um custo adicional para usar registros de alias?
Registros de alias são uma qualificação de um conjunto de registros DNS válido e não há nenhuma cobrança adicional para registros de alias.

## <a name="using-azure-dns"></a>Usando o DNS do Azure

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>É possível co-hospedar um domínio usando o DNS do Azure e outro provedor de DNS?

Sim. O DNS do Azure oferece suporte a domínios de co-hospedagem com outros serviços de DNS.

Para fazer isso, você precisará modificar os registros de DNS para o domínio (que controlam quais provedores recebem consultas de DNS para o domínio) para apontar para os servidores de nome de ambos os provedores. Você pode modificar esses registros de NS em três lugares: no DNS do Azure, no outro provedor e na zona pai (normalmente configuradas por meio do registrador de nomes de domínio). Para saber mais sobre delegação de DNS, veja [Delegação de domínio de DNS](dns-domain-delegation.md).

Além disso, você precisa garantir que os registros de DNS para o domínio estejam em sincronia entre os dois provedores de DNS. Atualmente não há suporte para transferências de zona DNS. Os registros DNS precisam ser sincronizados usando o [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), a [API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou [ferramenta da CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Preciso delegar meu domínio para todos os quatro servidores de nome de DNS do Azure?

Sim. O DNS do Azure atribui quatro servidores de nomes para cada zona DNS, para isolamento de falhas e aumento da resiliência. Para se qualificar para o SLA de DNS do Azure, você precisará delegar seu domínio para todos os quatro servidores de nome.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de uso do DNS do Azure?

Os limites padrão abaixo se aplicam ao usar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DNS do Azure entre grupos de recursos ou entre assinaturas?

Sim. As zonas DNS podem ser movidas entre grupos de recursos ou entre assinaturas.

Não há nenhum impacto nas consultas de DNS ao mover uma zona DNS. Os servidores de nomes atribuídos à zona permanecem os mesmos e as consultas de DNS são processadas normalmente em tudo.

Para saber mais e instruções sobre como mover as zonas DNS, veja [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo leva para as alterações de DNS entrarem em vigor?

As novas zonas DNS e registros DNS são normalmente refletidas nos servidores de nomes do DNS do Azure rapidamente, dentro de alguns segundos.

As alterações nos registros de DNS existentes podem demorar um pouco mais, mas ainda assim devem ser refletidas nos servidores de nomes de DNS do Azure dentro de 60 segundos. Nesse caso, o cache de DNS fora do DNS do Azure (por clientes de DNS e resolvedores recursivos do DNS) também pode afetar o tempo necessário para uma alteração de DNS ser eficaz. A duração do cache pode ser controlada usando a propriedade de vida útil (TTL) de cada conjunto de registros.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger minhas zonas DNS contra exclusão acidental?

O DNS do Azure é gerenciado usando o Resource Manager e utiliza recursos de controle de acesso de recursos que o Azure Resource Manager fornece. O controle de acesso baseado em função pode ser usado para controlar quais usuários têm acesso de leitura ou gravação para os conjuntos de zonas e registros DNS. Bloqueios de recursos podem ser aplicados para evitar a modificação ou exclusão acidental de conjuntos de zonas e registros DNS.

Para saber mais, veja [Proteção de zonas e registros DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como posso configurar registros de SPF no DNS do Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Os Servidores de Nomes do DNS do Azure são resolvidos pelo IPv6? 

Sim. Os Servidores de Nomes do DNS do Azure têm pilha dual (ambos têm endereços IPv4 e IPv6). Para localizar o endereço IPv6 dos servidores de nomes do DNS do Azure atribuídos à zona DNS, use uma ferramenta como nslookup (por exemplo, `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Como posso configurar um nome de domínio internacional (IDN) no DNS do Azure?

Nomes de domínios internacionais (IDNs) funcionam por codificação de cada nome de DNS usando '[punycode](https://en.wikipedia.org/wiki/Punycode)'. As consultas de DNS são feitas usando esses nomes codificados para punycode.

Você pode configurar nomes de domínios internacionais (IDNs) no DNS do Azure primeiro convertendo o nome da zona ou nome do conjunto de registros para punycode. Atualmente não há suporte para conversão interna de/para punycode no DNS do Azure.

## <a name="private-dns"></a>DNS privado

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure oferece suporte a domínios 'privados'?
O suporte para domínios "privados" é implementado usando o recurso Zonas Privadas.  Esse recurso está disponível atualmente em visualização pública.  As zonas privadas são gerenciadas usando as mesmas ferramentas que as zonas DNS do Azure para a Internet, mas só podem ser resolvidas de dentro das redes virtuais especificadas.  Consulte a [visão geral](private-dns-overview.md) para obter detalhes.

Neste momento, as Zonas Privadas não têm suporte no Portal do Azure. 

Para saber mais sobre outras opções de DNS internas no Azure, veja [Resolução de nomes para VMs e Instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Qual é a diferença entre a rede virtual de registro e de rede virtual de resolução no contexto de zonas privadas? 
Você pode vincular redes virtuais a uma zona privada de DNS de duas maneiras: como uma rede virtual de registro ou como uma rede virtual de resolução. Em ambos os casos, as máquinas virtuais na rede virtual serão capazes de determinar com êxito registros na zona privada. No entanto, se você especificar uma rede virtual como uma rede virtual de registro, o Azure registrará automaticamente os registros DNS (registro dinâmico) na zona para as máquinas virtuais na rede virtual. Além disso, quando uma máquina virtual em uma rede virtual de Registro é excluída, o Azure também removerá automaticamente o registro DNS correspondente da zona privada vinculada. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>As Zonas Privadas do DNS do Azure funcionarão em todas as regiões do Azure?
Sim. As Zonas Privadas têm suporte para a resolução do DNS entre redes virtuais em todas as regiões do Azure, mesmo sem emparelhamento explícito das redes virtuais, contanto que as redes virtuais sejam especificadas como redes virtuais de resolução para a zona privada. Os clientes talvez precisem que as redes virtuais sejam emparelhadas para o tráfego TCP/HTTP para fluir de uma região para outra. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>É obrigatório ter conectividade com a Internet a partir de redes virtuais para Zonas Privadas?
Não. As Zonas Privadas funcionam em conjunto com redes virtuais e permitem que os clientes gerenciem domínios para Máquinas Virtuais ou outros recursos dentro e entre as redes virtuais. Não é obrigatório ter conectividade com a Internet para a resolução de nomes. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>A mesma zona privada pode ser usada para várias redes virtuais para resolução? 
Sim. Os clientes podem associar até 10 redes virtuais de resolução com uma única zona privada.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Uma rede virtual que pertence a uma assinatura diferente pode ser adicionada como uma rede virtual de resolução para uma zona privada? 
Sim, contanto que o usuário tenha permissão de operação de gravação nas redes virtuais e na zona DNS Privado. A permissão de gravação pode ser alocada a várias funções RBAC. Por exemplo, a função de RBAC do Colaborador da Rede Clássica tem permissões de gravação nas redes virtuais. Para saber mais sobre as funções RBAC, veja [Controle de acesso baseado em função](../role-based-access-control/overview.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Os registros DNS da máquina de virtual que são registrados automaticamente em uma zona privada serão excluídos automaticamente quando as máquinas virtuais forem excluídas pelo cliente?
Sim. Se você excluir uma máquina virtual em uma rede virtual de registro, poderemos excluir automaticamente os registros DNS que foram registrados na zona porque esta é uma rede virtual de registro. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Um registro de máquina de virtual registrado automaticamente em uma zona privada (de uma rede virtual de registro) pode ser excluído manualmente? 
Não. Neste momento, os registros DNS da máquina virtual que são automaticamente registrados em uma zona privada de uma rede virtual de registro não estão visíveis nem são editáveis pelos clientes. No entanto, você pode substituir esses registros DNS registrados automaticamente por um registro DNS criado manualmente na zona. Consulte a pergunta e a resposta a seguir que trata disso.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>O que acontece ao tentar criar um novo registro DNS manualmente em uma zona privada que tem o mesmo nome de host que uma máquina virtual existente (automaticamente registrada) em uma rede virtual de registro? 
Se você tentar criar um novo registro DNS manualmente em uma zona privada que tem o mesmo nome de host que uma máquina virtual (automaticamente registrada) existente em uma rede virtual de registro, permitiremos que o novo registro DNS substitua automaticamente o registro de máquina virtual registrado. Além disso, se você tentar excluir esse registro DNS criado manualmente posteriormente da zona, a exclusão será bem-sucedida e o registro automático ocorrerá novamente (o registro DNS será recriado automaticamente na zona), contanto que a máquina virtual ainda exista e tenha um IP privado vinculado a ele. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>O que acontece quando desvinculamos uma rede virtual de registro de uma zona privada? Os registros da máquina de virtual registrados automaticamente da rede virtual também serão removidos da zona?
Sim. Se você desvincular uma rede virtual de registro (atualizar a zona DNS para remover a rede virtual de registro associada) de uma zona privada, o Azure removerá da zona os registros de máquina de virtual registrados automaticamente. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>O que acontece quando excluímos uma rede virtual de registro (ou resolução) que está vinculada a uma zona privada? É necessário atualizar manualmente a zona privada para desvincular a rede virtual como uma rede virtual de registro (ou resolução) da zona?
Sim. Quando você exclui uma rede virtual de registro (ou resolução) sem desvinculá-la de uma zona privada primeiro, o Azure permitirá que a operação de exclusão seja bem-sucedida, mas a rede virtual não será automaticamente desvinculada da sua zona privada se houver. Você precisa desvincular manualmente a rede virtual a partir da zona privada. Por esse motivo, é recomendável primeiro desvincular sua rede virtual da sua zona privada antes de excluí-la.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>A resolução DNS que usa o FQDN padrão (internal.cloudapp.net) ainda funcionaria mesmo quando uma zona privada (por exemplo: contoso.local) estivesse vinculada a uma rede virtual? 
Sim. O recurso de Zonas Privadas não substitui as resoluções DNS padrão usando a zona internal.cloudapp.net fornecida pelo Azure e é oferecido como um recurso adicional ou aprimoramento. Para ambos os casos (seja confiando no internal.cloudapp.net fornecido pelo Azure ou em sua zona privada), é recomendável usar o FQDN da zona que você deseja determinar. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>O sufixo DNS em máquinas virtuais em uma rede virtual vinculada seria alterado para o da zona privada? 
Não. Neste momento, o sufixo DNS nas máquinas virtuais em sua rede virtual vinculada permanecerá como sufixo padrão fornecido pelo Azure ("*.internal.cloudapp.net"). No entanto, você pode alterar manualmente esse sufixo DNS em suas máquinas virtuais para o da zona privada. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Há alguma limitação para Zonas Privadas durante esta visualização?
Sim. Durante a Visualização Pública, há as seguintes limitações:
* Uma Rede virtual de Registro por Zona Privada
* Até 10 redes virtuais de Resolução por Zona Privada
* Uma determinada rede virtual somente pode ser vinculada a uma Zona Privada como uma rede virtual de Registro
* Uma determinada rede virtual pode ser vinculada a até 10 Zonas Privadas como uma rede virtual de Resolução
* Se uma rede virtual de Registro for especificada, os registros DNS das VMs dessa rede virtual que estão registradas na Zona Privada não poderão ser visualizados nem recuperados a partir de APIs/CLI/Powershell, mas os registros da VM serão registrados e serão resolvidos com êxito
* O DNS inverso somente funcionará para o espaço de IP Privado na rede virtual de Registro
* O DNS inverso para um IP Privado que não está registrado na Zona Privada (por exemplo: IP Privado para uma máquina virtual em uma rede virtual vinculada como uma rede virtual de Resolução a uma zona privada) retornará "internal.cloudapp.net" como o sufixo DNS, no entanto, esse sufixo não poderá ser resolvido.   
* A rede virtual precisa estar vazia (por exemplo, nenhuma máquina virtual com uma NIC anexada) quando inicialmente (isto é, pela primeira vez) estiver vinculando a uma Zona Privada como rede virtual de Resolução ou Registro. No entanto, a rede virtual pode não estar vazia para futuras vinculações como uma rede virtual de Registro ou de Resolução a outras zonas privadas. 
* No momento, o encaminhamento condicional não tem suporte, por exemplo, para habilitar a resolução entre as redes do Azure e OnPrem. Para obter documentação sobre como os clientes podem realizar esse cenário por meio de outros mecanismos, veja [Resolução de nomes de VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Há cotas ou limites em zonas ou registros para Zonas Privadas?
Não há limites separados no número de zonas permitido por assinatura ou número de registros definidos por região, para Zonas Privadas. As Zonas Pública e Privada são contabilizadas para os limites gerais do DNS conforme documentado [aqui](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Há suporte do Portal para Zonas Privadas?
As Zonas Privadas que já estão criadas por meio de mecanismos que não são do Portal (API/PowerShell/CLI/SDKs) ficarão visíveis no Portal do Azure, mas os clientes não poderão criar novas zonas privadas ou gerenciar associações a redes virtuais. Além disso, para redes virtuais associadas como redes virtuais de registro, os registros de VM automaticamente registrados não serão visíveis no Portal. 

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre DNS do Azure](dns-overview.md)
<br>
[Saiba mais sobre o uso do DNS do Azure para domínios privados](private-dns-overview.md)
<br>
[Saiba mais sobre zonas e registros DNS](dns-zones-records.md)
<br>
[Introdução ao DNS do Azure](dns-getstarted-portal.md)

