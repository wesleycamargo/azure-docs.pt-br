---
title: Monitorar trabalhos no Azure Data Lake Analytics usando o portal do Azure | Microsoft Docs
description: 'Saiba como usar o Portal do Azure para solucionar problemas com trabalhos da Análise do Data Lake. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 14b1f4ec9dff78e4b5d2480755a4b1f2579ec135
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33884772"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorar trabalhos no Azure Data Lake Analytics usando o portal do Azure

**Para ver todos os trabalhos**

1. No portal do Azure, clique em **Microsoft Azure** no canto superior esquerdo.
2. Clique no bloco com o nome da conta da Análise Data Lake.  O resumo do trabalho é mostrado no bloco **Gerenciamento de Trabalhos** .

    ![Gerenciamento de trabalhos da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    O Gerenciamento do trabalho mostra rapidamente o status do trabalho. Observe que há um trabalho com falha.
3. Clique no bloco **Gerenciamento de Trabalhos** para ver os trabalhos. Os trabalhos são categorizados como **Em execução**, **Em fila** e **Concluído**. Você deverá ver o trabalho com falha na seção **Concluído** . Ele deve ser primeiro na lista. Quando tiver vários trabalhos, é possível clicar em **Filtrar** para ajudar a encontrar os trabalhos.

    ![Trabalhos de filtragem da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Clique no trabalho com falha na lista para abrir os detalhes do trabalho:

    ![Trabalho com falha da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observe o botão **Enviar novamente** . Depois de corrigir o problema, você poderá enviar novamente o trabalho.
5. Clique na parte realçada na captura de tela anterior para abrir os detalhes do erro.  Você verá algo semelhante ao seguinte:

    ![Detalhes do trabalho com falha da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Isso indica que a pasta de origem não foi encontrada.
6. Clique em **Duplicar Script**.
7. Atualize o caminho **FROM** para:

    "/Samples/Data/SearchLog.tsv"
8. Clique em **Enviar Trabalho**.

## <a name="see-also"></a>Consulte também
* [Visão geral da Análise Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md)
