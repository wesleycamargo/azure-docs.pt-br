<properties 
	pageTitle="Manual da solução de análise de telemetria do veículo | Microsoft Azure" 
	description="Usar os recursos da Análise do Cortana para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Manual da solução de análise de telemetria do veículo

Este **menu** fornece links para os capítulos deste manual.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## Visão geral
Os supercomputadores foram retirados do laboratório e agora estão estacionados em nossa garagem! Esses automóveis de ponta contêm uma infinidade de sensores, dando-lhes a capacidade de acompanhar e monitorar milhões de eventos por segundo. Esperamos que até 2020, a maioria desses carros seja conectada à Internet. Imagine-se explorando essa riqueza de dados para fornecer o melhor da segurança, confiabilidade e experiência de condução. A Microsoft tornou essa imaginação uma realidade por meio da Análise do Cortana.

A Análise do Cortana da Microsoft é um pacote de análises avançadas e de Big Data totalmente gerenciado que permite transformar seus dados em ação inteligente. Gostaríamos de apresentar o Modelo de Solução de Análise de Telemetria do Veículo da Análise do Cortana. Esta solução demonstra como as concessionárias, fabricantes de automóveis e seguradoras podem usar os recursos da Análise do Cortana para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução.

A solução é implementada como um [padrão de arquitetura lambda](https://en.wikipedia.org/wiki/Lambda_architecture) mostrando o potencial completo da plataforma de Análise do Cortana para processamento em tempo real e de lote. Ela inclui um simulador de Telemática do Veículo, aproveita os Hubs de Eventos para ingerir milhões de eventos simulados de telemetria do veículo no Azure e, em seguida, usa o Stream Analytics para obter informações em tempo real sobre a integridade do veículo e persiste esses dados no armazenamento de longo prazo para uma análise de lote mais aprofundada. Aproveita o Aprendizado de Máquina para a detecção de anomalias em tempo real e o processamento em lote para obter informações preditivas. O HDInsight é utilizado para transformar dados em escala; o Data Factory, por sua vez, lida com a orquestração, o agendamento, o gerenciamento de recursos e o monitoramento do pipeline de processamento em lote. Por fim, o Power BI fornece a essa solução um painel avançado para os dados em tempo real e as visualizações de análise preditiva.

## Arquitetura

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *Figura 1 – Arquitetura da solução de análise de telemetria do veículo*

Essa solução inclui os seguintes **componentes da Análise do Cortana** e apresenta sua integração de ponta a ponta:


- **Hubs de Eventos** para a ingestão de milhões de eventos de telemetria do veículo no Azure.
- **Stream Analytics** para obter informações em tempo real sobre a integridade do veículo e persistir esses dados no armazenamento de longo prazo para uma análise de lote mais avançada.
- **Aprendizado de Máquina** para a detecção de anomalias em tempo real e o processamento em lote para obter informações preditivas.
- O **HDInsight** é utilizado para transformar dados em escala
- O **Data Factory** lida com a orquestração, o agendamento, o gerenciamento de recursos e o monitoramento do pipeline de processamento em lote.
- O **PowerBI** fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva.

Essa solução acessa duas **fontes de dados** diferentes:

- **Sinais de veículo e diagnóstico simulados**: um simulador de telemática do veículo emite informações de diagnóstico e sinais que correspondem ao estado do veículo e ao padrão de condução em um determinado momento. 
- **Catálogo do veículo**: um conjunto de dados de referência que contém um VIN para o mapeamento do modelo.

<!---HONumber=AcomDC_1203_2015-->