---
title: O painel do Power BI no Stream Analytics | Microsoft Docs
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
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics e Power BI: Um painel de análise em tempo real para dados de streaming
A Stream Analytics do Azure permite aproveitar uma das principais ferramentas de business intelligence, o Microsoft Power BI. Saiba como usar a Stream Analytics do Azure para analisar grandes volumes, dados de streaming e obter insight em um painel de análise do Power BI em tempo real.

Use o [Microsoft Power BI](https://powerbi.com/) para criar um painel dinâmico rapidamente. [Assista a um vídeo que ilustra o cenário](https://www.youtube.com/watch?v=SGUpT-a99MA).

Neste artigo, saiba como criar suas próprias ferramentas de inteligência de negócios personalizadas usando o Power BI como uma saída de seus trabalhos da Stream Analytics do Azure e utilize um painel em tempo real.

## <a name="prerequisites"></a>Pré-requisitos
* Conta do Microsoft Azure
* Conta corporativa ou de estudante para Power BI
* Conclua o cenário de Introdução [Detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md). Este artigo se baseia nesse fluxo de trabalho e acrescenta uma saída de conjunto de dados de streaming do Power BI.

## <a name="add-power-bi-output"></a>Adicionar saída do Power BI
Agora que existe uma entrada para o trabalho, é possível definir uma saída para o Power BI. Marque a caixa no meio do painel de trabalho **Saídas**. Em seguida, clique no conhecido botão **+ Adicionar** e crie a saída.

![adicionar saída](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

Forneça o **Alias de Saída** – Coloque qualquer alias de saída que seja fácil de lembrar. Esse alias de saída será particularmente útil se você optar por ter várias saídas para seu trabalho. Nesse caso, você precisa se referir a essa saída em sua consulta. Por exemplo, vamos usar o valor do alias de saída = "StreamAnalyticsRealTimeFraudPBI".

Clique no botão **Autorizar**.

![adicionar autorização](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

Isso mostrará uma janela na qual você deve fornecer suas credenciais de Azure (conta corporativa ou de estudante), e assim seu trabalho do Azure receberá o acesso à sua área do Power BI.

![autorizar campos](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

A janela de autorização desaparecerá após sua ação, e a Nova área de saída terá os campos de Nome do Conjunto de Dados e Nome da Tabela.

![espaço de trabalho de pbi](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

Defina-os da seguinte maneira:
* **Espaço de Trabalho de Grupo** : selecione um espaço de trabalho no seu locatário do Power BI sob a qual o conjunto de dados será criado.
* **Nome do Conjunto de Dados** -Forneça um nome de conjunto de dados que você deseja que a saída do Power BI tenha. Por exemplo, vamos usar = "StreamAnalyticsRealTimeFraudPBI".
* **Nome da Tabela** - Forneça um nome de tabela sob o conjunto de dados da sua saída do Power BI. Vamos supor o nome "StreamAnalyticsRealTimeFraudPBI". Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados.

Clique em **Criar**. Sua configuração de saída está concluída.

> [!WARNING]
> Tenha em mente também que se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome fornecido no trabalho do Stream Analytics, os dados existentes serão substituídos!
> Além disso, você não deve criar explicitamente esse conjunto de dados e a tabela em sua conta do Power BI. Ele serão automaticamente criados quando você iniciar o trabalho do Stream Analytics e ele começar a enviar saídas para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados.
> 
> 

O conjunto de dados é criado com o seguinte conjunto de configurações;
* defaultRetentionPolicy: BasicFIFO - os dados são FIFO, no máximo 200 mil linhas
* defaultMode: pushStreaming: oferece suporte aos blocos de streaming e visuais com base em relatórios tradicionais (também conhecido como push)
* A criação de conjuntos de dados com outros sinalizadores não tem suporte no momento

Para saber mais sobre conjuntos de dados do Power BI, consulte a referência à [API REST do Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Gravar consulta
Vá para a guia **Consulta** do seu trabalho. Escreva sua consulta, a saída da qual você deseja em seu Power BI. Por exemplo, poderia ser algo como a seguinte consulta SQL para detectar fraudes de SIM na indústria de telecomunicações:


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Criar o painel no Power BI

Acesse [Powerbi.com](https://powerbi.com) e faça logon com a sua conta corporativa ou de estudante. Se a consulta do trabalho do Stream Analytics tiver como saída resultados, você verá seu conjunto de dados já criado:

![conjunto de dados de streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

Clique no bloco Adicionar e selecione os dados de streaming personalizados.

![conjunto de dados de streaming personalizados](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

Em seguida, selecione seu conjunto de dados na lista:

![seu conjunto de dados de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

Agora crie um cartão de visualização e selecione o campo fraudulentcalls.

![adicionar fraude](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

Agora temos um contador de fraudes!

![contador de fraude](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

Percorra o exercício de adição de bloco novamente, mas desta vez selecione o gráfico de linha. Adicione fraudulentcalls como valor, e o eixo como windowend. Selecionei os últimos 10 minutos:

![chamadas de fraude](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Observe que este tutorial demonstrou como criar um tipo de gráfico para um conjunto de dados. O Power BI pode ajudá-lo a criar outras ferramentas de cliente business intelligence para sua organização. Para obter outro exemplo de um painel do Power BI, assista ao vídeo [Introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para obter mais informações sobre como configurar uma saída do Power BI e utilizar grupos do Power BI, analise a [seção Power BI](stream-analytics-define-outputs.md#power-bi) de [Noções básicas sobre saídas do Stream Analytics](stream-analytics-define-outputs.md "Entendendo as saídas do Stream Analytics"). Outro recurso útil para saber mais sobre como criar Painéis com o Power BI é [Painéis no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitações e práticas recomendadas
O Power BI emprega restrições de simultaneidade e taxa de transferência conforme descrito aqui: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Preço do Power BI")

Atualmente, seu Power BI pode ser chamado aproximadamente uma vez por segundo. A transmissão de elementos visuais oferece suporte a pacotes de 15 kb. Além disso, a transmissão de elementos visuais falhará (mas o push continuará funcionando).

Por causa dessas restrições, o Power BI se ajusta bem mais naturalmente nos casos em que a Stream Analytics do Azure faz uma significativa redução de carga de dados.
É recomendável usar a TumblingWindow ou a HoppingWindow para garantir que o push de dados seja no máximo 1 push/segundo e que sua consulta esteja dentro dos requisitos de taxa de transferência – pode ser usada a seguinte equação para calcular o valor da sua janela em segundos:

![equação1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Exemplo – se você tiver 1.000 dispositivos que enviam dados a cada segundo, você está no Power BI Pro SKU que dá suporte a 1.000.000 linhas por hora e deseja obter a média de dados por dispositivo no Power BI, você pode fazer no máximo um push a cada quatro segundos por dispositivo (como mostrado abaixo):  

![eequação2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Isso significa que mudaríamos a consulta original para:

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
Você precisa autenticar novamente sua conta do Power BI caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez. Se a MFA (Multi-Factor Authentication) estiver configurada no locatário do AAD (Azure Active Directory) também será necessário renovar a autorização do Power BI a cada 2 semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "erro de Autenticar usuário" nos Logs de Operação.

Da mesma forma, se um trabalho tentar iniciar enquanto o token estiver vencido, ocorrerá um erro e o início do trabalho falhará. Para resolver esse problema, pare seu trabalho em execução e vá para a saída do Power BI.  Clique no link "Renovar autorização" e reinicie o trabalho a partir da Hora da Última Interrupção para evitar a perda de dados. Após a atualização da autorização com o Power BI, você verá um alerta verde na área de autorização para mostrar se o problema foi resolvido.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


