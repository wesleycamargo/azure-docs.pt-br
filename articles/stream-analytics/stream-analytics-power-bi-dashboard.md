---
title: Painel do Power BI no Stream Analytics do Azure | Microsoft Docs
description: "Use um painel de Power BI de transmissão em tempo real para reunir inteligência comercial e analisar grandes volumes de dados de um trabalho de Stream Analytics."
keywords: "painel de análise, painel em tempo real"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3dbc49d35def6d7b12ade529d1dd1156dee9d75b
ms.openlocfilehash: 09c54f8cce119c1cbe6a08e969236612447d9e17
ms.lasthandoff: 02/27/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics e Power BI: um painel de análise em tempo real para dados de streaming
O Stream Analytics do Azure permite aproveitar uma das principais ferramentas de business intelligence, o Microsoft Power BI. Saiba como usar a Stream Analytics do Azure para analisar grandes volumes, dados de streaming e obter informações de um painel de análise do Power BI em tempo real.

Use o [Microsoft Power BI](https://powerbi.com/) para criar um painel dinâmico rapidamente. [Assista a um vídeo que ilustra esse cenário](https://www.youtube.com/watch?v=SGUpT-a99MA).

Neste artigo, você saberá como criar suas próprias ferramentas de business intelligence personalizadas usando o Power BI como uma saída de seus trabalhos da Stream Analytics do Azure. Você também aprenderá a usar um painel em tempo real.

## <a name="prerequisites"></a>Pré-requisitos
* Conta do Microsoft Azure.
* Conta corporativa ou de estudante para Power BI.
* Conclusão do cenário [Detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md). O artigo que você está lendo agora se baseia no fluxo de trabalho que é descrito em [detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) e adiciona um fluxo de saída do conjunto de dados do Power BI.

## <a name="add-power-bi-output"></a>Adicionar saída do Power BI
Agora que existe uma entrada para o trabalho, é possível definir uma saída para o Power BI.

1. Marque a caixa **Saídas** no meio do painel do trabalho. Em seguida, selecione **+ Adicionar** para criar a saída.

    ![Adicionar saída](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. Forneça o **Alias de Saída**. Você pode usar qualquer alias de saída que seja fácil de lembrar. Esse alias de saída será útil se você optar por ter várias saídas para seu trabalho. Nesse caso, você faz referência a essa saída em sua consulta. Por exemplo, vamos usar o valor do alias de saída "StreamAnalyticsRealTimeFraudPBI".

3. Selecione **Autorizar**.

    ![Adicionar autorização](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. É aberta uma janela onde você pode fornecer suas credenciais do Azure (para uma conta corporativa ou escolar). Ele também fornece o trabalho do Azure com acesso à sua área do Power BI.

    ![Autorizar campos](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. A autorização desaparecerá depois que você tiver fornecido as informações necessárias. O **nova saída** área tem campos para o **nome do conjunto de dados** e **nome de tabela**.

    ![Espaço de trabalho de PBI](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. Defina-os da seguinte maneira:
    * **Espaço de Trabalho de Grupo**: selecione um espaço de trabalho no seu locatário do Power BI sob a qual o conjunto de dados será criado.
    * **Nome do Conjunto de Dados**: forneça um nome de conjunto de dados que você deseja que a saída do Power BI tenha. Por exemplo, vamos usar "StreamAnalyticsRealTimeFraudPBI".
    * **Nome da Tabela**: forneça um nome de tabela sob o conjunto de dados da sua saída do Power BI. Por exemplo, vamos usar "StreamAnalyticsRealTimeFraudPBI". Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados.

7. Selecione **Criar**. Agora sua configuração de saída está concluída.

> [!WARNING]
> Se o Power BI já tem um conjunto de dados e uma tabela que tem o mesmo nome que aquele neste trabalho do Stream Analytics, os dados existentes são substituídos.
> Além disso, recomendamos que você não crie explicitamente esse conjunto de dados e a tabela em sua conta do Power BI. Ele serão automaticamente criados quando você iniciar o trabalho do Stream Analytics e ele começar a enviar saídas para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados.
>
>

O conjunto de dados é criado com as seguintes configurações:
* **defaultRetentionPolicy: BasicFIFO**: os dados são FIFO, no máximo com 200 mil linhas.
* **defaultMode: pushStreaming**: oferece suporte aos blocos de streaming e visuais com base em relatórios tradicionais (também conhecido como push).

Atualmente, não é possível criar conjuntos de dados com outros sinalizadores.

Para saber mais sobre conjuntos de dados do Power BI, consulte a referência à [API REST do Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Gravar consulta
Vá para a guia **Consulta** do seu trabalho. Escreva sua consulta, a saída da qual você deseja no Power BI. Por exemplo, poderia ser algo semelhante à seguinte consulta SQL para detectar fraudes de SIM na indústria de telecomunicações:


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Criar o painel no Power BI

1. Acesse [Powerbi.com](https://powerbi.com) e entre com a sua conta corporativa ou de estudante. Se a consulta do Stream Analytics de trabalho gera resultados, verá que seu conjunto de dados já foi criado, conforme mostrado na ilustração a seguir:

    ![Conjunto de dados de streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Selecione **Adicionar bloco** e então selecione os dados de streaming personalizados.

    ![Conjunto de dados de streaming personalizado](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. Selecione seu conjunto de dados na lista.

    ![Seu conjunto de dados de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. Crie um cartão de visualização. Em seguida, selecione o campo **fraudulentcalls**.

    ![Adicionar fraude](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    Agora temos um contador de fraudes!

    ![Contador de fraudes](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. Percorra o exercício de adição de um bloco novamente. Desta vez, no entanto, selecione o gráfico de linhas. Adicione **fraudulentcalls** como valor, e o eixo como **windowend**. Selecionamos os últimos 10 minutos, conforme mostrado na seguinte captura de tela:

![Chamadas de fraude](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Este tutorial demonstrou como criar somente um tipo de gráfico para um conjunto de dados. O Power BI pode ajudá-lo a criar outras ferramentas de cliente business intelligence para sua organização. Para obter outro exemplo de um painel do Power BI, assista ao vídeo [Introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para obter mais informações sobre como configurar uma saída do Power BI e utilizar grupos do Power BI, analise a [seção Power BI](stream-analytics-define-outputs.md#power-bi) de [Noções básicas sobre saídas do Stream Analytics](stream-analytics-define-outputs.md "Entendendo as saídas do Stream Analytics"). [Painéis no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) é outro recurso útil.

## <a name="learn-about-limitations-and-best-practices"></a>Saiba mais sobre limitações e práticas recomendadas
Power BI emprega restrições de simultaneidade e taxa de transferência conforme descrito [nesta página sobre o Power BI](https://powerbi.microsoft.com/pricing "Power BI preços").

Atualmente, o Power BI pode ser chamado aproximadamente uma vez por segundo. A transmissão de elementos visuais oferece suporte a pacotes de 15 KB. Além disso, o streaming de elementos visuais falha (mas o push continua a funcionar).

Por causa dessas limitações, o Power BI se ajusta bem mais naturalmente nos casos em que a Stream Analytics do Azure faz uma significativa redução de carga de dados.
É recomendável usar a janela em cascata ou janela de salto para garantir que o envio de dados é no máximo um envio por segundo e que sua consulta esteja dentro dos requisitos de taxa de transferência.

Você pode usar a seguinte equação para calcular o valor em segundos dar sua janela:

![Equação1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Por exemplo:
- Você tem 1.000 dispositivos que enviam dados em intervalos de um segundo.
- Você está usando o SKU do Power BI Pro que dá suporte a 1.000.000 linhas por hora.
- Você deseja publicar a quantidade de dados médios por dispositivo no Power BI.

Como resultado, a equação torna-se:

![Equação2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Isso significa que podemos alterar a consulta original para o seguinte:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Renovar autorização
Caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez, será necessário autenticar novamente sua conta do Power BI. Se a Autenticação Multifator estiver configurada no locatário do Azure Active Directory (Azure AD) também será necessário renovar a autorização do Power BI a cada duas semanas. Se você não renovar, você pode ver os sintomas, como falta de saída do trabalho ou um "Erro de usuário autenticar" nos logs de operação.

Da mesma forma, se um trabalho tenta iniciar depois que o token tiver expirado, ocorrerá um erro e o trabalho começa a falhar. Para resolver esse problema, pare o trabalho em execução e vá para a saída do Power BI. Para evitar a perda de dados, selecione **Renovar autorização** e reinicie o trabalho a partir da Hora da **Última Interrupção**.

Depois que a autorização foi atualizada com o Power BI, um alerta verde é exibida na área de autorização para refletir se o problema foi resolvido.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

