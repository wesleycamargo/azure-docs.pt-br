---
title: Solução do VMware CloudSimple - conexões de rede do Azure
description: Saiba mais sobre como conectar sua rede virtual do Azure à sua rede de região CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576994"
---
# <a name="azure-network-connection-overview"></a>Visão geral da conexão de rede do Azure

Quando você cria um serviço CloudSimple em uma região, ele:

* Cria um circuito de ExpressRoute do Azure e a anexa ao serviço nessa região
* Conecta-se a sua rede de região CloudSimple para sua rede virtual do Azure ou sua rede local usando o Azure ExpressRoute
* Fornece serviços de acesso em execução em sua assinatura do Azure ou sua rede local, do seu ambiente de nuvem privada

Essa conexão é a largura de banda alta com baixa latência.

## <a name="benefits"></a>Benefícios

Conexão de rede do Azure permite que você:

* Use o Azure como um destino de backup para máquinas virtuais em sua nuvem privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu armazenamento de dados de vSAN de nuvem privada.
* Use aplicativos híbridos em que a camada da web do aplicativo é executado na nuvem pública, enquanto o aplicativo e as camadas de banco de dados executado em sua nuvem privada.

## <a name="expressroute-connection-to-on-premises-network"></a>Conexão de ExpressRoute à rede local

Você pode conectar o circuito de ExpressRoute do Azure existente para sua região CloudSimple. O recurso de alcance Global do ExpressRoute é usado para se conectar os dois circuitos uns com os outros.  Uma conexão é estabelecida entre locais e circuitos de CloudSimple ExpressRoute.

Esse método estabelece uma conexão entre os dois ambientes é:

* Segurança
* Privado
* Largura de banda alta
* Baixa latência

Para criar uma conexão de ExpressRoute para uma rede local, [entre em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

* [Configurar a conexão de rede virtual](https://docs.azure.cloudsimple.com/virtual-network-connection)