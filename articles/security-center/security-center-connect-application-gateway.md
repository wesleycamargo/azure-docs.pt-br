---
title: Conexão do Gateway de Aplicativo do Microsoft Azure à Central de Segurança do Azure | Microsoft Docs
description: Saiba como integrar o Gateway de Aplicativo e a Central de Segurança do Azure para aumentar a segurança geral de seus recursos.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: 5638b71147592ae71c741ca86da68ddfec668af5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299059"
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

2. No menu **Microsoft Azure**, selecione **Central de Segurança**.

3. Em **HIGIENIZAÇÃO DE SEGURANÇA DE RECURSO**, selecione **Soluções de segurança**.

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
