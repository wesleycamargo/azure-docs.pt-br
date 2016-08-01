<properties
	pageTitle="Adicionar atraso em Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral das ações atrasar e atrasar até, e como usá-las como um aplicativo lógico do Azure."
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introdução às ações atrasar e atrasar até

Com as ações atrasar e atrasar até, você pode concluir cenários de fluxo de trabalho como

- Aguardar até um dia útil para enviar uma atualização de status por email
- Atrasar o fluxo de trabalho até que uma chamada HTTP tenha tempo para ser concluída antes da retomada e recuperação do resultado

Para começar a usar a ação atrasar em um aplicativo lógico, confira [criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usar as ações atrasar

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](connectors-overview.md).

Veja uma sequência de exemplos de como usar uma etapa de atraso em um aplicativo lógico:

1. Depois de adicionar um gatilho, clique em **Nova Etapa** para adicionar uma ação
1. Procure por "atrasar" para exibir as ações atrasar. Neste exemplo, escolheremos **Atrasar**

	![Ações atrasar](./media/connectors-native-delay/using-action-1.png)

1. Preencha qualquer uma das propriedades de ação para configurar o atraso

	![Configuração de atraso](./media/connectors-native-delay/using-action-2.png)

1. Clique em "Salvar" para publicar e ativar o aplicativo lógico

---

## Detalhes técnicos

Veja abaixo os detalhes das ações atrasar e atrasar até.

### Detalhes da ação

O gatilho de recorrência tem as propriedades a seguir que podem ser configuradas.

#### Atrasar

Atrasa a execução de um determinado intervalo de tempo. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Contagem*|count|O número de unidades de tempo a serem atrasadas|
|Unidade*|unit|A unidade de tempo - `Second`, `Minute`, `Hour` ou `Day`|
<br>

#### Atrasar até

Atrasa a execução até uma data/hora especificada. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Ano*|timestamp|O ano até o qual atrasar (GMT)|
|Mês*|timestamp|O mês até o qual atrasar (GMT)|
|Dia*|timestamp|O dia até o qual atrasar (GMT)|
<br>


## Próximas etapas

Veja abaixo os detalhes sobre como seguir adiante com os aplicativos lógicos e nossa comunidade.

## Criar um aplicativo lógico

Teste a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->