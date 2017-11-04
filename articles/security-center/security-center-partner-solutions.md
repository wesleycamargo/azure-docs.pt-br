---
title: "Gerenciamento de soluções de parceiros conectados na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento fornece uma orientação de como a Central de Segurança do Azure permite que você monitore rapidamente o status de integridade de suas soluções de parceiro integradas com sua assinatura do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Monitoramento das soluções de parceiros com a Central de Segurança do Azure
Este artigo explica como gerenciar e monitorar soluções de segurança conectadas na Central de Segurança do Azure.

## <a name="monitoring-partner-solutions"></a>Monitoramento das soluções de parceiros
Para monitorar o status de integridade de soluções de segurança conectadas e executar o gerenciamento básico:

1. Em **Central de Segurança - Visão Geral**, selecione **Soluções de segurança**.

  ![Selecionar soluções de segurança][1]

  A seção **Soluções conectadas** inclui soluções de segurança conectadas à Central de Segurança e informações sobre o status de integridade de cada solução.

  ![Soluções de parceiros][2]

   O status de uma solução de parceiro pode ser:

   * Íntegro (verde) - não há qualquer problema de integridade.
   * Não íntegro (vermelho) - há um problema de integridade que requer atenção imediata
   * Problemas de integridade (laranja) - a solução interrompeu o envio de relatórios sobre sua integridade.
   * Não relatado (cinza) - a solução não relatou nada ainda. O status da solução pode não ser relatado se ela tiver sido conectada recentemente e se ainda estiver sendo implantada, ou se não houver nenhum dado de integridade disponível.

   > [!NOTE]
   > Se os dados de status de integridade não estiverem disponíveis, a Central de Segurança mostrará a data e a hora do último evento recebido para indicar se a solução está reportando ou não. Se não houver dados de integridade disponíveis e se nenhum alerta for tiver sido recebido nos últimos 14 dias, a Central de Segurança indica que a solução não está íntegra ou não está reportando.
   >
   >

2. Selecione **EXIBIR** para obter informações e opções adicionais, o que inclui:

  - **Console da solução**. Abre a experiência de gerenciamento para esta solução.
  - **Vincular VM**. Abre a folha Vincular Aplicativos. Nela, você pode conectar recursos à solução de parceiro.
  - **Excluir solução**.
  - **Configurar**.

   ![Detalhes da solução de parceiro][3]

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a gerenciar e a monitorar soluções de segurança conectadas na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Visão geral das soluções de segurança](security-center-partner-integration.md) — saiba como conectar e gerenciar soluções de segurança.
* [Conexão do Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) — saiba como conectar os alertas do ATA.
* [Conexão do Azure Active Directory (AD) Identity Protection ](security-center-aadip-integration.md) — saiba como conectar os alertas do Azure AD Identity Protection.
* [Integração de parceiro e soluções](security-center-partner-integration.md) - obtenha uma visão geral da integração de outras soluções de segurança.
* [Gerenciar e responder aos alertas de segurança](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
