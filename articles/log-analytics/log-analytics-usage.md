---
title: Analisar o uso de dados no Log Analytics | Microsoft Docs
description: "Você pode usar o painel Uso no Log Analytics para exibir quantos dados estão sendo enviados para o serviço do OMS."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 7e3d4b83fefdc70f292cf85b682cf8ed756bf4c5
ms.openlocfilehash: e7f04df679604f274c8ad9bf4daddc63c8b5418a
ms.contentlocale: pt-br
ms.lasthandoff: 02/22/2017


---
# <a name="analyze-data-usage-in-log-analytics"></a>Analisar o uso de dados no Log Analytics
O Log Analytics coleta dados e os envia ao serviço OMS periodicamente.  Você pode usar o painel **Uso do Log Analytics** para exibir quantos dados estão sendo enviados ao serviço OMS. O painel também mostra quantos dados estão sendo enviados pelas soluções e com que frequência seus servidores estão enviando dados.

> [!NOTE]
> Se tiver uma conta gratuita, você estará limitado ao envio de 500 MB de dados para o serviço OMS diariamente. Se você atingir o limite diário, a análise de dados será parada e reiniciada no início do dia seguinte. Nesse caso, você precisará reenviar os dados não foi aceito ou processados pelo OMS.

Se tiver excedido ou estiver próximo do limite de uso diário, opcionalmente, você poderá remover uma solução para reduzir a quantidade de dados a serem enviados para o serviço OMS. Para obter mais informações sobre como remover soluções, consulte [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md).

![painel de uso](./media/log-analytics-usage/usage-dashboard01.png)

O painel **Uso de Log Analytics** exibe as seguintes informações:

- Volume de dados
    - Volume de dados ao longo do tempo (com base em seu escopo de tempo atual)
    - Volume de dados por solução
    - Dados não associados a um computador
- Computadores
    - Computadores enviando dados
    - Computadores sem dados nas últimas 24 horas
- Ofertas
    - Nós Insight e de Análise
    - Nós de automação e controle
    - Nós de segurança
- Desempenho
    - Tempo necessário para coletar e indexar dados
- Lista de consultas

## <a name="understanding-nodes-for-oms-offers"></a>Noções básicas sobre nós de ofertas OMS

Se você estiver usando o tipo de preço *por nó (OMS)*, será cobrado com base no número de nós e soluções que habilitou. Você pode ver quantos nós de cada oferta estão em uso na seção *ofertas* do painel de uso.

![painel de uso](./media/log-analytics-usage/log-analytics-usage-offerings.png)

## <a name="to-work-with-usage-data"></a>Para trabalhar com dados de uso
1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com) usando a sua assinatura do Azure.
2. No menu **Hub**, clique em **Mais serviços** e, na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Clique em **Log Analytics**.  
    ![Hub do Azure](./media/log-analytics-usage/hub.png)
3. O painel **Log Analytics** mostra uma lista dos espaços de trabalho. Selecione um espaço de trabalho.
4. No painel *espaço de trabalho*, clique em **Uso do Log Analytics**.
5. No painel **Uso do Log Analytics**, clique em **Tempo: últimas 24 horas** para alterar o intervalo de tempo.  
    ![intervalo de tempo](./media/log-analytics-usage/time.png)
6. Exiba as folhas de categoria de uso que mostram as áreas de seu interesse. Escolha uma folha e clique em um item para exibir mais detalhes em [Pesquisa de Log](log-analytics-log-searches.md).  
    ![folha de uso de dados de exemplo](./media/log-analytics-usage/blade.png)
7. No painel de Pesquisa de Log, examine os resultados que são retornados da pesquisa.  
    ![pesquisa de log de uso de exemplo](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>Próximas etapas
* Confira [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir informações detalhadas que são coletadas e enviadas ao OMS por recursos e soluções.

