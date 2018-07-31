---
title: Tutorial – Arquivar logs do Azure Active Directory a uma conta de armazenamento do Azure (versão prévia) | Microsoft Docs
description: Saiba como configurar o Diagnóstico do Azure para enviar logs do Active Directory do Azure por push para uma conta de armazenamento (versão prévia)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240140"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Tutorial: Arquivar logs do Azure Active Directory a uma conta de armazenamento do Azure (versão prévia)

Neste tutorial, você aprenderá a definir as configurações de diagnóstico do Azure Monitor para encaminhar logs do Azure Active Directory para uma conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

Você precisa de:

* Uma assinatura do Azure com uma conta de armazenamento do Azure. Se não tiver uma assinatura do Azure, você poderá [inscrever-se em uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um locatário do Azure Active Directory.
* Um usuário, que é um administrador global ou administrador de segurança para esse locatário.

## <a name="archive-logs-to-an-azure-storage-account"></a>Arquivar os logs para uma conta de armazenamento do Azure

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Clique em **Azure Active Directory** -> **Atividade** -> **Logs de auditoria**. 
3. Clique em **Exportar configurações** para abrir a folha de Configurações de Diagnóstico. Clique em **Editar configuração** se você quiser alterar as configurações existentes ou clique em **Adicionar configuração de diagnóstico** para adicionar um novo. Você pode ter um máximo de três configurações. 
    ![Exportar configurações](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Exportar configurações")

4. Adicione um nome amigável para a configuração para lembrá-lo de sua finalidade. Por exemplo, "Enviar para a conta de armazenamento do Azure". 
5. Marque a caixa de seleção **Arquivar em uma conta de armazenamento** e clique em **Conta de armazenamento** para escolher a conta de armazenamento do Azure. 
6. Selecione uma conta de armazenamento e uma assinatura do Azure para a qual você deseja encaminhar os logs e, em seguida, clique em **OK** para fechar a configuração.
7. Marque a caixa de seleção **AuditLogs** para enviar os logs de auditoria para a conta de armazenamento. 
8. Marque a caixa de seleção **SignInLogs** para enviar os logs de entrada para a conta de armazenamento.
9. Use o controle deslizante para definir a retenção em seus dados de log. Por padrão, esse valor é "0" e os logs serão mantidos na conta de armazenamento indefinidamente. Caso contrário, você pode definir um valor e os eventos mais antigos do que o número de dias selecionado serão apagados automaticamente.
10. Clique em **Salvar** para salvar a configuração.
    ![Configurações de diagnóstico](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Configurações de diagnóstico")

11. Após cerca de 15 minutos, verifique se os logs são enviados por push para sua conta de armazenamento. Acesse o portal do Azure, clique em **Contas de armazenamento**, escolha a conta de armazenamento que você usou anteriormente e clique **Blobs**. 
12. Para **Logs de auditoria**, clique em **insights-log-audit**. Para **Logs de entrada**, clique em **insights-log-signin**.
    ![Conta de armazenamento](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Conta de armazenamento")

## <a name="next-steps"></a>Próximas etapas

* [Interpretar esquema dos logs de auditoria no Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretar esquema dos logs de entrada no Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)