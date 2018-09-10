---
title: Usar DNS do Azure para domínios privados | Microsoft Docs
description: Uma visão geral do serviço de hospedagem de DNS privado no Microsoft Azure.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: 2ab7070a4cf46dae543af8d3e1d688e12ec1eb2a
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173635"
---
# <a name="use-azure-dns-for-private-domains"></a>Usar DNS do Azure para domínios privados
O Sistema de Nomes de Domínio ou DNS, é responsável por converter (ou resolver) um nome de serviço em seu endereço IP. Um serviço de hospedagem para domínios DNS, o DNS do Azure fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Além de dar suporte a domínios DNS para a Internet, o DNS do Azure agora também dá suporte a domínios DNS privados como um recurso de versão prévia. 
 
O DNS do Azure fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao usar zonas DNS privadas você poderá usar seus próprios nomes de domínio personalizados, em vez dos nomes fornecidos pelo Azure atualmente disponíveis. O uso de nomes de domínio personalizados ajuda você a personalizar a sua arquitetura de rede virtual para melhor satisfazer às necessidades da sua organização. Esse uso fornece resolução de nomes para VMs (máquinas virtuais) em uma rede virtual e entre redes virtuais. Além disso, você pode configurar nomes de zonas com uma exibição de omissão de rotas, o que permite que uma zona DNS privada e pública compartilhe o mesmo nome.

![Visão geral de DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Benefícios

O DNS do Azure fornece os seguintes benefícios:

* **Remove a necessidade de soluções personalizadas de DNS**. Anteriormente, muitos clientes criaram soluções personalizadas de DNS para gerenciar zonas DNS em suas redes virtuais. Agora, é possível executar o gerenciamento de zona DNS usando a infraestrutura nativa do Azure, o que elimina a responsabilidade de criar e gerenciar soluções DNS personalizadas.

* **Usar todos os tipos de registros DNS comuns**. O DNS do Azure dá suporte a registros A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

* **Gerenciamento automático de registro de nome do host**. Junto com a hospedagem de seus registros DNS personalizados, o Azure manterá automaticamente os registros de nome do host das VMs em redes virtuais especificadas. Nesse cenário, é possível otimizar os nomes de domínio usados sem precisar criar soluções DNS personalizadas ou modificar aplicativos.

* **Resolução de nome do host entre redes virtuais**. Ao contrário dos nomes de host fornecidos pelo Azure, as zonas de DNS privado podem ser compartilhadas entre redes virtuais. Essa capacidade simplifica os cenários entre redes e de descoberta de serviço, como o emparelhamento de rede virtual.

* **Ferramentas familiares e experiência do usuário**. Para reduzir a curva de aprendizado, essa nova oferta usa ferramentas bem estabelecidas de DNS do Azure (PowerShell, modelos do Azure Resource Manager e API REST).

* **Suporte a omissão de rotas DNS**. Com o DNS do Azure, você pode criar zonas com o mesmo nome que resolvem respostas diferentes a partir de uma rede virtual e da Internet pública. Um cenário típico para omissão de rotas DNS é o fornecimento de uma versão dedicada de um serviço para uso dentro de sua rede virtual.

* **Disponível em todas as regiões do Azure**. O recurso de zonas privadas do DNS do Azure está disponível em todas as regiões do Azure na nuvem pública do Azure. 


## <a name="capabilities"></a>Funcionalidades

O DNS do Azure fornece os seguintes recursos:
 
* **Registro automático de máquinas virtuais a partir de uma única rede virtual vinculada a uma zona privada como uma rede virtual de registro**. As máquinas virtuais são registradas (adicionadas) à zona privada como registros A apontando para os IPs privados. Quando uma máquina virtual em uma rede virtual de registro é excluída, o Azure também remove automaticamente o registro DNS correspondente da zona privada vinculada. 

  > [!NOTE]
  > Por padrão, as redes virtuais de registro também atuam como redes virtuais de resolução, pois a resolução de DNS contra a zona funciona de qualquer uma das máquinas virtuais dentro da rede virtual de registro. 

* **A resolução de DNS direta tem suporte em redes virtuais vinculadas à zona privada como redes virtuais de resolução**. Para a resolução de DNS entre redes virtuais, não há dependência explícita de que as redes virtuais sejam emparelhadas umas com as outras. No entanto, os clientes podem querer emparelhar redes virtuais para outros cenários (por exemplo, tráfego HTTP).

* **Pesquisa inversa do DNS tem suportada dentro do escopo de rede virtual**. A pesquisa inversa do DNS para um IP privado dentro da rede virtual atribuída a uma zona privada retornará o FQDN que inclui o nome do host/registro, bem como o nome da zona como o sufixo. 


## <a name="limitations"></a>Limitações

O DNS do Azure está sujeito às seguintes limitações:

* Apenas uma rede virtual de registro é permitida por zona privada.
* Até 10 redes virtuais de resolução são permitidas por zona privada.
* Uma rede virtual específica pode ser vinculada a apenas uma zona privada como uma rede virtual de registro.
* Uma rede virtual específica pode ser vinculada a até 10 zonas privadas como uma rede virtual de resolução.
* Se uma rede virtual de registro for especificada, os registros DNS das VMs dessa rede virtual registrados na zona privada não poderão ser visualizados nem recuperados nas APIs da CLI do Azure e Azure Powershell, mas os registros da VM serão realmente registrados e serão resolvidos com êxito.
* O DNS inverso funciona apenas para o espaço IP privado na rede virtual de registro.
* O DNS inverso para um IP privado que não está registrado na zona privada (por exemplo, um IP privado para uma máquina virtual em uma rede virtual que esteja vinculada como uma rede virtual de resolução a uma zona privada) retorna *internal.cloudapp.net* como o sufixo DNS. No entanto, esse sufixo não pode ser resolvido. 
* A rede virtual precisa estar vazia (ou seja, não existem registros de VM) quando inicialmente (isto é, pela primeira vez) vincula uma zona privada como uma rede virtual de registro ou resolução. No entanto, a rede virtual pode não estar vazia para futuras vinculações como uma rede virtual de registro ou de resolução a outras zonas privadas. 
* No momento, o encaminhamento condicional não tem suporte (por exemplo, para habilitar a resolução entre as redes OnPrem e Azure). Para obter informações sobre como os clientes podem realizar esse cenário por meio de outros mecanismos, consulte [Resolução de nomes de VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo o registro específico de DNS e o comportamento de resolução que pode ser esperado para determinados tipos de operações, consulte as [Perguntas frequentes](./dns-faq.md#private-dns).  


## <a name="pricing"></a>Preços

O recurso de zonas DNS privadas é gratuito durante a visualização pública. Durante a disponibilidade geral, o recurso oferecerá um modelo de preços com base no uso, semelhante ao da oferta de DNS do Azure existente. 


## <a name="next-steps"></a>Próximas etapas

Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que pode ser esperado para determinados tipos de operações, consulte as [Perguntas frequentes](./dns-faq.md#private-dns). 

Saiba mais sobre zonas e registros DNS, visitando [Visão geral de zonas e registros DNS](dns-zones-records.md).

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure. 

