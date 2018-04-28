---
title: Testar o trabalho do Azure Stream Analytics com dados de exemplo | Microsoft Docs
description: Como testar as consultas em trabalhos do Stream Analytics.
keywords: testar um trabalho, amostragem de entrada, carregar dados de exemplo
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Testar a consulta do Stream Analytics com dados de exemplo

Usando o Azure Stream Analytics, é possível carregar dados de exemplo e testar consultas no portal sem iniciar ou interromper um trabalho.

## <a name="upload-sample-data-and-test-the-query"></a>Carregar dados de exemplo e testar a consulta

1. Navegue para um dos trabalhos existentes do Stream Analytics > clique em **Consulta** para abrir a janela do editor de Consultas. 

2. Para testar a consulta com dados de entrada de exemplo, clique com o botão direito do mouse em qualquer uma das entradas e, em seguida, selecione **Carregar dados de exemplo do arquivo**. No momento, você pode carregar apenas os dados formatados em JSON. Se os dados estiverem em um formato diferente, como CSV, você deverá convertê-los em JSON antes de carregar. Você pode usar qualquer ferramenta de conversão de código-fonte aberto, como [conversor de CSV para JSON](http://www.convertcsv.com/csv-to-json.htm), para converter seus dados em JSON.

    ![consulta de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. Depois que o upload for concluído, clique em **Teste** para testar essa consulta em relação aos dados de exemplo que você forneceu.

    ![dados de exemplo de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Se você deseja salvar a saída do teste para uso posterior, a saída da consulta é exibida no navegador com um link para os resultados do download. Agora você pode modificar fácil e iterativamente sua consulta e testá-la repetidamente para ver como a saída muda.

   ![A saída do exemplo do editor de consultas do Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Ao usar várias saídas em uma consulta, você pode ver os resultados de cada saída separadamente e alterná-los facilmente. Após verificar os resultados mostrados no navegador, será possível salvar a consulta, iniciar o trabalho e permitir que ele processe eventos sem erros.
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
