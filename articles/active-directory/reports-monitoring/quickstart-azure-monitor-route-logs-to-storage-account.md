---
title: Tutorial – Arquivar logs do Azure Active Directory a uma conta de armazenamento do Azure (versão prévia) | Microsoft Docs
description: Saiba como configurar o Diagnóstico do Azure para enviar logs do Active Directory do Azure por push para uma conta de armazenamento (versão prévia)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 35acd3fa4815be6d60738dd5f5cef9dc9b45d748
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824551"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account-preview"></a>Tutorial: Arquivar logs do Azure AD em uma conta de armazenamento do Azure (versão prévia)

Neste tutorial, você aprende a definir as configurações de diagnóstico do Azure Monitor para encaminhar logs do Azure AD (Active Directory) para uma conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

Para usar esse recurso, você precisa de:

* Uma assinatura do Azure com uma conta de armazenamento do Azure. Se não tiver uma assinatura do Azure, você poderá [inscrever-se em uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um locatário do Azure AD.
* Um usuário que seja *administrador global* ou *administrador de segurança* do locatário do Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Arquivar os logs para uma conta de armazenamento do Azure

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. Selecione **Azure Active Directory** > **Atividade** > **Trilhas de auditoria**. 

3. Selecione **Exportar Configurações**. 

4. No painel **Configurações de Diagnóstico**, siga um destes procedimentos:
    * Para alterar as configurações existentes, selecione **Editar configuração**.
    * Para adicionar novas configurações, selecione **Adicionar configuração de diagnóstico**.  
      Você pode ter até três configurações. 

    ![Exportar configurações](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Insira um nome amigável para a configuração para lembrá-lo da finalidade (por exemplo, *Enviar para a conta de armazenamento do Azure*). 

6. Marque a caixa de seleção **Arquivar em uma conta de armazenamento** e selecione **Conta de armazenamento**. 

7. Selecione a assinatura e a conta de assinatura do Azure para onde você deseja encaminhar os logs.
 
8. Selecione **OK** para sair da configuração.

9. Siga um destes procedimentos, ou ambos:
    * Para enviar as trilhas de auditoria para a conta de armazenamento, marque a caixa de seleção **AuditLogs**. 
    * Para enviar os logs de entrada para a conta de armazenamento, marque a caixa de seleção **SignInLogs**.

10. Use o controle deslizante para definir a retenção dos dados de log. Por padrão, esse valor é *0*, o que significa que os logs são mantidos na conta de armazenamento indefinidamente. Se você definir um valor diferente, os eventos mais antigos que o número de dias selecionado serão limpos automaticamente.

11. Clique em **Salvar** para salvar a configuração.

    ![Configurações de diagnóstico](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Após cerca de 15 minutos, verifique se os logs são enviados por push para sua conta de armazenamento. Vá para o [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento**, selecione a conta de armazenamento que você usou anteriormente e selecione **Blobs**. Como **Trilhas de auditoria**, selecione **insights-log-audit**. Como **Logs de entrada**, selecione **insights-log-signin**.

    ![Conta de armazenamento](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de trilhas de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
