---
title: Usando o DNS do Azure para domínios privados | Microsoft Docs
description: Visão geral do serviço de hospedagem de DNS privado no Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 7f1bd8cdcab7bdd61b3f006acf6090c53db8eda6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="using-azure-dns-for-private-domains"></a>Usando o DNS do Azure para domínios privados
O Sistema de Nomes de Domínio ou DNS, é responsável por converter (ou resolver) um nome de serviço em seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo a resolução de nomes usando a infraestrutura do Microsoft Azure.  Além dos domínios DNS para a Internet, o DNS do Azure agora também dá suporte a domínios DNS privados como um recurso de versão prévia.  
 
O DNS do Azure fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. As zonas DNS privadas permitem que você use seus próprios nomes de domínio personalizados em vez dos nomes fornecidos pelo Azure atualmente disponíveis.  O uso de nomes de domínio personalizados ajuda você a personalizar a sua arquitetura de rede virtual para melhor satisfazer às necessidades da sua organização. Esse uso fornece a resolução de nomes para VMs em uma rede virtual e entre redes virtuais. Além disso, você pode configurar nomes de zonas com uma exibição de omissão de rotas, permitindo que zonas DNS públicas e privadas compartilhem o mesmo nome.

![Visão geral de DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Benefícios

* **Remove a necessidade de soluções personalizadas de DNS.** Anteriormente, muitos clientes criaram soluções personalizadas de DNS para gerenciar zonas DNS em suas redes virtuais.  O gerenciamento de zona DNS agora pode ser feito usando a infraestrutura nativa do Azure, o que remove a sobrecarga de criar e gerenciar soluções personalizadas de DNS.

* **Usar todos os tipos de registros DNS comuns.**  O DNS do Azure dá suporte a registros A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

* **Gerenciamento automático de registro de nome do host.** Junto com a hospedagem de seus registros DNS personalizados, o Azure manterá automaticamente os registros de nome do host das VMs em redes virtuais especificadas.  Isso permite que você otimize os nomes de domínio que você usa sem a necessidade de criar soluções personalizadas de DNS ou modificar o aplicativo.

* **Resolução de nome do host entre redes virtuais.** Ao contrário dos nomes de host fornecidos pelo Azure, as zonas de DNS privado podem ser compartilhadas entre redes virtuais.  Essa capacidade simplifica os cenários entre redes e de descoberta de serviço como o emparelhamento de rede virtual.

* **Ferramentas familiares e experiência do usuário.** Para reduzir a curva de aprendizado, essa nova oferta usa as ferramentas de DNS do Azure já bem estabelecidas (PowerShell, modelos do Resource Manager, API REST).

* **Suporte a omissão de rotas DNS.** O DNS do Azure permite que você crie zonas com o mesmo nome que resolvem em diferentes respostas de dentro de uma rede virtual e da Internet pública.  Um cenário típico para omissão de rotas DNS é o fornecimento de uma versão dedicada de um serviço para uso dentro de sua rede virtual.

* **Disponível em todas as regiões do Azure.** As Zonas Privadas do DNS do Azure estão disponíveis em todas as regiões do Azure na nuvem pública do Azure. 


## <a name="capabilities"></a>Funcionalidades 
* Registro automático de máquinas virtuais a partir de uma única rede virtual vinculada a uma zona privada como uma rede virtual de Registro. As máquinas virtuais serão registradas (adicionadas) à zona privada como registros A apontando para seus IPs Privados. Além disso, quando uma máquina virtual em uma rede virtual de Registro é excluída, o Azure também removerá automaticamente o registro DNS correspondente da zona privada vinculada. Observe que as redes virtuais de Registro também agem como redes virtuais de Resolução, pois a resolução de DNS na zona funcionará em qualquer uma das máquinas virtuais da rede virtual de Registro. 
* Encaminhe resolução de DNS com suporte em redes virtuais vinculadas à zona privada como redes virtuais de Resolução. Para a resolução de DNS entre redes virtuais, não há dependência explícita de que as redes virtuais sejam emparelhadas umas com as outras. No entanto, os clientes podem querer emparelhar redes virtuais para outros cenários (por exemplo, tráfego HTTP).
* Pesquisa inversa do DNS com suporte no escopo de VNET. A pesquisa inversa do DNS para um IP Privado dentro da rede virtual atribuída a uma zona privada retornará o FQDN que inclui o nome do host/registro, bem como o nome da zona como o sufixo. 


## <a name="limitations"></a>Limitações
* 1 Rede virtual de Registro por Zona Privada
* Até 10 redes virtuais de Resolução por Zona Privada
* Uma determinada rede virtual somente pode ser vinculada a uma Zona Privada como uma rede virtual de Registro
* Uma determinada rede virtual pode ser vinculada a até 10 Zonas Privadas como uma rede virtual de Resolução
* Se uma rede virtual de Registro for especificada, os registros DNS das VMs dessa rede virtual que estão registradas na Zona Privada não poderão ser visualizados nem recuperados a partir de APIs/CLI/Powershell, mas os registros da VM serão registrados e serão resolvidos com êxito
* O DNS inverso somente funcionará para o espaço de IP Privado na rede virtual de Registro
* DNS inverso para um IP Privado que não está registrado na Zona Privada (por exemplo: IP Privado para uma máquina virtual em uma rede virtual vinculada como uma rede virtual de Resolução a uma zona privada) retornará "internal.cloudapp.net" como o sufixo DNS, no entanto, esse sufixo não poderá ser resolvido.   
* A rede virtual precisa estar vazia (por exemplo, nenhum registro de VM) quando inicialmente (ou seja, pela primeira vez) vinculando a uma Zona Privada como rede virtual de Resolução ou Registro. No entanto, a rede virtual pode não estar vazia para futuras vinculações como uma rede virtual de Registro ou de Resolução a outras zonas privadas. 
* No momento, o encaminhamento condicional não tem suporte, por exemplo, para habilitar a resolução entre as redes do Azure e OnPrem. Para obter documentação sobre como os clientes podem realizar esse cenário por meio de outros mecanismos, consulte [Resolução de nomes de VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

Adicionalmente, é recomendável que você leia as [perguntas frequentes](./dns-faq.md#private-dns) para conhecer algumas perguntas e respostas comuns sobre Zonas Privadas no DNS do Azure, incluindo o registro DNS específico e o comportamento de resolução que pode ser esperado para determinados tipos de operações. 


## <a name="pricing"></a>Preços

As zonas de DNS privado são gratuitas durante a versão prévia gerenciada. Durante a disponibilidade geral, esse recurso usará um modelo de preço baseado no uso, semelhante à oferta do DNS do Azure existente. 


## <a name="next-steps"></a>Próximas etapas

Aprenda como criar uma zona privada no DNS do Azure usando o [PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI](./private-dns-getstarted-cli.md).

Leia mais sobre alguns [cenários de Zona Privada](./private-dns-scenarios.md) comuns que podem ser realizados com Zonas Privadas no DNS do Azure.

Leia as [perguntas frequentes](./dns-faq.md#private-dns) para conhecer algumas perguntas e respostas comuns sobre Zonas Privadas no DNS do Azure, incluindo o comportamento específico esperado para determinados tipos de operações. 

Saiba mais sobre as zonas e registros DNS visitando: [Visão geral de zonas e registros DNS](dns-zones-records.md).

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

