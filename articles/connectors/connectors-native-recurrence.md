<properties
	pageTitle="Adicionar o gatilho de recorrência em aplicativos lógicos | Microsoft Azure"
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

Usando o gatilho de recorrência, você pode criar fluxos de trabalho poderosos na nuvem.

Por exemplo, você pode:

- Agendar um fluxo de trabalho para executar um procedimento armazenado SQL todos os dias.
- Enviar por email um resumo de todos os tweets da semana passada sobre uma determinada hashtag.

Para começar a usar o gatilho de recorrência em um aplicativo lógico, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Usar um gatilho de recorrência

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja uma sequência de exemplo de como configurar um gatilho de recorrência em um aplicativo lógico:

1. Adicione o gatilho **Recorrência** como a primeira etapa em um aplicativo lógico.
2. Preencha os parâmetros para o intervalo de recorrência.

O aplicativo lógico iniciará uma execução depois de cada intervalo de tempo.

![Gatilho HTTP](./media/connectors-native-recurrence/using-trigger.png)

## Detalhes do gatilho

O gatilho de recorrência tem as propriedades a seguir que você pode configurar.

Ele dispara um aplicativo lógico depois de um intervalo de tempo especificado. Um * significa que é um campo obrigatório.

|Nome de exibição|Nome da propriedade|Descrição|
|---|---|---|
|Frequência*|frequência|A unidade de tempo: `Second`, `Minute`, `Hour`, `Day` ou `Year`.|
|Intervalo*|intervalo|O intervalo da frequência determinada para a recorrência.|
|Fuso horário|timeZone|Se uma hora de início for fornecida sem uma diferença UTC, este fuso horário será usado.|
|Hora de início|startTime|A hora de início no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).|
<br>


## Próximas etapas

Agora, experimente a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md). Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->