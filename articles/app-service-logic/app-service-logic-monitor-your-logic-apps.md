<properties 
	pageTitle="Monitorar seus Aplicativos lógicos no Serviço de Aplicativo do Azure | Microsoft Azure" 
	description="Como ver o que os Aplicativos lógicos fizeram" 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="stepsic"/>

# Monitorar seus Aplicativos lógicos

Depois de [criar um Aplicativo lógico](app-service-logic-create-a-logic-app.md), é possível ver o histórico completo de sua execução no portal do Azure. Para exibir o histórico, selecione **Procurar** e **Aplicativos lógicos**. É exibida uma lista com todos os Aplicativos Lógicos em sua assinatura. É possível selecionar qualquer Aplicativo lógico e **Habilitar** ou **Desabilitar**. Aplicativos lógicos **Habilitados** significam que os gatilhos executam seu Aplicativo lógico em resposta aos eventos de gatilho. Um Aplicativo lógico **Desabilitado** não é executado em resposta aos eventos.

![Visão geral](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Quando a folha de seu Aplicativo lógico aparecer, haverá duas seções úteis:

- **Resumo** mostra o status mais recente e é um ponto de entrada para editar seu Aplicativo lógico.
- **Todas as execuções** mostra uma lista das execuções desse Aplicativo lógico.

## Exibir as execuções do aplicativo

![Todas as execuções](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Essa lista de execuções mostra a **Hora de início**, o **Identificador de execução** (você pode usar isso ao chamar a API REST) e a **Duração** das execuções específicas. Clique em qualquer linha para ver os detalhes da execução.

A folha de detalhes mostra um gráfico com o tempo de execução e a sequência de todas as ações na execução. Veja a seguir a lista completa de todas as ações que foram executadas:

![Execução e ações](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Finalmente, em uma ação específica, você pode obter todos os dados que foram enviados à ação e que foram recebidos da ação nas seções **Entradas** e **Saídas**. Selecione os links para ver todo o conteúdo (você também pode copiar os links para baixar o conteúdo).

Outra informação importante é a **ID de Acompanhamento**. Esse identificador é transmitido nos cabeçalhos de todas as chamadas de ação. Se você tiver o registro em log dentro do seu próprio serviço, fazer registrar a ID de Acompanhamento e então fazer referência cruzada de seus próprios logs com esse identificador.

## Exibir o histórico de gatilho 

Gatilhos de sondagem verificam uma API em um intervalo de sondagem, mas não necessariamente iniciam uma execução, dependendo da resposta (por exemplo, um `200` significa executar e um `202` significa não executar). O histórico de gatilhos permite ver todas as chamadas que aconteceram, mas que não executam o Aplicativo lógico (as respostas `202`).

![Histórico de gatilho](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Para cada gatilho, você pode ver se ele foi **disparado** ou não, ou se houve algum tipo de erro (**Falha**). Para inspecionar o motivo da falha do gatilho, você pode clicar no link **Saídas**. Se ele disparou, selecione o link **Executar** para ver o que aconteceu após o disparo.

Observe que, para gatilhos de *Push*, você *não* vê as horas de início das execuções. Em vez disso, você vê as chamadas de *registro de retorno de chamada*, que são o registro do Aplicativo Lógico para receber o retorno de chamada. Se o gatilho de push não estiver funcionando, pode ser que haja um problema com o registro (que você poderá ver nas saídas), mas, caso contrário, talvez seja necessário investigar especificamente essa API.

## Habilitando o Diagnóstico do Azure

Você pode habilitar as informações de Diagnóstico para recuperar e armazenar os dados da execução para os fluxos de trabalho. Na folha Aplicativo Lógico, role para baixo até três gráficos de **Monitoramento** e escolha **Clicar aqui para habilitar o diagnóstico**. Você pode configurar em uma conta de armazenamento na região do Aplicativo Lógico e assinar **Logs** ou **Métrica** no Aplicativo. **Logs** enviará um evento sempre que uma execução, ação ou evento iniciar ou terminar. **Métrica** fornece dados agregados sobre quantas execuções foram realizadas dentro de uma janela de tempo.

## Habilitar o controle de versão

Há uma funcionalidade adicional que, atualmente, não está disponível na interface do usuário (em breve), mas que está na [API REST](https://msdn.microsoft.com/library/azure/mt643788.aspx). Quando você atualiza a definição de um aplicativo lógico, a versão anterior da definição é armazenada. Isso ocorre porque, se você já tiver uma execução em andamento, ela fará referência à versão do Aplicativo Lógico que existia quando a execução teve início. Definições de execuções não podem mudar enquanto estiverem em andamento. O histórico de versão de API REST permite o acesso a essas informações.

<!---HONumber=AcomDC_0601_2016-->