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
ms.openlocfilehash: 3d99f9869dfdfbe18f82e873bd367cc85f9414f1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2017
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Excluir um espaço de trabalho do Azure Log Analytics com o Portal do Azure
Este tópico mostra como usar o Portal do Azure para excluir um espaço de trabalho do Log Analytics que você não precisa mais. 

## <a name="to-delete-a-workspace"></a>Para excluir um espaço de trabalho 
Quando você exclui um espaço de trabalho do Log Analytics, todos os dados relacionados ao espaço de trabalho são excluídos do serviço dentro de 30 dias.  Tenha cuidado ao excluir um espaço de trabalho, pois pode haver dados e configurações importantes que podem afetar negativamente as operações do serviço.  
 
Se você for um administrador e houver vários usuários associados ao espaço de trabalho, a associação entre os usuários e o espaço de trabalho será interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando o Log Analytics com esses outros espaços. No entanto, se eles não estiverem associados a outros espaços de trabalho, precisarão criar um espaço de trabalho para usar o Log Analytics. 

1. Faça logon no [Portal do Azure](http://portal.azure.com). 
2. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
3. No painel de assinaturas do Log Analytics, selecione um espaço de trabalho e clique em **Excluir** no painel central superior.<br><br> ![Opção de exclusão do painel de propriedades do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Quando a janela de mensagem de confirmação for exibida solicitando que você confirme a exclusão do espaço de trabalho, clique em **Sim**.<br><br> ![Confirmar exclusão do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

