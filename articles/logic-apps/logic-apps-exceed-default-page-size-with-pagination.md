---
title: Obtenha mais dados, itens ou registros de paginação - aplicativos lógicos do Azure
description: Configurar a paginação para exceder o limite de tamanho de página padrão para ações do conector em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476535"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obtenha mais dados, itens ou registros usando a paginação em aplicativos lógicos do Azure

Quando você recuperar dados, itens ou registros por meio de uma ação do conector no [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), você poderá obter conjuntos de resultados tão grande que a ação não retorna todos os resultados ao mesmo tempo. Com algumas ações, o número de resultados pode exceder o tamanho de página padrão do conector. Nesse caso, a ação retorna apenas a primeira página de resultados. Por exemplo, o tamanho de página padrão para o conector do SQL Server **obter linhas** ação é 2048, mas podem variar com base em outras configurações.

Algumas ações permitem que você ative uma *paginação* configuração para que seu aplicativo lógico pode recuperar mais resultados até o limite de paginação, mas retornar esses resultados como uma única mensagem quando a ação for concluída. Quando você usa a paginação, você deve especificar uma *limite* valor, que é o número alvo de resultados que você deseja que a ação para retornar. A ação recupera os resultados até atingir o limite especificado. Quando o número total de itens é menor que o limite especificado, a ação recupera todos os resultados.

Ativar as páginas de recupera de configuração de paginação de resultados com base no tamanho da página de um conector. Esse comportamento significa que às vezes, você poderá obter resultados mais que o limite especificado. Por exemplo, ao usar o SQL Server **obter linhas** ação, que dá suporte à configuração de paginação:

* Tamanho da página da ação padrão é 2048 registros por página.
* Suponha que você tenha 10.000 registros e especificar 5000 registros como o mínimo.
* Paginação obtém páginas de registros, então, para obter no mínimo o mínimo especificado, a ação retorna registros de 6144 (3 páginas x 2048 registros), não os registros de 5000.

Aqui está uma lista com apenas alguns dos conectores em que você pode exceder o tamanho de página padrão para ações específicas:

* [Armazenamento de Blobs do Azure](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Equipes da Microsoft](https://docs.microsoft.com/connectors/teams/)
* [Banco de dados Oracle](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico e a ação onde você deseja ativar a paginação. Se você não tiver um aplicativo lógico, consulte [guia de início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Ativar a paginação

Para determinar se uma ação dá suporte à paginação no Designer do aplicativo lógico, verifique as configurações da ação para o **paginação** configuração. Este exemplo mostra como ativar a paginação no SQL Server **obter linhas** ação.

1. No canto superior direito da ação, escolha as reticências (**...** ) botão e, em seguida, selecione **configurações**.

   ![Abra configurações da ação](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se a ação dá suporte à paginação, a ação mostra a **paginação** configuração.

1. Alterar o **paginação** definindo a partir **Off** para **em**. No **limite** propriedade, especifique um valor inteiro para o número alvo de resultados que você deseja que a ação para retornar.

   ![Especifique o número mínimo de resultados a serem retornados](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Quando estiver pronto, escolha **Concluído**.

## <a name="workflow-definition---pagination"></a>Definição de fluxo de trabalho - paginação

Quando você ativar a paginação para uma ação que dá suporte a esse recurso, a definição de fluxo de trabalho do aplicativo lógico inclui o `"paginationPolicy"` propriedade juntamente com o `"minimumItemCount"` propriedade em que ação `"runtimeConfiguration"` propriedade, por exemplo:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Obter suporte

Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
