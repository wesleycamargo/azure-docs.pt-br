---
title: Conectar à Caixa - Aplicativos Lógicos do Azure | Microsoft Docs
description: Criar e gerenciar arquivos com APIs REST de caixa e Aplicativos Lógicos do Azure
author: ecfan
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 971d38fa0fbd47f0deb815577033bbe684aac32f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312569"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Criar e gerenciar arquivos no Box com os Aplicativos Lógicos do Azure

Este artigo mostra como você pode criar e gerenciar seus arquivos no Box de dentro de um aplicativo lógico com o conector do Box. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos e outras ações, por exemplo:

* Compile seu fluxo de negócios baseado nos dados que obtém do Box.

* Dispare o fluxo de trabalho e tarefas automatizadas quando um arquivo é criado ou atualizado.

* Execute ações para copiar um arquivo, excluir um arquivo e muito mais.

  Quando essas ações obtêm uma resposta, elas disponibilizam a saída para outras ações. 
  Por exemplo, quando um arquivo é alterado no Box, você pode enviar esse arquivo em email usando o Office 365.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Box](https://www.box.com/home)

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* O aplicativo lógico no qual você deseja acessar a conta do Box. Para iniciar seu aplicativo lógico com um gatilho do Box, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se ainda não estiver familiarizado com o Aplicativo Lógico, veja [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente conhecido como Swagger) de arquivos, consulte o [página de referência do conector](/connectors/box/).

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)