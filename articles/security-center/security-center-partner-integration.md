---
title: "Integração de parceiros na Central de Segurança do Azure | Microsoft Docs"
description: "Saiba como a Central de Segurança do Azure se integra aos parceiros para aprimorar a segurança geral dos recursos do Azure."
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
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: pt-br
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Integração com Parceiros na Central de Segurança do Azure

Neste artigo, descreveremos como a Central de Segurança do Azure se integra aos parceiros para ajudá-lo a melhorar a segurança geral. A Central de Segurança oferece uma experiência integrada no Azure e aproveita o Azure Marketplace para certificação de parceiro e cobrança.

> [!NOTE] 
> A partir de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Migração de plataforma da Central de Segurança do Azure](security-center-platform-migration.md). As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Por que implantar soluções de parceiro da Central de Segurança

Os quatro principais motivos para utilizar a integração de parceiros na Central de Segurança são:

- **Facilidade de implantação**. Implantar uma solução de parceiro seguindo a recomendação da Central de Segurança é muito mais fácil. O processo de implantação pode ser completamente automatizado por meio de uma topologia de rede e de configuração padrão. Como alternativa, os clientes podem escolher uma opção semiautomatizada para obter mais flexibilidade e personalização.
- **Detecções integradas**. Os eventos de segurança das soluções de parceiro são automaticamente coletados, agregados e exibidos como parte dos incidentes e alertas da Central de Segurança. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Monitoramento e gerenciamento de integridade unificados**. Os clientes podem usar eventos de integridade integrados para monitorar todas as soluções de parceiros em um relance. O gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.
- **Exportar para o SIEM**. Os clientes podem exportar todos os alertas de parceiro e da Central de Segurança no Formato de Evento Comum (CEF) para sistemas de Gerenciamento de Informações e Eventos de Segurança (SIEM) locais usando a integração de log do Azure (versão prévia).


## <a name="partners-that-integrate-with-security-center"></a>Parceiros que se integram à Central de Segurança

No momento, a Central de Segurança se integra a estas soluções:

- Proteção de ponto de extremidade ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec e [Antimalware da Microsoft para Serviços de Nuvem e Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Firewall de aplicativo Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [Gateway de Aplicativo do Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Firewall de próxima geração ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Avaliação de vulnerabilidades ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Ao longo do tempo, a Central de Segurança expandirá o número de parceiros dentro dessas categorias e adicionará novas categorias. 

## <a name="deploy-a-partner-solution"></a>Implantar uma solução de parceiro

Com base na configuração de seu ambiente do Azure e na política de segurança que você definiu, a Central de Segurança pode recomendar a implantação de uma solução de parceiro. A recomendação da Central de Segurança orienta você pelo processo de seleção e instalação de uma solução de parceiro. A experiência geral de implantação pode variar, dependendo do tipo de solução e do parceiro que você usar. Para obter mais informações, consulte os seguintes artigos:

- [Instalar o Endpoint Protection](security-center-install-endpoint-protection.md)
- [Adicione um firewall do aplicativo Web](security-center-add-web-application-firewall.md)
- [Adicionar um firewall de próxima geração](security-center-add-next-generation-firewall.md)
- [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Gerenciar soluções de parceiros

Após a implantação, para exibir informações sobre a integridade da solução e executar o gerenciamento básico de tarefas, na folha **Central de Segurança**, selecione a opção **Soluções de parceiro**. Para saber mais sobre o gerenciamento de soluções de parceiros na Central de Segurança, veja [Monitorar soluções de parceiro com a Central de Segurança do Azure](security-center-partner-solutions.md).

![Integração de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> Suporte à proteção de ponto de extremidade Symantec é limitado à descoberta. Nenhum alerta de integridade disponível.
>

## <a name="see-also"></a>Consulte também

Neste artigo, você aprendeu a integrar as soluções de parceiro à Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de planejamento e operações da Central de Segurança](security-center-planning-and-operations-guide.md)
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de segurança por tipo na Central de Segurança](security-center-alerts-type.md)
* [Monitoramento da integridade de segurança na Central de Segurança](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Monitoramento das soluções de parceiros na Central de Segurança](security-center-partner-solutions.md). Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Obtenha respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.

