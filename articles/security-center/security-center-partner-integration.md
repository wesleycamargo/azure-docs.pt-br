---
title: "Integração de Parceiros na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento explica como a Central de segurança do Azure se integra com parceiros para aprimorar a segurança geral dos recursos do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 85460ea37d251b53074de9d832610d253700f2c1
ms.contentlocale: pt-br
ms.lasthandoff: 06/24/2017


---
<a id="partner-integration-in-azure-security-center" class="xliff"></a>

# Integração com Parceiros na Central de Segurança do Azure
Este documento explica como a Central de segurança do Azure se integra com parceiros para aprimorar a segurança geral e fornecer uma experiência integrada no Azure, ao mesmo tempo que aproveita o Azure Marketplace para cobrança e certificação de parceiros.

>[!NOTE] 
>A partir do início de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md) para saber mais. As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>

<a id="why-deploy-partners-solutions-from-security-center" class="xliff"></a>

## Por que implantar soluções de parceiro da Central de Segurança?

Os quatro principais motivos para utilizar a integração de parceiros na Central de Segurança são:

- **Facilidade de implantação**: implantar uma solução de parceiro seguindo a recomendação da Central de Segurança é muito mais fácil. O processo de implantação pode ser completamente automatizado usando uma topologia de rede e configuração padrão ou os clientes podem escolher uma opção semiautomatizada para permitir mais flexibilidade e personalização da configuração.
- **Detecções Integradas**: eventos de segurança das soluções de parceiro são automaticamente coletados, agregados e exibidos como parte dos incidentes e alertas da Central de Segurança. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Unificação de gerenciamento e monitoramento de integridade**: eventos de integridade integrados permitem aos clientes monitorar todas as soluções de parceiro em um relance. Gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.
- **Exportar para o SIEM**: os clientes agora podem exportar todos os alertas de parceiros e da Central de Segurança em formato CEF para sistemas SIEM locais usando a Integração de Log do Microsoft Azure (versão prévia)


<a id="what-partners-are-integrated-with-security-center" class="xliff"></a>

## Quais os parceiros estão integrados à Central de Segurança?
No momento, a Central de Segurança se integra com as soluções:

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, [Microsoft Antimalware para Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Firewall do Aplicativo Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [App Gateway WAF](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Firewall da Próxima Geração ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Avaliação de Vulnerabilidade ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Ao longo do tempo, a Central de Segurança expandirá o número de parceiros dentro dessas categorias existentes e adicionará novas categorias. 

<a id="how-to-deploy-a-partner-solution" class="xliff"></a>

## Como implantar uma solução de parceiro?

Com base na configuração de seu ambiente do Azure e na política de segurança que você definiu, a Central de Segurança pode recomendar a implantação de uma solução de parceiro. A recomendação orientará você pelo processo de seleção e instalação de uma solução de parceiro. A experiência geral de implantação, a partir desse ponto, pode variar de acordo com a solução e o parceiro. Consulte os links abaixo para obter mais informações:

- [Adicione um firewall do aplicativo Web](security-center-add-web-application-firewall.md)
- [Adicionar um Firewall de Última Geração](security-center-add-next-generation-firewall.md)
- [Instalar proteção do ponto de extremidade](security-center-install-endpoint-protection.md)
- [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md)

<a id="how-to-manage-partner-solutions" class="xliff"></a>

## Como gerenciar soluções de parceiros?

Após a implantação de uma solução de parceiro, você pode exibir informações sobre a integridade da solução e executar tarefas básicas de gerenciamento no bloco Solução de parceiro no painel principal da Central de Segurança. Para obter mais informações sobre o gerenciamento de soluções de parceiros na Central de Segurança, leia [Monitoramento de soluções de parceiro com a Central de Segurança do Azure](security-center-partner-solutions.md).

![Integração de Parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> O suporte ao Symantec Endpoint Protection é limitado à descoberta, mas não há alertas de integridade disponíveis.
>

<a id="see-also" class="xliff"></a>

## Consulte também
Neste documento, você aprendeu como integrar a solução de parceiro à Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de Segurança por Tipo na Central de Segurança do Azure](security-center-alerts-type.md)
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.

