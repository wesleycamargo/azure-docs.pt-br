---
title: "Excluir um espaço de trabalho do Azure Log Analytics | Microsoft Docs"
description: "Saiba como excluir seu espaço de trabalho do Log Analytics se tiver criado um em uma assinatura pessoal ou para reestruturar seu modelo de espaço de trabalho."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 4f455e26f078360f17f8118f8b5b1db5bd75d473
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Excluir um espaço de trabalho do Azure Log Analytics com o Portal do Azure
Este tópico mostra como usar o Portal do Azure para excluir um espaço de trabalho do Log Analytics que você não precisa mais. 

## <a name="to-delete-a-workspace"></a>Para excluir um espaço de trabalho 
Quando você exclui um espaço de trabalho do Log Analytics, todos os dados relacionados ao espaço de trabalho são excluídos do serviço dentro de 30 dias.  Tenha cuidado ao excluir um espaço de trabalho, pois pode haver dados e configurações importantes que podem afetar negativamente as operações do serviço.  
 
Se você for um administrador e houver vários usuários associados ao espaço de trabalho, a associação entre os usuários e o espaço de trabalho será interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando o Log Analytics com esses outros espaços. No entanto, se eles não estiverem associados a outros espaços de trabalho, precisarão criar um espaço de trabalho para usar o Log Analytics. 

1. Faça logon no [Portal do Azure](http://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
3. No painel de assinaturas do Log Analytics, selecione um espaço de trabalho e clique em **Excluir** no painel central superior.<br><br> ![Opção de exclusão do painel de propriedades do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Quando a janela de mensagem de confirmação for exibida solicitando que você confirme a exclusão do espaço de trabalho, clique em **Sim**.<br><br> ![Confirmar exclusão do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

