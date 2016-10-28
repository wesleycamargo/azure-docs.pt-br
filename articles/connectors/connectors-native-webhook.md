<properties
	pageTitle="Conector de webhook do Aplicativo Lógico | Microsoft Azure"
	description="Visão geral da ação e dos gatilhos de webhook para execução de ações como Filtrar Matriz."
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
   ms.date="07/21/2016"
   ms.author="jehollan"/>

# Introdução ao conector de webhook

Com a ação e o gatilho de webhook, você pode disparar, pausar e retomar fluxos para:

- Disparar de um [Hub de Eventos do Azure assim que um item](https://github.com/logicappsio/EventHubAPI) é recebido
- Aguardar uma aprovação antes de continuar um fluxo de trabalho

As informações sobre como criar uma API que permita a assinatura de webhook podem ser encontradas [neste artigo sobre como criar conectores de Aplicativo Lógico](../app-service-logic/app-service-logic-create-api-app.md).

---

## Usar o gatilho de webhook

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md). Um gatilho de webhook é especialmente útil, pois ele não depende da sondagem de novos itens – como o [gatilho de solicitação](./connectors-native-reqres.md) que o aplicativo lógico vai acionar no instante que um evento ocorrer. Isso é feito registrando uma *URL de retorno de chamada* em um serviço que pode ser usado para acionar o aplicativo lógico conforme a necessidade.

