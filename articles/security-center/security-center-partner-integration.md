---
title: "Integração de Parceiros na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento explica como a Central de segurança do Azure se integra com parceiros para aprimorar a segurança geral dos recursos do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 0c946ce6a96f2e3644b9890dad5d60a35ad4bcb7
ms.openlocfilehash: 10184c52d532eb56e66212fafdea3d059b0c43e3


---
# <a name="partner-integration-in-azure-security-center"></a>Integração com Parceiros na Central de Segurança do Azure
Este documento explica como a Central de segurança do Azure se integra com parceiros para aprimorar a segurança geral e fornecer uma experiência integrada no Azure, ao mesmo tempo que aproveita o Azure Marketplace para cobrança e certificação de parceiros.

## <a name="why-deploy-partners-solutions-from-security-center"></a>Por que implantar soluções de parceiro da Central de Segurança?

Os quatro principais motivos para utilizar a integração de parceiros na Central de Segurança são:

- **Facilidade de implantação**: implantar uma solução de parceiro seguindo a recomendação da Central de Segurança é muito mais fácil. O processo de implantação pode ser completamente automatizado usando uma topologia de rede e configuração padrão ou os clientes podem escolher uma opção semiautomatizada para permitir mais flexibilidade e personalização da configuração.
- **Detecções Integradas**: eventos de segurança das soluções de parceiro são automaticamente coletados, agregados e exibidos como parte dos incidentes e alertas da Central de Segurança. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Unificação de gerenciamento e monitoramento de integridade**: eventos de integridade integrados permitem aos clientes monitorar todas as soluções de parceiro em um relance. Gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.
- **Exportar para o SIEM**: os clientes agora podem exportar todos os alertas de parceiros e da Central de Segurança em formato CEF para sistemas SIEM locais usando a Integração de Log do Microsoft Azure (visualização)


## <a name="what-partners-are-integrated-with-security-center"></a>Quais os parceiros estão integrados à Central de Segurança?
A Central de Segurança no momento se integra com os seguintes parceiros:

- Endpoint Protection (Trend Micro), 
- Firewall do aplicativo Web (Barracuda, F5, Imperva e em breve Microsoft WAF e Fortinet), 
- Soluções de firewall de próxima geração (ponto de verificação, Barracuda e em breve Fortinet e Cisco). 
- Soluções de avaliação de vulnerabilidade (Qualys – visualização). 

Ao longo do tempo, a Central de Segurança expandirá o número de parceiros dentro dessas categorias existentes e adicionará novas categorias. 

## <a name="how-to-deploy-a-partner-solution"></a>Como implantar uma solução de parceiro?

Soluções de parceiro que já foram implantadas na Central de Segurança podem ser acessadas facilmente do bloco de solução de parceiro no painel principal da Central de Segurança:

![Integração de Parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig1.png)

Para implantar uma nova solução de parceiro com base em uma recomendação da Central de Segurança, execute as seguintes etapas:

> [!NOTE]
> As etapas no exemplo a seguir pressupõem que você tem uma carga de trabalho que deseja proteger com um firewall do aplicativo Web.

1. No painel da Central de Segurança, clique no bloco **Recomendações**.
2. Na folha **recomendações**, clique em **Adicionar firewall do aplicativo Web**.
3. Clique no nome do aplicativo na folha **Adicionar um Firewall do Aplicativo Web** será aberta.
4. Na folha **Adicionar um Firewall do Aplicativo Web**, clique em **Criar Novo**.
5. A folha **Criar um Novo Firewall do Aplicativo Web** mostra uma lista dos parceiros atuais que estão oferecendo a funcionalidade de firewall do aplicativo Web.
6. Selecione a solução de parceiro apropriada e siga as etapas (que podem variar de acordo com o parceiro).

A experiência de implantação geral agora pode variar de acordo com o parceiro. Para obter mais informações sobre o gerenciamento de soluções de parceiros na Central de segurança, leia [Monitoramento de soluções de parceiro](security-center-partner-solutions.md) com a Central de Segurança do Azure.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como integrar a solução de parceiro à Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de Segurança por Tipo na Central de Segurança do Azure](security-center-alerts-type.md)
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.



<!--HONumber=Nov16_HO5-->


