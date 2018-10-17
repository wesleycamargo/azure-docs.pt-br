---
title: O que é o DNS do Azure?
description: Visão geral do Serviço de hospedagem de DNS no Microsoft Azure. Hospede seu domínio no Microsoft Azure.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: f68281f71ed525798410fb80b0d0175d38050c40
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829912"
---
# <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure.

Você não pode usar o DNS do Azure para comprar um nome de domínio. Por uma taxa anual, você pode comprar um nome de domínio usando [Aplicativos Web do Azure](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) ou um registrador de nome de domínio de terceiros. Seus domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

Os recursos abaixo estão incluídos com o DNS do Azure:

## <a name="reliability-and-performance"></a>Confiabilidade e desempenho

Domínios DNS no DNS do Azure são hospedados na rede global do Azure de servidores de nomes DNS. O DNS do Azure usa rede anycast, de modo que cada consulta DNS é respondida pelo servidor DNS mais próximo disponível, fornecendo desempenho rápido e alta disponibilidade para seu domínio.

## <a name="security"></a>Segurança

O serviço DNS do Azure se baseia no Azure Resource Manager, fornecendo recursos como:

* [controle de acesso baseado em função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control): para controlar quem tem acesso a ações específicas da sua organização.

* [log de atividades](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs): para monitorar como um usuário em sua organização modificou um recurso ou para encontrar um erro ao solucionar problemas.

* [bloqueio de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources): para bloquear uma assinatura, um recurso ou um grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem recursos críticos acidentalmente.

Para saber mais, confira [Como proteger zonas e registros DNS](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Fácil de uso

O serviço de DNS do Azure pode gerenciar os registros DNS para os serviços do Azure e também fornece o DNS para os recursos externos. O DNS do Azure é integrado ao portal do Azure e usa as mesmas credenciais, cobrança e contrato de suporte que outros serviços do Azure. 

O preço do DNS é baseado no número de zonas DNS hospedadas no Azure e o número de consultas DNS recebidas. Para saber mais sobre os preços, visite [Preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

Seus domínios e registros podem ser gerenciados usando o portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de plataforma cruzada. Aplicativos que requerem gerenciamento automatizado de DNS podem se integrar com o serviço usando a API REST e os SDKs.

## <a name="customizable-virtual-networks-with-private-domains"></a>Redes virtuais personalizáveis com domínios privados

O DNS do Azure também oferece suporte a domínios de DNS privado que está atualmente em visualização pública. Isso permite que você use seus próprios nomes de domínio personalizados em suas redes virtuais privadas em vez dos nomes fornecidos pelo Azure atualmente disponíveis.

Para ver mais informações, consulte [Como usar o DNS do Azure para domínios privados](private-dns-overview.md).

## <a name="alias-records"></a>Registros de alias

O DNS do Azure oferece suporte a conjuntos de registros de alias. Você pode usar um conjunto de registros de alias para se referir a um recurso do Azure, como um endereço IP público do Azure ou um perfil do Gerenciador de Tráfego. Se o endereço IP do recurso subjacente for alterado, o conjunto de registros de alias se atualizará perfeitamente durante a resolução de DNS. O alias registra os pontos de configuração na instância do serviço e a instância do serviço é associada a um endereço IP. 

Além disso, agora você pode apontar seu apex ou domínio nu (por exemplo, contoso.com) para um perfil do Gerenciador de Tráfego usando um registro de alias.

Para obter mais informações, consulte [registros de alias de visão geral do DNS do Azure](dns-alias.md).


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre zonas e registros DNS: [Visão geral de zonas e registros DNS](dns-zones-records.md).

* Saiba como criar uma zona no DNS do Azure: [Criar uma zona DNS](./dns-getstarted-create-dnszone-portal.md).

* Para ver as perguntas frequentes sobre o DNS do Azure, veja o [Perguntas frequentes do DNS do Azure](dns-faq.md).

