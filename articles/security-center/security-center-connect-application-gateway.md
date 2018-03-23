---
title: Conexão do Gateway de Aplicativo do Microsoft Azure à Central de Segurança do Azure | Microsoft Docs
description: Saiba como integrar o Gateway de Aplicativo e a Central de Segurança do Azure para aumentar a segurança geral de seus recursos.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Conexão do Gateway de Aplicativo do Microsoft Azure à Central de Segurança do Azure
Este documento ajuda você a configurar a integração com o firewall do aplicativo web (WAF) do Gateway de Aplicativo e a Central de Segurança.

## <a name="why-connect-application-gateway"></a>Por que conectar o Gateway de Aplicativo?
O WAF no Gateway de Aplicativo protege aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão. A Central de Segurança se integra com o Gateway de Aplicativo para impedir e detectar ameaças a aplicativos web desprotegidos em seu ambiente.

## <a name="how-do-i-configure-this-integration"></a>Como faço para configurar essa integração?
A Central de Segurança descobre instâncias de WAF previamente implantadas na assinatura. Conecte essas soluções à Central de Segurança para permitir a integração de alertas.

> [!NOTE]
> O WAF do Gateway de Aplicativo também pode ser provisionado nas **Recomendações** da Central de Segurança conforme descrito em [Adicionar um firewall do aplicativo web](security-center-add-web-application-firewall.md).
>
>

1. Faça logon no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Central de Segurança**. **Central de Segurança - Visão geral** é aberto.

3. Em **Visão Geral**, selecione **Soluções de Segurança**.

  ![Visão geral da Central de Segurança](./media/security-center-connect-application-gateway/overview.png)

4. Em **Soluções descobertas**encontre Firewall do Aplicativo Web baseado em SaaS da Microsoft e selecione **Conectar**.

  ![Soluções descobertas](./media/security-center-connect-application-gateway/connect.png)

5. **Conectar-se à solução de WAF** é aberto.  Selecione **Conectar** para integrar o WAF e a Central de Segurança.

  ![Conectar-se à solução de WAF](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a integrar o WAF de Gateway de Aplicativo à Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Integrar soluções de segurança na Central de Segurança](security-center-partner-integration.md)
* [Conexão do Microsoft Advanced Threat Analytics à Central de Segurança](security-center-ata-integration.md)
* [Conexão do Azure Active Directory Identity Protection à Central de Segurança](security-center-aadip-integration.md)
* [Monitoramento da integridade de segurança na Central de Segurança](security-center-monitoring.md).
* [Monitoramento das soluções de parceiros na Central de Segurança](security-center-partner-solutions.md).
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md).
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/).
