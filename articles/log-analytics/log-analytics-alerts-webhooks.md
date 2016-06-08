<properties 
   pageTitle="Exemplo de webhook de alerta no Log Analytics"
   description="Uma das ações que você pode executar em resposta a um alerta do Log Analytics é um *webhook*, que permite invocar um processo externo por meio de uma única solicitação HTTP. Neste artigo, vamos examinar um exemplo de como criar uma ação de webhook em um alerta do Log Analytics usando o Slack."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/12/2016"
   ms.author="bwren" />

# Webhooks nos alertas do Log Analytics

Uma das ações que você pode executar em resposta a um [alerta do Log Analytics](log-analytics-alerts.md) é um *webhook*, que permite invocar um processo externo por meio de uma única solicitação HTTP. Você pode ler sobre os detalhes de alertas e webhooks em [Alertas no Log Analytics](log-analytics-alerts.md)

Neste artigo, vamos examinar um exemplo de como criar uma ação de webhook em um alerta do Log Analytics usando o Slack, que é um serviço de mensagens.

>[AZURE.NOTE] Você deve ter uma conta do Slack para concluir este exemplo. Inscreva-se para uma conta gratuita em [slack.com](http://slack.com).

## Etapa 1: Habilitar webhooks no Slack
2.	Entre no Slack em [slack.com](http://slack.com).
3.	Selecione um canal na seção **Canais** no painel esquerdo. Esse é o canal para o qual a mensagem será enviada. Você pode selecionar um dos canais padrão como **geral** ou **aleatório**. Em um cenário de produção, você provavelmente criaria um canal especial como **alertasdeserviçocríticos**. <br>

	![Canais do Slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)

3. Clique em **Adicionar um aplicativo ou uma integração personalizada** para abrir o Diretório de Aplicativos.
3.	Digite *webhooks* na caixa de pesquisa e selecione **WebHooks de Entrada**. <br>
	
	![Canais do Slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)

4.	Clique em **Instalar** ao lado do nome da sua equipe.
5.	Clique em **Adicionar Configuração**.
6.	Selecione o canal que você vai usar para este exemplo e clique em **Adicionar integração de WebHooks de Entrada**.  
6. Copie a **URL do Webhook**. Você colará isso na configuração do Alerta. <br>

	![Canais do Slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## Etapa 2: Criar a regra de alerta no Log Analytics
1.	[Crie uma regra de alerta](log-analytics-alerts.md) com as seguintes configurações.
    - Consulta: ```	Type=Event EventLevelName=error ```
    - Verificar esse alerta a cada: 5 minutos
    - O número de resultados é: maior que 10
    - Durante o período de: 60 minutos
    - Selecione **Sim** para **Webhook** e **Não** para as outras ações.
7. Cole a URL do Slack no campo **URL do Webhook**. 
8. Selecione a opção para **incluir uma carga JSON personalizada**.
9. O Slack espera uma carga formatada em JSON com um parâmetro chamado *text*. Este é o texto que será exibido na mensagem que ele cria. Você pode usar um ou mais dos parâmetros de alerta usando o símbolo *#*, como no exemplo a seguir.

    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }	
    ```

	![carga JSON de exemplo](media/log-analytics-alerts-webhooks/oms-webhooks07.png)

9.	Clique em **Salvar** para salvar a regra de alerta.
    
10. Aguarde o tempo suficiente para que um alerta seja criado e verifique se há uma mensagem no Slack semelhante à seguinte.
    
	![webhook de exemplo no Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)


### Carga de webhook avançada para o Slack

É possível personalizar amplamente as mensagens de entrada com o Slack. Para obter mais informações, consulte [Webhooks de Entrada](https://api.slack.com/incoming-webhooks) no site do Slack. A seguir está uma carga mais complexa para criar uma mensagem avançada com formatação:

	{
		"attachments": [
			{
				"title":"OMS Alerts Custom Payload",
				"fields": [
					{
						"title": "Alert Rule Name",
						"value": "#alertrulename"},
					{
						"title": "Link To SearchResults",
						"value": "<#linktosearchresults|OMS Search Results>"},
					{
						"title": "Search Interval",
						"value": "#searchinterval"},
					{
						"title": "Threshold Operator",
						"value": "#thresholdoperator"},	
					{
						"title": "Threshold Value",
						"value": "#thresholdvalue"}
				],
				"color": "#F35A00"
			}
		]
	}


O script gerará uma mensagem no Slack semelhante a esta:

![mensagem de exemplo no Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## Resumo

Com essa regra de alerta em vigor, uma mensagem será enviada para o Slack sempre que os critérios forem atendidos.

Esse é apenas um exemplo de uma ação que você pode criar em resposta a um alerta. Você pode criar uma ação de webhook que chama outro serviço externo, uma ação de runbook para iniciar um runbook na Automação do Azure ou uma ação de email para enviar um email para si mesmo ou para outros destinatários.

## Próximas etapas

- Saiba mais sobre [alertas no Log Analytics](log-analytics-alerts.md), incluindo outras ações.
- [Crie runbooks na Automação do Azure](../automation/automation-webhooks.md) que podem ser chamados de um webhook.

<!---HONumber=AcomDC_0518_2016-->