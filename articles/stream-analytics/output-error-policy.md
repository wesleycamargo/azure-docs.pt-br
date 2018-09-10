---
title: Políticas de erro de saída no Azure Stream Analytics
description: Saiba mais sobre as políticas de tratamento de erro de saída disponíveis no Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392563"
---
# <a name="output-error-policy"></a>Política de erro de saída
Este artigo descreve as políticas de tratamento de erro de dados de saída que podem ser configuradas no Azure Stream Analytics.

As políticas de tratamento de erro de dados de saída aplicam-se apenas a erros de conversão de dados que ocorrem quando o evento de saída produzido por um trabalho do Stream Analytics não está em conformidade com o esquema do coletor de destino. É possível configurar essa política, escolhendo **Repetir** ou **Remover**. No portal do Azure, enquanto estiver em um trabalho do Stream Analytics, em **Configurar**, selecione **Política de Erro** para fazer a seleção.

![Política de erro - local](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Retry
Quando um erro ocorrer, o Azure Stream Analytics repetirá a gravação do evento indefinidamente até que a gravação seja executada com êxito. Não há tempo limite para novas repetições. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está tentando novamente. Essa opção é a política de tratamento de erro de saída padrão.

## <a name="drop"></a>Remover
O Azure Stream Analytics removerá qualquer evento de saída que resulte em um erro de conversão de dados. Os eventos removidos não poderão ser recuperados para reprocessamento posterior.


Todos os erros transitórios (por exemplo, erros de rede) serão repetidos, independentemente da configuração da política de tratamento de erro de saída.


## <a name="next-steps"></a>Próximas etapas
[Guia de solução de problemas do Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
