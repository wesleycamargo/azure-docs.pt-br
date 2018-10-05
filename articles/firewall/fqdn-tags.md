---
title: Visão geral de marcas FQDN para o Firewall do Azure
description: Saiba mais sobre as marcas FQDN no Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6dc7d20d31d9399355b2b3de90ea90f2f3e07af5
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224640"
---
# <a name="fqdn-tags-overview"></a>Visão geral de marcas FQDN

Uma marca FQDN representa um grupo de nomes de domínio totalmente qualificados (FQDNs) associados aos serviços Microsoft conhecidos. Você pode usar uma marca FQDN em regras de aplicativo para permitir o tráfego de rede de saída necessário pelo firewall.

Por exemplo, para permitir manualmente a passagem do tráfego de rede do Windows Update pelo seu firewall, você precisará criar várias regras de aplicativo de acordo com a documentação da Microsoft. Com as marcas FQDN, você pode criar uma regra de aplicativo, inclusive a marca do **Windows Update**, e agora o tráfego de rede para pontos de extremidade do Microsoft Windows Update pode fluir através do firewall.

Não é possível criar suas próprias marcas FQDN, nem especificar quais FQDNs são incluídos em uma marca. A Microsoft gerencia os FQDNs englobados pela marca FQDN e atualiza a marca à medida que os FQDNs mudam. 

<!--- screenshot of application rule with a FQDN tag.-->

A tabela a seguir mostra as marcas FQDN atuais que você pode usar. A Microsoft mantém essas marcas e adiciona outras periodicamente.

|Marca FQDN  |DESCRIÇÃO  |
|---------|---------|
|Windows Update     |Permitir acesso de saída ao Microsoft Update, conforme descrito em [Como configurar um Firewall para atualizações de software](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnóstico do Windows|Permitir acesso de saída para todos os [pontos de extremidade de diagnóstico do Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|MAPS (Microsoft Active Protection Service)|Permitir acesso de saída ao [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|ASE (Ambiente do Serviço de Aplicativo)|Permite acesso de saída para tráfego de plataforma do ASE. Essa marca não cobre pontos de extremidade de Armazenamento e SQL específicos ao cliente criados pelo ASE. Eles devem ser habilitados por meio dos [Pontos de Extremidade de Serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou adicionados manualmente.|
|Serviço de Backup do Azure|Permite acesso de saída para os serviços de Backup do Azure.

> [!NOTE]
> Ao selecionar a Marca de FQDN em uma regra de aplicativo, o campo protocolo:porta precisa ser definido como **HTTPS**.

## <a name="next-steps"></a>Próximas etapas

Para saber como implantar um Firewall do Azure, confira [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md).