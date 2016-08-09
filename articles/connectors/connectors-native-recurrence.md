<properties
	pageTitle="Adicionar o gatilho de recorrência em Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral do gatilho de recorrência e como usá-lo com um aplicativo lógico do Azure."
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introdução ao gatilho de recorrência

Com o gatilho de recorrência, você pode criar fluxos de trabalho poderosos na nuvem, como:

- Agendar um fluxo de trabalho para executar um procedimento armazenado SQL todos os dias
- Enviar por email um resumo de todos os tweets da semana passada sobre uma determinada hashtag

Para começar a usar o gatilho de recorrência em um aplicativo lógico, confira [criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usar um gatilho de recorrência

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja uma sequência de exemplo de como configurar um gatilho de recorrência em um aplicativo lógico

1. Adicione o gatilho **Recorrência** como a primeira etapa em um aplicativo lógico
1. Preencha os parâmetros para o intervalo de recorrência
1. O aplicativo lógico abrirá uma execução depois de cada intervalo de tempo

![Gatilho de HTTP](./media/connectors-native-recurrence/using-trigger.png)

---

## Detalhes técnicos

Veja a seguir os detalhes para o gatilho de recorrência e os parâmetros que podem ser configurados.

### Detalhes do gatilho

O gatilho de recorrência tem as propriedades a seguir que podem ser configuradas.

#### Recorrência
Dispare um aplicativo lógico depois de um intervalo de tempo especificado. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Frequência*|frequência|A unidade de tempo - `Second`, `Minute`, `Hour`, `Day` ou `Year`|
|Intervalo*|intervalo|Intervalo na frequência determinada para a recorrência|
|Fuso horário|timeZone|Se uma startTime for fornecida sem uma diferença UTC, esta timeZone será usada|
|Hora de início|startTime|Hora de início no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Se uma startTime for fornecida sem uma diferença UTC, esta timeZone será usada|
<br>


## Próximas etapas

Veja abaixo os detalhes sobre como seguir adiante com os aplicativos lógicos e nossa comunidade.

## Criar um aplicativo lógico

Teste a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->