---
title: "Habilitar a auditoria em bancos de dados SQL na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Habilitar a auditoria em bancos de dados SQL**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c662a69f2fdbe3bd7848bdcd1eb0f9742f56c8c8


---
# <a name="enable-auditing-on-sql-databases-in-azure-security-center"></a>Habilitar a auditoria em bancos de dados SQL na Central de Segurança do Azure
A Central de Segurança do Azure recomendará que você ative a auditoria para todos os bancos de dados SQL se isso não tiver sido feito. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

Depois de ativar a auditoria, você poderá definir as configurações de Detecção de Ameaças e emails para receber alertas de segurança. A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Isso permite que você detecte e reaja a possíveis ameaças à medida que elas ocorrem.

Essa recomendação se aplica apenas ao serviço do SQL Azure, não incluindo o SQL em execução em máquinas virtuais.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.
> 
> 

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **Recomendações**, selecione **Habilitar Auditoria em bancos de dados SQL**.  Isso abre a folha **Habilitar Auditoria em bancos de dados SQL** .
   ![Enable auditing on SQL databases][1]
2. Selecione um Banco de Dados SQL no qual você deseja habilitar a auditoria. Isso abre a folha **Auditoria e detecção de ameaças**.
   ![Auditoria e detecção de ameaças][2]
3. Na folha **Auditoria e detecção de ameaças**, selecione **ATIVAR** em **Auditoria**.
   ![Ativar a auditoria e detecção de ameaças][3]
4. Siga as etapas em [Introdução à Detecção de Ameaças do Banco de Dados SQL](../sql-database/sql-database-threat-detection-get-started.md) para ativar e configurar a Detecção de Ameaças e configurar a lista de emails que receberá alertas de segurança após a detecção de atividades anormais.

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação da Central de Segurança "Habilitar auditoria em bancos de dados SQL". Para saber mais sobre como proteger seu Banco de Dados SQL, consulte:

* [Protegendo o Banco de Dados SQL](../sql-database/sql-database-security.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço de localização.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]:./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png



<!--HONumber=Dec16_HO2-->


