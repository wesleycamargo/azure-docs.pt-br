---
title: "Visão geral do DNS do Azure | Microsoft Docs"
description: "Visão geral do Serviço de hospedagem de DNS no Microsoft Azure. Hospede seu domínio no Microsoft Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 87122cb432f90573fdb0a35943ce14c8d62e84a5
ms.openlocfilehash: f8ccf5c0fab1e4aca85b22b99a1a5b48f35dfcbc

---

# <a name="azure-dns-overview"></a>Visão geral do DNS do Azure

O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure.

Domínios DNS no DNS do Azure são hospedados na rede global do Azure de servidores de nomes DNS. Podemos usar a rede Anycast, para que cada consulta DNS seja atendida pelo servidor DNS mais próximo disponível. Isso fornece desempenho rápido e alta disponibilidade para seu domínio.

O serviço DNS do Azure baseia-se no Azure Resource Manager. Sendo assim, ele aproveita recursos do Resource Manager, como o controle de acesso baseado em função, os logs de auditoria e o bloqueio de recursos. Seus domínios e registros podem ser gerenciados por meio do portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de plataforma cruzada. Aplicativos que requerem gerenciamento automático de DNS podem se integrar com o serviço por meio da API REST e dos SDKs.

O DNS do Azure não dá suporte a compra de nomes de domínio. Se você deseja adquirir domínios, será necessário usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

### <a name="next-steps"></a>Próximas etapas

[Criar uma zona DNS](./dns-getstarted-create-dnszone-portal.md)




<!--HONumber=Dec16_HO3-->


