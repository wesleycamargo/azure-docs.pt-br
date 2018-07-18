---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678164"
---
### <a name="set-up-pagination"></a>Configurar a paginação

Para alguns conectores e suas ações que recuperam vários itens, os resultados podem exceder o tamanho de página padrão do conector. Nesse caso, a ação retorna apenas a primeira página de resultados. Por exemplo, o tamanho de página padrão para a ação **SQL Server - Obter linhas** é 2048, mas pode ser diferente com base em outras configurações. Para certificar-se de que você obteve todos os registros, ative a configuração de **Paginação** para essa ação. Essa configuração tem seu aplicativo lógico solicitando o conector para os registros restantes, mas retorna todos os resultados como mensagem única quando a ação terminar. 

Aqui estão apenas alguns conectores onde você pode ativar a paginação para ações específicas: 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Banco de dados Oracle</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

Aqui está um exemplo para a ação **Obter linhas**:

1. Para localizar se a ação dá suporte à paginação, abra as **Configurações** da ação. 

   ![Na ação, abra “Configurações”](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Se a ação dá suporte à paginação, altere a configuração **Paginação** de **Off** para **On**. Para certificar-se de que a ação retorna um conjunto mínimo de resultados, especifique um valor para **Limite**.

   ![Especifique o número mínimo de retorno da ação dos resultados](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. Quando estiver pronto, escolha **Concluído**.