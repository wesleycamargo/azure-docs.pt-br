---
title: 'Início Rápido: baixar um relatório de auditoria usando o portal do Azure | Microsoft Docs'
description: Saiba como baixar um relatório de auditoria usando o portal do Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d702810ae7de03a14e50f56c2b8e819c31f3ea9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192703"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Início Rápido: Baixar um relatório de auditoria usando o portal do Azure

Neste início rápido, você aprenderá a baixar os logs de auditoria para seu locatário nas últimas 24 horas. É possível baixar até 5000 registros do portal do Azure. Os registros são classificados por mais recentes; por padrão, você obterá 5000 registros mais recentes. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de:

* Um locatário do Azure Active Directory. 
* Um usuário, que está na função de **Administrador de Segurança**, **Leitor de Segurança** ou **Administrador Global** para o locatário. Além disso, qualquer usuário no locatário pode acessar seus próprios logs de auditoria.

## <a name="quickstart-download-an-audit-report"></a>Início Rápido: Baixar um relatório de auditoria

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** do painel de navegação à esquerda e use o botão **Mudar diretório** para selecionar seu Active Directory.
3. No painel, selecione **Azure Active Directory** e, em seguida, selecione **Logs de auditoria**. 
4. Escolha **Últimas 24 horas** na lista suspensa do filtro **Intervalo de datas** e selecione **Aplicar** para exibir os logs de auditoria nas últimas 24 horas. 
5. Selecione o botão **Baixar** para baixar um arquivo CSV que contém os registros filtrados. 

![Relatórios](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Próximas etapas

* [Relatórios de atividades de entrada no portal do Azure Active Directory](concept-sign-ins.md)
* [Retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md)
* [Latências de relatórios do Azure Active Directory](reference-reports-latencies.md)
