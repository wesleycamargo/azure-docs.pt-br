---
title: Excluir um espaço de trabalho do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu espaço de trabalho do Log Analytics se tiver criado um em uma assinatura pessoal ou para reestruturar seu modelo de espaço de trabalho.
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
ms.topic: article
ms.date: 05/07/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 084b8fa0addbe2ddeee699b58c9b0de1a6f27d99
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Excluir um espaço de trabalho do Azure Log Analytics com o Portal do Azure
Este artigo mostra como usar o Portal do Azure para excluir um espaço de trabalho do Log Analytics que você não precisa mais. 

## <a name="to-delete-a-workspace"></a>Para excluir um espaço de trabalho 
Quando você exclui um espaço de trabalho do Log Analytics, todos os dados relacionados ao espaço de trabalho são excluídos do serviço dentro de 30 dias.  Tenha cuidado ao excluir um espaço de trabalho, pois pode haver dados e configurações importantes que podem afetar negativamente as operações do serviço. Considere os outros serviços e fontes do Azure que armazenam seus dados no Log Analytics, como:

* Application Insights
* Central de Segurança do Azure
* Automação do Azure
* Agentes em execução em máquinas virtuais do Windows e do Linux
* Agentes em execução em computadores com Windows e Linux em seu ambiente
* System Center Operations Manager
* Soluções de gerenciamento 

Todos os agentes e grupos de gerenciamento do System Center Operations Manager configurados para responder ao espaço de trabalho continuam em um estado de órfão.  Relacione quais agentes, soluções, e outros serviços do Azure são integrados ao espaço de trabalho antes de continuar.   
 
Se você for um administrador e houver vários usuários associados ao espaço de trabalho, a associação entre os usuários e o espaço de trabalho será interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando o Log Analytics com esses outros espaços. No entanto, se eles não estiverem associados a outros espaços de trabalho, precisarão criar um espaço de trabalho para usar o Log Analytics. 

1. Faça logon no [Portal do Azure](http://portal.azure.com). 
2. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
3. No painel de assinaturas do Log Analytics, selecione um espaço de trabalho e clique em **Excluir** no painel central superior.<br><br> ![Opção de exclusão do painel de propriedades do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Quando a janela de mensagem de confirmação for exibida solicitando que você confirme a exclusão do espaço de trabalho, clique em **Sim**.<br><br> ![Confirmar exclusão do espaço de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

