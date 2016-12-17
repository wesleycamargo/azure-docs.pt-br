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
ms.date: 09/26/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c954e85aa61803b4afe82c2e9d30729035ac094f


---
# <a name="stream-analytics-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics e Power BI: Um painel de análise em tempo real para dados de streaming
A Stream Analytics do Azure permite aproveitar uma das principais ferramentas de business intelligence, o Microsoft Power BI. Saiba como usar a Stream Analytics do Azure para analisar grandes volumes, dados de streaming e obter insight em um painel de análise do Power BI em tempo real.

Use o [Microsoft Power BI](https://powerbi.com/) para criar um painel dinâmico rapidamente. [Assista a um vídeo que ilustra o cenário](https://www.youtube.com/watch?v=SGUpT-a99MA).

Neste artigo, saiba como criar suas próprias ferramentas de inteligência de negócios personalizadas usando o Power BI como uma saída de seus trabalhos da Stream Analytics do Azure e utilize um painel em tempo real.

## <a name="prerequisites"></a>Pré-requisitos
* Conta do Microsoft Azure
* Uma entrada para o trabalho da Stream Analytics de onde serão consumidos dados de streaming. A Stream Analytics aceita a entrada do armazenamento do Hubs de eventos do Azure ou Armazenamento do blob do Azure.  
* Conta corporativa ou de estudante para Power BI

## <a name="create-azure-stream-analytics-job"></a>Criar trabalho do Azure Stream Analytics
No [Portal clássico do Azure](https://manage.windowsazure.com), clique em **Novo, Serviços de Dados, Stream Analytics, Criação Rápida**.

Especifique os valores a seguir, então clique em **Criar trabalho do Stream Analytics**:

* **Nome do Trabalho** - Insira um nome de trabalho. Por exemplo, **Temperaturas do Dispositivo**.
* **Região** - Selecione a região em que você deseja que o trabalho esteja localizado. Considere posicionar o trabalho e o hub de eventos na mesma região para garantir um desempenho ideal e evitar custos de transferência de dados entre regiões.
* **Conta de Armazenamento** - Escolha a Conta de Armazenamento que você deseja usar para armazenar dados de monitoramento de todos os trabalhos do Stream Analytics executados nesta região. Você tem a opção de escolher uma Conta de Armazenamento existente ou criar uma nova.

Clique em **Stream Analytics** no painel esquerdo para listar os trabalhos do Stream Analytics.

![elementográfico1][graphic1]

> [!TIP]
> O novo trabalho será listado com um status **Não Iniciado**. Observe que o botão **Iniciar** na parte inferior da página está desabilitado. Esse é o comportamento esperado, já que você deve configurar a entrada do trabalho, a saída, a consulta e assim por diante antes de poder iniciar o trabalho.
> 
> 

## <a name="specify-job-input"></a>Especificar entrada de trabalho
Para este tutorial, estamos supondo que você esteja usando o Hub de Eventos como uma entrada com serialização JSON e codificação UTF-8.

* Clique no nome do trabalho.
* Clique em **Entradas** na parte superior da página e em **Adicionar Entrada**. A caixa de diálogo que será aberta o orientará ao longo de uma série de etapas para configurar sua entrada.
* Selecione **Fluxo de Dados**e então clique no botão direito.
* Selecione **Hub de Eventos**e então clique no botão direito.
* Digite ou selecione os seguintes valores na terceira página:
  * **Alias de Entrada** - Insira um nome amigável para essa entrada de trabalho. Observe que você usará esse nome na consulta posteriormente.
  * **Hub de Eventos** - Se o Hub de Eventos que você criou estiver na mesma assinatura que o trabalho do Stream Analytics, selecione o namespace que contém o hub de eventos.
* Se o hub de eventos estiver em uma assinatura diferente, selecione **Usar Hub de Eventos de Outra Assinatura** e insira manualmente as informações para o **Namespace do Barramento de Serviço**, o **Nome do Hub de Eventos**, o **Nome da Política do Hub de Eventos**, a **Chave de Política do Hub de Eventos** e a **Contagem de Partições do Hub de Eventos**.

> [!NOTE]
> Este exemplo usa o número de partições padrão, que é 16.
> 
> 

* **Nome do Hub de Eventos** - Selecione o nome do Hub de Eventos do Azure que você tem.
* **Nome de Política do Hub de Eventos** -Selecione a política de Hub de Eventos para o Hub de Eventos que você está usando. Verifique se essa política tem permissões de gerenciamento.
* **Grupo de Consumidores do Hub de Eventos** – Você pode deixar esta opção vazia ou especificar um grupo de consumidores que você tem em seu hub de eventos. Observe que cada grupo de consumidores de um hub de eventos pode ter apenas cinco leitores por vez. Dessa forma, decida qual é o grupo de consumidores certo para seu trabalho. Se você deixar o campo em branco, ele usará o grupo de consumidores padrão.
* Clique no botão direito.
* Especifique os seguintes valores:
  * **Formato do Serializador de Eventos** - JSON
  * **Codificação** - UTF8
* Clique no botão de seleção para adicionar essa fonte e verificar se a Análise de fluxo pode se conectar com êxito ao Hub de eventos.

## <a name="add-power-bi-output"></a>Adicionar saída do Power BI
1. Clique em **Saída** na parte superior da página e em **Adicionar Saída**. Você verá o Power BI listado como uma opção de saída.
   
   ![elementográfico2][graphic2]  
2. Selecione **Power BI** e então clique no botão direito.
3. Você verá uma tela como esta:
   
   ![elementográfico3][graphic3]  
4. Nesta etapa, forneça uma conta corporativa ou de estudante para a saída de trabalho do Stream Analytics. Se você já tiver uma conta do Power BI, selecione **Autorizar Agora**. Se não, escolha **Inscreva-se agora**. [Aqui você encontrará um blog com detalhes de orientação para a inscrição no Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).
   
   ![elementográfico11][graphic11]  
5. Em seguida, você verá uma tela como esta:
   
   ![elementográfico4][graphic4]  

Forneça valores como a seguir:

* **Alias de Saída** – Você pode colocar qualquer alias de saída que seja fácil de lembrar. Esse alias de saída será particularmente útil se você optar por ter várias saídas para seu trabalho. Nesse caso, você precisa se referir a essa saída em sua consulta. Por exemplo, vamos usar o valor do alias de saída = "OutPbi".
* **Nome do Conjunto de Dados** -Forneça um nome de conjunto de dados que você deseja que a saída do Power BI tenha. Por exemplo, usaremos “pbidemo”.
* **Nome da Tabela** - Forneça um nome de tabela sob o conjunto de dados da sua saída do Power BI. Digamos que nós a chamaremos de “pbidemo”. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados.
* **Espaço de trabalho** : selecione um espaço de trabalho no seu locatário do Power BI sob a qual o conjunto de dados será criado.

> [!NOTE]
> Você não deve criar explicitamente esse conjunto de dados e a tabela em sua conta do Power BI. Ele serão automaticamente criados quando você iniciar o trabalho do Stream Analytics e ele começar a enviar saídas para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados.
> 
> 

* Clique em **OK**, **Testar Conexão**. Agora sua configuração de saída está concluída.

> [!WARNING]
> Tenha em mente também que se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome fornecido no trabalho do Stream Analytics, os dados existentes serão substituídos.
> 
> 

## <a name="write-query"></a>Gravar consulta
Vá para a guia **Consulta** do seu trabalho. Escreva sua consulta, a saída da qual você deseja em seu Power BI. Por exemplo, ela poderia ser algo como a seguinte consulta SQL:

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
        TUMBLINGWINDOW(ss,1),
        dspl



Inicie o trabalho. Valide se o seu hub de eventos está recebendo eventos e se sua consulta gera os resultados esperados. Se a sua consulta tiver como saída 0 linhas, o conjunto de dados e as tabelas do Power BI não serão criados automaticamente.

## <a name="create-the-dashboard-in-power-bi"></a>Criar o painel no Power BI
Acesse [Powerbi.com](https://powerbi.com) e faça logon com a sua conta corporativa ou de estudante. Se a consulta do trabalho do Stream Analytics tiver como saída resultados, você verá seu conjunto de dados já criado:

![elementográfico5][graphic5]

Para a criação do painel, vá para a opção de Painéis e crie um novo Painel.

![elementográfico6][graphic6]

Neste exemplo, vamos rotulá-lo como “Painel de Demonstração”.

Agora, clique no conjunto de dados criado por seu trabalho do Stream Analytics (pbidemo, em nosso exemplo atual). Você será levado a uma página para criar um gráfico na parte superior desse conjunto de dados. A seguir, um exemplo dos relatórios que você pode criar:

Selecione os campos Σ temporário e hora. Eles irão automaticamente para Valor e Eixo no gráfico:

![elementográfico7][graphic7]

Com isso, você obterá automaticamente um gráfico como a seguir:

![elementográfico8][graphic8]

Na seção de valor, clique na lista suspensa de temporários e escolha **média** para a temperatura e, no gráfico, clique em **visualização** e escolha **gráfico de linhas**:

![elementográfico9][graphic9]

Agora, você obterá um gráfico de linhas de média ao longo do tempo.  Usando a opção de fixação como mostrado abaixo, você poderá fixar isso em seu painel criado anteriormente:

![elementográfico10][graphic10]

Agora quando você exibir o painel com esse relatório fixado, você verá a atualização do relatório em tempo real. Tente alterar os dados em seus eventos – Temp especial ou algo parecido e você verá o efeito em tempo real disso refletido no painel.

Observe que este tutorial demonstrou como criar um tipo de gráfico para um conjunto de dados. O Power BI pode ajudá-lo a criar outras ferramentas de cliente business intelligence para sua organização. Para obter outro exemplo de um painel do Power BI, assista ao vídeo [Introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para obter mais informações sobre como configurar uma saída do Power BI e utilizar grupos do Power BI, analise a [seção Power BI](stream-analytics-define-outputs.md#power-bi) de [Noções básicas sobre saídas do Stream Analytics](stream-analytics-define-outputs.md "Entendendo as saídas do Stream Analytics"). Outro recurso útil para saber mais sobre como criar Painéis com o Power BI é [Painéis no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitações e práticas recomendadas
O Power BI emprega restrições de simultaneidade e taxa de transferência conforme descrito aqui: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Preço do Power BI")

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

### <a name="powerbi-view-refresh"></a>Atualização de exibição do PowerBI
Uma pergunta comum é "Por que o painel não se atualiza automaticamente no PowerBI?".

Para fazer isso, no PowerBI utiliza P e R, faz uma pergunta como "Valor máximo por arquivo temporário cujo carimbo de data/hora indica hoje" e fixa esse bloco ao painel.

### <a name="renew-authorization"></a>Renovar autorização
Você precisará autenticar novamente sua conta do Power BI caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez. Se a MFA (Multi-Factor Authentication) estiver configurada no locatário do AAD (Azure Active Directory) também será necessário renovar a autorização do Power BI a cada 2 semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "erro de Autenticar usuário" nos Logs de Operação:

![elementográfico12][graphic12]

Da mesma forma, se um trabalho tentar iniciar enquanto o token estiver vencido, ocorrerá um erro e o início do trabalho falhará. O erro será algo semelhante a isto:

![Erro de validação do Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 

Para resolver esse problema, pare seu trabalho em execução e vá para a saída do Power BI.  Clique no link "Renovar autorização" e reinicie o trabalho a partir da Hora da Última Interrupção para evitar a perda de dados.

![Renovação de validação do Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Após a atualização da autorização com o Power BI, você verá um alerta verde na área de autorização:

![Renovação de validação do Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[elementográfico1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[elementográfico2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[elementográfico3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[elementográfico4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[elementográfico5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[elementográfico6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[elementográfico7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[elementográfico8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[elementográfico9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[elementográfico10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[elementográfico11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[elementográfico12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[elementográfico13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png



<!--HONumber=Nov16_HO3-->


