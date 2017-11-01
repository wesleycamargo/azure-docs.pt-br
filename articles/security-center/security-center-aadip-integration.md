---
title: "Conectar o Azure Active Directory Identity Protection à Central de Segurança do Azure | Microsoft Docs"
description: "Saiba como a Central de Segurança do Azure integra-se ao Azure Active Directory Identity Protection."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 7562dd5e1c303a6cb97d25bda5aa080bb5643583
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Conexão do Azure Active Directory Identity Protection à Central de Segurança do Azure
Este documento ajuda você a configurar a integração entre o Azure AD (Active Directory) Identity Protection e a Central de Segurança do Azure.

## <a name="why-connect-azure-ad-identity-protection"></a>Por que conectar o Azure AD Identity Protection?
O [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) ajuda a detectar possíveis vulnerabilidades que afetam as identidades da sua organização. Quando conectado, é possível exibir alertas do Azure AD Identity Protection na Central de Segurança. Essa integração permite que você exiba, correlacione e investigue todos os alertas de segurança relacionados às suas cargas de trabalho de nuvem híbrida na Central de Segurança. 

## <a name="how-do-i-configure-this-integration"></a>Como faço para configurar essa integração?
Se a sua organização já estiver usando o Azure AD Identity Protection, siga as etapas abaixo para configurar a integração:

1. Abra o painel **Central de Segurança**.
2. No painel esquerdo, clique em **Soluções de Segurança**. A Central de Segurança detecta automaticamente se o Azure AD Identity Protection está habilitado para a sua organização.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Clique em **CONECTAR**.
4. Na página **Integrar o Azure AD Identity Protection**, role para baixo e selecione o espaço de trabalho apropriado:

    ![espaço de trabalho](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Clique em **Conectar**.

Depois de concluir essa configuração, a solução do Azure AD Identity Protection aparece na página **Soluções de Segurança** em **Soluções conectadas**. 

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar-se do Azure AD Identity Protection à Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Conexão do Microsoft Advanced Threat Analytics à Central de Segurança do Azure](security-center-ata-integration.md)
* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md) – saiba como os dados são gerenciados e protegidos na Central de Segurança do Azure.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.


