---
title: 'Início Rápido: baixar um relatório de auditoria usando o portal do Azure | Microsoft Docs'
description: Saiba como baixar um relatório de auditoria usando o portal do Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 09/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d6ecf62b46c25ec7cf4ab80219d1bc220e4eb4cc
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367399"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Início Rápido: baixar um relatório de auditoria usando o portal do Azure

Neste início rápido, você aprenderá a baixar os logs de auditoria para seu locatário nas últimas 24 horas.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de:

* Um locatário do Azure Active Directory. 
* Um usuário, que está na função de Administrador de Segurança, Leitor de Segurança ou Administrador Global para o locatário. Além disso, qualquer usuário no locatário pode acessar seus próprios logs de auditoria.

## <a name="quickstart-download-an-audit-report"></a>Início Rápido: baixar um relatório de auditoria

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
