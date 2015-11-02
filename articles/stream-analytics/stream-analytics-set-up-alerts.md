<properties 
	pageTitle="Alertas do Stream Analytics | Microsoft Azure" 
	description="Noções básicas sobre alertas do Stream Analytics" 
	keywords="análise de big data, serviço de nuvem, internet das coisas, serviço gerenciado, processamento de fluxo, streaming analytics, dados de streaming"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>


# Configurando alertas

## Página Monitor

Você pode configurar uma regra para disparar um alerta quando uma métrica atinge uma condição que você especifica.

Por exemplo, “Se Eventos de Saída dos últimos 15 minutos forem inferiores a 100, enviar notificação por email à id de email: xyz@company.com”.

As regras podem ser configuradas em métricas por meio do portal ou [de modo programático](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) pelos dados dos Logs de Operação”.

## Configurando alertas pelo Portal do Azure

Há duas maneiras de configurar alertas no portal de Gerenciamento do Azure:

1.	Na guia **Monitorar** do seu trabalho do Stream Analytics  
2.	No Log de Operações nos Serviços de gerenciamento  

## Alertas por meio da guia Monitor do trabalho no portal

1.	Selecione a métrica na guia Monitorar, clique no botão **Adicionar Regra** na parte inferior do painel e configure as regras.  

    ![Painel](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Defina o nome e a descrição do alerta

    ![Criar Regra](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Insira os limites, a janela de avaliação do alerta e as ações para o alerta

    ![Definir Condições](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Configurando alertas pelos Logs de operações

1.	Vá para a guia **Alertas** nos Serviços de Gerenciamento, no [Portal do Azure](https://manage.windowsazure.com).  
2.	Clique em **Adicionar Regra**  

    ![Critérios](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Defina o nome e a descrição do alerta. Selecione 'Stream Analytics' como Tipo de Serviço e o nome do trabalho como o Nome do Serviço.

    ![Definir Alerta](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Configurando alertas no portal de Visualização do Azure ##

No portal de Visualização do Azure, navegue até o trabalho do Stream Analytics do qual você deseja receber alertas e clique na seção **Monitoramento**. Na folha **Métrica** que é aberta, clique no comando **Adicionar alerta**.

  ![Instalação no portal de visualização do Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)

Você pode nomear a regra de alerta e escolher uma descrição que aparecerá no email de notificação.

Ao selecionar Métricas, você escolherá uma condição e um valor de limite para a métrica.

  ![Selecionar métrica no portal de visualização do Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)

Para obter mais detalhes sobre como configurar alertas no portal de Visualização do Azure, veja [Receber notificações de alerta](./azure-portal/insights-receive-alert-notifications.md).

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO4-->