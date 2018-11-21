---
title: Firewall de IP para contas do Azure Cosmos
description: Saiba como proteger os dados do Azure Cosmos DB usando políticas de controle de acesso IP para suporte de firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 3432100ab00f6ed9bc3f596085152169a8d05187
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622390"
---
# <a name="ip-firewall-for-azure-cosmos-accounts"></a>Firewall de IP para contas do Azure Cosmos

Para proteger dados armazenados na conta, o Azure Cosmos DB dá suporte a um modelo de autorização baseado em segredo que utiliza um HMAC (Message Authentication Code com Base em Hash) forte. Além disso, o Azure Cosmos DB dá suporte a controles de acesso baseados em IP para suporte de firewall de entrada. Esse modelo é semelhante às regras de firewall de um sistema de banco de dados tradicional e fornece um nível de segurança adicional à conta. Com firewalls, é possível configurar a conta do Azure Cosmos para ser acessível somente de um conjunto aprovado de computadores e/ou serviços de nuvem. O acesso aos dados armazenados no banco de dados do Azure Cosmos desses conjuntos de computadores e serviços aprovados exigirá que o chamador apresente um token de autorização válido.

## <a id="ip-access-control-overview"></a>Visão geral do controle de acesso IP

Por padrão, a conta do Azure Cosmos pode ser acessada da Internet, desde que a solicitação seja acompanhada por um token de autorização válido. Para configurar o controle de acesso baseado em política IP, o usuário deverá fornecer o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR (Roteamento entre Domínios sem Classificação) a ser incluído como a lista permitida de IPs do cliente para acessar uma determinada conta do Azure Cosmos. Depois que essa configuração for aplicada, todas as solicitações originadas de computadores fora dessa lista permitida receberão uma resposta 404 (não encontrada). Ao usar o firewall de IP, é recomendável permitir que o portal do Azure acesse a conta. O acesso é necessário para permitir o uso do Data Explorer, bem como para recuperar as métricas da conta que aparecem no portal do Azure.

É possível combinar firewall baseado em IP com sub-rede e controle de acesso VNET. Ao combiná-los, você poderá limitar o acesso a qualquer fonte que tenha um IP público e/ou de uma sub-rede específica dentro da VNET. Para saber mais sobre como usar o controle de acesso baseado em sub-rede e VNET, consulte [Acessar recursos do Azure Cosmos DB a partir de redes virtuais](vnet-service-endpoint.md).

Para resumir, o token de autorização sempre será necessário para acessar uma conta do Azure Cosmos. Se o firewall de IP e as ACLs (listas de controle de acesso) de VNET não estiverem configurados, a conta do Azure Cosmos poderá ser acessada com o token de autorização. Depois que o firewall de IP ou as ACLs de VNET, ou ambos, estiverem configurados na conta do Azure Cosmos, somente as solicitações originadas das fontes especificadas (e com o token de autorização) receberão respostas válidas. 

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá configurar o firewall de IP ou o ponto de extremidade de serviço VNET da conta, usando os documentos a seguir:

* [Como configurar o firewall de IP para a conta do Azure Cosmos](how-to-configure-firewall.md)
* [Acessar recursos do Azure Cosmos DB a partir de redes virtuais](vnet-service-endpoint.md)
* [Como configurar o ponto de extremidade de serviço de rede virtual para a conta do Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




