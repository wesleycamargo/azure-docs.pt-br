<properties
	pageTitle="Adicionar a ação de consulta em Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da ação de consulta para executar ações como Filtrar Matriz."
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
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# Introdução à ação de consulta

Com a ação de consulta, você pode trabalhar com lotes e matrizes a fim de executar os fluxos de trabalho para:

- Criar uma tarefa para todos os registros de alta prioridade de um banco de dados.
- Salvar todos os anexos em PDF para emails nos Blobs do Azure.

Para começar a usar a ação de consulta em um aplicativo lógico, confira [criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usar a ação de consulta
	
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) Atualmente, a ação de consulta tem uma operação exposta no designer – matriz de filtro. Isso permite que você consulte uma matriz e retorne um conjunto de resultados filtrados. Veja como é possível adicioná-lo em um aplicativo lógico:

1. Escolha o botão **Nova Etapa**
1. Escolha **Adicionar uma ação**
1. Na caixa de pesquisa de ação, digite "Filtro" para listar a ação **Filtrar matriz**

	![Selecionar ação de consulta](./media/connectors-native-query/using-action-1.png)

1. Selecione uma matriz a ser filtrada (a captura de tela abaixo mostra a matriz dos resultados de uma pesquisa do Twitter)
1. Crie uma condição para ser avaliada em cada item (a captura de tela abaixo filtra tweets de usuários com mais de 100 seguidores)

	![Concluir ação de consulta](./media/connectors-native-query/using-action-2.png)

1. A ação produzirá uma nova matriz que contém somente os resultados que atenderam aos requisitos do filtro
1. Clique em Salvar no canto superior esquerdo da barra de ferramentas e seu aplicativo lógico será salvo e publicado (ativado)

---

## Detalhes técnicos

Veja abaixo os detalhes das ações que esse conector permite.

## Ações de consulta

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) O conector tem uma ação possível.

|Ação|Descrição|
|---|---|
|Filtrar Matriz|Avalie uma condição para cada item em uma matriz e retorne os resultados|

### Detalhes da ação

A ação de consulta apresenta uma ação possível. Veja abaixo as informações sobre cada uma das ações, seus campos de entrada obrigatórios e opcionais, bem como os detalhes da saída correspondente associada ao uso delas.

#### Filtrar Matriz
Faça uma solicitação de saída HTTP. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|De*|de|A matriz a ser filtrada|
|Condição*|onde|A condição a ser avaliada para cada item|
<br>

**Detalhes da saída**

Resposta HTTP

|Nome da Propriedade|Tipo de Dados|Descrição|
|---|---|---|
|Matriz filtrada|array|Matriz que contém um objeto para cada resultado filtrado|

---

## Próximas etapas

Veja abaixo os detalhes sobre como seguir adiante com os aplicativos lógicos e nossa comunidade.

## Criar um aplicativo lógico

Teste a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->