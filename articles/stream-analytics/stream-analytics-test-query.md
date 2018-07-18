---
title: Testar um trabalho do Azure Stream Analytics com dados de exemplo
description: Como testar as consultas em trabalhos do Stream Analytics.
keywords: Este artigo descreve como usar o portal do Azure para testar um trabalho do Azure Stream Analytics, uma entrada de exemplo e fazer o upload de dados de exemplo.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2018
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testar uma consulta do Stream Analytics com dados de exemplo

Usando o Azure Stream Analytics, é possível carregar dados de exemplo e testar consultas no portal do Azure sem iniciar ou interromper um trabalho.

## <a name="upload-sample-data-and-test-the-query"></a>Carregar dados de exemplo e testar a consulta

1. Entre no Portal do Azure. 

2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, sob o título **Topologia de Trabalho**, selecione **Consulta** para abrir a janela Editor de consultas. 

4. Para testar a consulta com dados de entrada de exemplo, clique com o botão direito do mouse em qualquer uma das entradas.  Em seguida, selecione **Carregar dados de exemplo do arquivo**.

   Os dados devem ser formatados em JSON somente. Se os dados estiverem em um formato diferente, como CSV, você deverá convertê-los em JSON antes de carregar. Você pode usar qualquer ferramenta de conversão de código-fonte aberto, como [conversor de CSV para JSON](http://www.convertcsv.com/csv-to-json.htm), para converter seus dados em JSON.

    ![consulta de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Depois que o upload for concluído, selecione **Teste** para testar essa consulta em relação aos dados de exemplo que você forneceu.

    ![dados de exemplo de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Se você precisar testar a saída do teste para uso posterior, a saída da consulta será exibida no navegador com um link para os resultados do download. 

7. Modifique iterativamente sua consulta e teste-a novamente para ver como a saída muda.

   ![A saída do exemplo do editor de consultas do Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Quando você usa várias saídas em uma consulta, os resultados são mostrados em guias separadas, e você pode alternar facilmente entre elas.

8. Depois de verificar os resultados mostrados no navegador, **Salve** sua consulta. Em seguida, **Inicie** o trabalho e permita que ele processe os eventos de entrada.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
