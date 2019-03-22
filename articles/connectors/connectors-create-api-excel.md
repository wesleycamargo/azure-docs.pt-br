---
title: Conectar-se para o Excel Online – aplicativos lógicos do Azure
description: Gerenciar dados com as APIs REST do Excel Online e os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 28739ad65462acc9f2d2ed7db1e9ed14d19f032c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314512"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gerenciar dados do Excel Online com Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Excel Online, você pode criar tarefas automatizadas e fluxos de trabalho com base em seus dados no Excel Online for Business ou OneDrive. Esse conector fornece ações que ajudam você a trabalhar com seus dados e gerenciar planilhas, por exemplo:

* Crie novas planilhas e tabelas.
* Obter e gerenciar planilhas, tabelas e linhas.
* Adicionar linhas únicas e colunas de chave.

Em seguida, você pode usar as saídas dessas ações com ações para outros serviços. Por exemplo, se você usa uma ação que cria planilhas a cada semana, você pode usar outra ação que envia email de confirmação por meio do conector do Office 365 Outlook.

Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Os conectores do [Excel Online for Business](/connectors/excelonlinebusiness/) e do [Excel Online for OneDrive](/connectors/excelonline/) funcionam com os Aplicativos Lógicos do Azure e diferem do [conector do Excel para o PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Uma [conta do Office 365](https://www.office.com/) para sua conta corporativa ou conta pessoal Microsoft

  Os dados do Excel podem existir como um arquivo CSV (valores separados por vírgulas) em uma pasta de armazenamento, por exemplo, no OneDrive. 
  Você também pode usar o mesmo arquivo CSV com o [conector de arquivo simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar os dados do Excel Online. Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, selecione um gatilho separado, por exemplo, o gatilho **Recorrência**.

## <a name="add-excel-action"></a>Adicionar ação de Excel

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativo lógico, se já não estiver aberto.

1. No gatilho, escolha **Nova etapa**.

1. Na caixa de pesquisa, insira "excel" como filtro. Na lista de ações, selecione a ação desejada.

1. Se solicitado a entrar em sua conta do Office 365, escolha **Entrar**.

   Suas credenciais autorizam o aplicativo lógico a criar uma conexão com o Excel Online e acessar seus dados.

1. Continue fornecendo os detalhes necessários para a ação selecionada, criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente conhecido como Swagger) arquivos, consulte estas páginas de referência do conector:

* [Excel Online for Business](/connectors/excelonlinebusiness/)
* [Excel Online for OneDrive](/connectors/excelonline/)

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
