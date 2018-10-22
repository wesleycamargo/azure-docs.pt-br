---
title: Excluir um workspace do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu workspace do Log Analytics se tiver criado um em uma assinatura pessoal ou para reestruturar seu modelo de workspace.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c04cc601152001dabc0cf152918c8d0ec884c7ed
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041129"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Excluir um workspace do Azure Log Analytics com o Portal do Azure
Este artigo mostra como usar o Portal do Azure para excluir um workspace do Log Analytics que você não precisa mais. 

## <a name="to-delete-a-workspace"></a>Para excluir um workspace 
Quando você exclui um workspace do Log Analytics, todos os dados relacionados ao workspace são excluídos do serviço dentro de 30 dias.  Tenha cuidado ao excluir um workspace, pois pode haver dados e configurações importantes que podem afetar negativamente as operações do serviço. Considere os outros serviços e fontes do Azure que armazenam seus dados no Log Analytics, como:

* Application Insights
* Central de Segurança do Azure
* Automação do Azure
* Agentes em execução em máquinas virtuais do Windows e do Linux
* Agentes em execução em computadores com Windows e Linux em seu ambiente
* System Center Operations Manager
* Soluções de gerenciamento 

Todos os agentes e grupos de gerenciamento do System Center Operations Manager configurados para responder ao workspace continuam em um estado de órfão.  Relacione quais agentes, soluções, e outros serviços do Azure são integrados ao workspace antes de continuar.   
 
Se você for um administrador e houver vários usuários associados ao workspace, a associação entre os usuários e o workspace será interrompida. Se os usuários estiverem associados a outros workspaces, eles poderão continuar usando o Log Analytics com esses outros workspaces. No entanto, se eles não estiverem associados a outros workspaces, precisarão criar um workspace para usar o Log Analytics. 

1. Faça logon no [Portal do Azure](http://portal.azure.com). 
2. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
3. No painel de assinaturas do Log Analytics, selecione um workspace e clique em **Excluir** no painel central superior.<br><br> ![Opção de exclusão do painel de propriedades do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Quando a janela de mensagem de confirmação for exibida solicitando que você confirme a exclusão do workspace, clique em **Sim**.<br><br> ![Confirmar exclusão do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