Veja uma sequência de exemplo de como configurar um gatilho HTTP no designer de aplicativo lógico. Isso pressupõe que você já implantou ou está acessando uma API que segue [o padrão de assinatura e cancelamento de assinatura de webhook usado nos Aplicativos Lógicos](../app-service-logic/app-service-logic-create-api-app.md#webhook-triggers). A chamada à assinatura é feita sempre que um aplicativo lógico é salvo com um novo webhook ou alternado de desabilitado para habilitado. A chamada ao cancelamento da assinatura é feita sempre que um gatilho de webhook do aplicativo lógico é removido e salvo ou alternado de habilitado para desabilitado.

1. Adicione o gatilho de **Webhook HTTP** como a primeira etapa em um aplicativo lógico
1. Preencha os parâmetros das chamadas à assinatura e ao cancelamento da assinatura do webhook
	- Isso segue o mesmo padrão do formato da [ação HTTP](./connectors-native-http.md)

	![Gatilho de HTTP](./media/connectors-native-webhook/using-trigger.png)

1. Adicione pelo menos uma ação
1. Clique em salvar para publicar o aplicativo lógico – isso chamará o ponto de extremidade de assinatura com a URL de retorno de chamada necessário para disparar esse aplicativo lógico
1. Sempre que o serviço fizer um `HTTP POST` para a URL de retorno de chamada, o aplicativo lógico vai disparar (e incluir todos os dados passados na solicitação)

## Usar a ação de webhook
	
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) Uma ação de webhook é especialmente útil, pois ela registrará uma *URL de retorno de chamada* com um serviço e aguardará até que a URL seja chamada antes da retomada. ["Enviar Email de Aprovação"](./connectors-create-api-office365-outlook.md) é um exemplo de conector que segue esse padrão. Você pode estender esse padrão para qualquer serviço por meio de ação de webhook. Isso pressupõe que você já implantou ou está acessando uma API que segue [o padrão de assinatura e cancelamento de assinatura de webhook usado nos Aplicativos Lógicos](../app-service-logic/app-service-logic-create-api-app.md#webhook-actions). A chamada à assinatura é feita sempre que um aplicativo lógico executa a ação de webhook. A chamada ao cancelamento da assinatura é feita sempre que uma execução é cancelada enquanto aguarda uma resposta, ou antes do fim da execução do aplicativo lógico.

Para adicionar uma ação de webhook:

1. Escolha o botão **Nova Etapa**
1. Escolha **Adicionar uma ação**
1. Na caixa de pesquisa de ação, digite "webhook" para listar a ação **Webhook HTTP**

	![Selecionar ação de consulta](./media/connectors-native-webhook/using-action-1.png)

1. Preencha os parâmetros das chamadas à assinatura e ao cancelamento da assinatura do webhook
	- Isso segue o mesmo padrão do formato da [ação HTTP](./connectors-native-http.md)

	![Concluir ação de consulta](./media/connectors-native-webhook/using-action-2.png)

	- No tempo de execução, o aplicativo lógico chamará o ponto de extremidade de assinatura assim que ele atingir a etapa

1. Clique em Salvar no canto superior esquerdo da barra de ferramentas e seu aplicativo lógico será salvo e publicado (ativado)

---

## Detalhes técnicos

Veja abaixo os detalhes do gatilho e da ação aos quais o webhook dá suporte.

## Gatilhos de Webhook

Um gatilho é uma operação para iniciar um fluxo de trabalho. [Saiba mais sobre gatilhos.](connectors-overview.md) Este conector tem um gatilho.

|Ação|Descrição|
|---|---|
|Webhook HTTP|Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a acionar o aplicativo lógico conforme a necessidade.|

### Detalhes do gatilho

O conector de webhook apresenta um possível gatilho. Veja abaixo as informações sobre a ação, seus campos de entrada obrigatórios e opcionais, bem como os detalhes da saída correspondente associada ao uso dela.

#### Webhook HTTP
Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a acionar o aplicativo lógico conforme a necessidade. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Método de Assinatura*|estático|Método HTTP a ser usado para solicitação de assinatura|
|URI da Assinatura*|uri|URI do HTTP a ser usado para solicitação de assinatura|
|Método de Cancelamento da Assinatura*|estático|Método HTTP a ser usado para solicitação de cancelamento da assinatura|
|URI do Cancelamento da Assinatura*|uri|URI do HTTP a ser usado para solicitação de cancelamento da assinatura|
|Corpo da Assinatura|corpo|Corpo da solicitação HTTP da assinatura|
|Cabeçalhos da Assinatura|headers|Cabeçalhos da solicitação HTTP da assinatura|
|Autenticação da Assinatura|authencation|Autenticação HTTP a ser usada para assinatura. [Confira o artigo sobre o conector HTTP](./connectors-native-http.md#authenication) para obter detalhes|
|Corpo do Cancelamento da Assinatura|corpo|Corpo da solicitação HTTP do cancelamento da assinatura|
|Cabeçalhos do Cancelamento da Assinatura|headers|Cabeçalhos da solicitação HTTP do cancelamento da assinatura|
|Autenticação do Cancelamento da Assinatura|autenticação|Autenticação HTTP a ser usada para cancelamento da assinatura. [Confira o artigo sobre o conector HTTP](./connectors-native-http.md#authenication) para obter detalhes|
<br>

**Detalhes da saída**

Solicitação de webhook

|Nome da Propriedade|Tipo de Dados|Descrição|
|---|---|---|
|Cabeçalhos|objeto|Cabeçalhos da solicitação de webhook|
|Corpo|objeto|Objeto da solicitação de webhook|
|Código de status|int|Código de status da solicitação de webhook|

## Ações de Webhook

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) O conector tem uma ação possível.

|Ação|Descrição|
|---|---|
|Webhook HTTP|Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a retomar uma etapa do fluxo de trabalho conforme a necessidade.|

### Detalhes da ação

O conector de webhook apresenta uma possível ação. Veja abaixo as informações sobre a ação, seus campos de entrada obrigatórios e opcionais, bem como os detalhes da saída correspondente associada ao uso dela.

#### Webhook HTTP
Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a retomar uma etapa do fluxo de trabalho conforme a necessidade. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Método de Assinatura*|estático|Método HTTP a ser usado para solicitação de assinatura|
|URI da Assinatura*|uri|URI do HTTP a ser usado para solicitação de assinatura|
|Método de Cancelamento da Assinatura*|estático|Método HTTP a ser usado para solicitação de cancelamento da assinatura|
|URI do Cancelamento da Assinatura*|uri|URI do HTTP a ser usado para solicitação de cancelamento da assinatura|
|Corpo da Assinatura|corpo|Corpo da solicitação HTTP da assinatura|
|Cabeçalhos da Assinatura|headers|Cabeçalhos da solicitação HTTP da assinatura|
|Autenticação da Assinatura|authencation|Autenticação HTTP a ser usada para assinatura. [Confira o artigo sobre o conector HTTP](./connectors-native-http.md#authentication) para obter detalhes|
|Corpo do Cancelamento da Assinatura|corpo|Corpo da solicitação HTTP do cancelamento da assinatura|
|Cabeçalhos do Cancelamento da Assinatura|headers|Cabeçalhos da solicitação HTTP do cancelamento da assinatura|
|Autenticação do Cancelamento da Assinatura|autenticação|Autenticação HTTP a ser usada para cancelamento da assinatura. [Confira o artigo sobre o conector HTTP](./connectors-native-http.md#authentication) para obter detalhes|
<br>

**Detalhes da saída**

Solicitação de webhook

|Nome da Propriedade|Tipo de Dados|Descrição|
|---|---|---|
|Cabeçalhos|objeto|Cabeçalhos da solicitação de webhook|
|Corpo|objeto|Objeto da solicitação de webhook|
|Código de status|int|Código de status da solicitação de webhook|

---

## Próximas etapas

Veja abaixo os detalhes sobre como seguir adiante com os aplicativos lógicos e nossa comunidade.

## Criar um aplicativo lógico

Teste a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->