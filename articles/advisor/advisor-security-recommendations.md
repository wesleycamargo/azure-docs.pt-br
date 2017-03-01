---
title: "Recomendações de segurança do Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para melhorar a segurança das implantações do Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: dbdbae0f87d8d115407026b263c65c3b387fa178
ms.lasthandoff: 02/21/2017

---
# <a name="advisor-security-recommendations"></a>Recomendações de segurança do Advisor

O Advisor fornece uma exibição consistente e consolidada de recomendações para todos os seus recursos do Azure. Ele se integra à Central de Segurança do Azure para fornecer recomendações de segurança. Você pode obter recomendações de segurança na guia **Segurança** no painel do Advisor.

![guia Segurança do Advisor](./media/advisor-security-recommendations/advisor-security-tab.png)

A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela analisa periodicamente o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações. As recomendações o orientam ao longo do processo de configuração dos controles necessários. 

![guia Segurança do Advisor](./media/advisor-security-recommendations/advisor-security-recommendations.png)

Para saber mais sobre recomendações de segurança, leia [Gerenciando recomendações de segurança na Central de Segurança do Azure](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Como acessar recomendações de segurança no Azure Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Mais serviços**, no painel do menu de serviço, role para baixo até **Monitoramento e Gerenciamento** e clique em **Azure Advisor**. Isso inicia o painel do Advisor. 
3. No painel do Advisor, clique na guia **Segurança** e selecione a assinatura para a qual deseja receber recomendações.

> [!NOTE]
> Para acessar as recomendações do assistente, você deve primeiro **registrar** sua assinatura com o assistente. Uma assinatura é registrada quando um **proprietário da assinatura** inicia o painel Assistente e clica no botão **Obter recomendações**. Essa é uma **operação única**. Depois que uma assinatura for registrada, as recomendações do Assistente poderão ser acessadas por **proprietário**s, **colaborador**es ou **leitor**es de uma assinatura, grupo de recursos ou um recurso específico.

## <a name="next-steps"></a>Próximas etapas

Veja estes recursos para saber mais sobre as recomendações do Advisor:
-  [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
-  [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
-  [Recomendações de custo do Advisor](advisor-performance-recommendations.md)
 

