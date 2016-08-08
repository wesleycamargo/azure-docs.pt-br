<properties 
	pageTitle="Modelo de preços dos Aplicativos Lógicos | Microsoft Azure" 
	description="Detalhes sobre como funciona o preço em Aplicativos Lógicos" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/25/2016"
	ms.author="klam"/>

# Modelo de preços de Aplicativos Lógicos

Os Aplicativos Lógicos permitem que você dimensione e execute fluxos de trabalho de integração na nuvem. Veja abaixo detalhes sobre a cobrança e planos de preços para os Aplicativos Lógicos do Azure.

## Preço por consumo

Aplicativos Lógicos recém-criado usam um plano de consumo. Com o modelo de preços de consumo dos Aplicativos Lógicos, você só paga pelo que usa. Os Aplicativos Lógicos não sofrem limitações ao usar um plano de consumo. Todas as execuções de ação realizadas durante a execução de uma instância de um aplicativo lógico são monitoradas.

### O que são execuções de ação?

Cada etapa em uma definição de aplicativo lógico é uma ação. Isso inclui gatilhos, etapas de fluxo de controle, como condições, escopos, loops for each e loops do until, chamadas a conectores e chamadas para ações nativas. Os gatilhos são apenas ações especiais projetadas para criar uma nova instância do aplicativo lógico quando um evento específico é recebido. Há diversos comportamentos diferentes para gatilhos que podem afetar o modo como o aplicativo lógico é monitorado.

-	**Gatilho de sondagem** – esse gatilho sonda continuamente um ponto de extremidade até receber uma mensagem que satisfaça os critérios para a criação de uma nova instância de um aplicativo lógico. O intervalo de sondagem pode ser configurado no gatilho no designer dos Aplicativos Lógicos. Cada solicitação de sondagem, mesmo se não criar uma nova instância de um aplicativo lógico, contará como uma execução da ação.

-	**Gatilho de webhook** – esse disparador espera o envio de uma solicitação por parte do cliente em um ponto de extremidade específico. Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução da ação. A Solicitação e o gatilho Webhook HTTP são gatilhos de webhook.

-	**Gatilho de recorrência** – esse gatilho criará uma nova instância do aplicativo lógico com base no intervalo de recorrência configurado gatilho. Por exemplo, um gatilho de recorrência pode ser configurado para executar a cada três dias ou até mesmo a cada minuto.

As execuções do gatilho podem ser vistas na folha de recursos dos Aplicativos Lógicos na parte do Histórico de Gatilhos.

Todas as ações executadas, bem-sucedidas ou não, são medidas como uma execução de ação. As ações que foram ignoradas devido a uma condição não atendida, ou ações que não foram executadas porque o aplicativo lógico foi encerrado antes da conclusão, não são contadas como execuções de ação.

Ações executadas em loops são contadas por iteração do loop. Por exemplo, uma única ação em um loop for each iterando por uma lista de 10 itens será contada como a contagem de itens na lista (10) multiplicada pelo número de ações no loop (1), mais um para o início do loop que, neste exemplo, seria (10 * 1) + 1 = 11 execuções de ação.

Os Aplicativos Lógicos desabilitados não podem ter novas instâncias criadas e, portanto, durante o tempo em que estiverem desabilitados não serão cobrados. Lembre-se de que depois de desabilitar um aplicativo lógico pode demorar algum tempo para as instâncias fecharem para novas sessões antes de serem completamente desabilitadas.

## Planos do Serviço de Aplicativo

Os Planos do Serviço de Aplicativo não são mais necessários para criar um Aplicativo Lógico. Os Aplicativo Lógicos criados anteriormente com um Plano do Serviço de Aplicativo continuarão se comportando como antes e, dependendo do plano escolhido, sofrerão uma limitação após um número de execuções diárias e não serão cobrados usando o medidor de execução da ação.

Planos do Serviço de Aplicativo e suas execuções diária de ação permitida:

| |Gratuito/Compartilhado/Básico|Standard|Premium|
|---|---|---|---|
|Execuções de ação por dia| 200|10\.000|50\.000|

Para alterar um Aplicativo Lógico que tem um Plano do Serviço de Aplicativo associado para um modelo de consumo, remova a referência ao Plano do Serviço de Aplicativo na definição do Aplicativo Lógico. Isso pode ser feito com uma chamada simples para um cmdlet do PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## Preços

Para obter detalhes sobre preços, confira [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

## Próximas etapas

- [Uma visão geral dos Aplicativos Lógicos][whatis]
- [Criar seu primeiro aplicativo lógico][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0727_2016-->