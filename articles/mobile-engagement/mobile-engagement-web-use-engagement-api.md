<properties
	pageTitle="APIs do SDK para Web do Azure Mobile Engagement | Microsoft Azure"
	description="Atualizações e procedimentos mais recentes do SDK para Web do Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />

# Como usar a API do Engagement em um aplicativo Web

Este documento é um complemento ao documento [How to Integrate Engagement on a Web application](mobile-engagement-web-integrate-engagement.md) (Como integrar o Engagement em um aplicativo Web). Ele fornece detalhes aprofundados sobre como usar a API do Engagement para relatar as estatísticas do aplicativo.

A API do Engagement é fornecida pelo objeto `engagement.agent`. `engagement` é o alias padrão do SDK do Engagement que pode ser redefinido na configuração do SDK.

## Conceitos de Engagement

As partes a seguir refinam os [Conceitos do Mobile Engagement](mobile-engagement-concepts.md) comuns para a plataforma da Web.

### `Session` e `Activity`

Se o usuário permanecer mais de alguns segundos ocioso entre duas *atividades*, a sua sequência de *atividades* é dividida em duas *sessões* diferentes. Esses poucos segundos são chamados de *tempo limite da sessão*.

Se o aplicativo Web não declarar o fim das atividades do usuário por si só (chamando a função `engagement.agent.endActivity`), o servidor do Engagement vai expirar automaticamente a sessão do usuário em 3 minutos depois que a página do aplicativo for fechada. Esse comportamento é chamado de *tempo limite da sessão* do servidor.

### `Crash`

Não há relatório automatizado de exceções não capturadas do JavaScript. No entanto, você pode relatar falhas manualmente usando a função `sendCrash` (veja abaixo).

## Relatório de atividades

### O usuário inicia uma nova atividade

	engagement.agent.startActivity("MyUserActivity");

É necessário chamar `startActivity()` sempre que houver alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão de usuário.

### O usuário encerra sua atividade atual

	engagement.agent.endActivity();

É necessário chamar `endActivity()` pelo menos uma vez quando o usuário conclui sua última atividade. Isso informa o SDK do Engagement que o usuário está ocioso no momento e que a sessão do usuário precisa ser fechada quando o tempo limite da sessão expirar (se você chamar `startActivity()` antes de expirar o tempo limite da sessão, a sessão será simplesmente retomada).

Muitas vezes, isso dificulta ou impossibilita capturar o fim das atividades do usuário em ambientes da Web (nenhuma chamada confiável quando a janela do navegador é fechada). Por esse motivo, o servidor do Engagement expira automaticamente a sessão do usuário em 3 minutos depois que a página do aplicativo é fechada.

## Eventos de relatório

### Eventos de sessão

Eventos de sessão são geralmente usados para relatar as ações executadas por um usuário durante a sessão.

**Exemplo sem dados adicionais:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login');
	  // [...]
	}

**Exemplo com dados adicionais:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login', {user: 'alice'});
	  // [...]
	}

### Eventos autônomos

Ao contrário dos eventos de sessão, os eventos independentes podem ocorrer fora do contexto de uma sessão.

Para isso, use ``engagement.agent.sendEvent`` em vez de ``engagement.agent.sendSessionEvent``.

## Erros de relatório

### Erros de sessão

Erros de sessão são geralmente usados para relatar os erros que afetam o usuário durante a sessão.

**Exemplo sem dados adicionais:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short');
	  }
	  // [...]
	}

**Exemplo com dados adicionais:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short', {length: 4});
	  }
	  // [...]
	}

### Erros autônomos

Ao contrário dos erros de sessão, os erros autônomos podem ocorrer fora do contexto de uma sessão.

Para isso, use `engagement.agent.sendError` em vez de `engagement.agent.sendSessionError`.

## Relatório de trabalhos

### Exemplo

Suponha que você queira monitorar uma solicitação Ajax:
			
	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	  // [...]
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Relatando erros durante um trabalho

Os erros podem estar relacionados a um trabalho em execução, em vez de serem relacionados a sessão do usuário atual.

**Exemplo:**

Suponha que você queira relatar um erro se uma solicitação Ajax falhar:

	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	    // [...]
	    if (xhr.status == 0 || xhr.status >= 400) {
	      engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
	    }
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Relatar eventos durante um trabalho

Os erros podem estar relacionados a um trabalho em execução, em vez de estar relacionados à sessão do usuário atual graças à função `engagement.agent.sendJobEvent`.

Essa função funciona exatamente como o `engagement.agent.sendJobError`.

### Relatórios de falhas

A função `sendCrash` é usada para relatar falhar manualmente.

O argumento `crashid` é uma cadeia de caracteres usada para identificar o tipo de falha. O argumento `crash` geralmente é o rastreamento de pilha da falha como uma cadeia de caracteres.

	engagement.agent.sendCrash(crashid, crash);

## Parâmetros adicionais

Dados arbitrários podem ser anexados a um evento, um erro, uma atividade ou um trabalho.

Esses dados podem ser qualquer objeto JSON (não uma matriz ou tipos primitivos).

**Exemplo**

	var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
	engagement.agent.sendEvent("video_clicked", extras);

### Limites

#### simétricas

Cada chave no objeto deve corresponder à seguinte expressão regular:

	^[a-zA-Z][a-zA-Z_0-9]*

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### Valores

Os valores são limitados aos tipos de booliano, número e cadeia de caracteres.

#### Tamanho

Os adicionais são limitados a **1024** caracteres por chamada (uma vez codificados em JSON pelo SDK).

## Relatório de informações de aplicativo

Você pode relatar manualmente informações (ou quaisquer outras informações específicas do aplicativo) de controle usando a função `sendAppInfo()`.

Observe que essas informações podem ser enviadas de forma incremental: somente o último valor para uma determinada chave será mantido por um determinado dispositivo.

Assim como os extras de evento, qualquer objeto JSON pode ser usado para abstrair informações do aplicativo. Observe que as matrizes ou os subobjetos serão tratados como cadeias de caracteres simples (usando a serialização JSON).

### Exemplo

Aqui está um exemplo de código para enviar a data de nascimento e sexo do usuário:

	var appInfos = {"birthdate":"1983-12-07","gender":"female"};
	engagement.agent.sendAppInfo(appInfos);

### Limites

#### simétricas

Cada chave no objeto deve corresponder à seguinte expressão regular:

	^[a-zA-Z][a-zA-Z_0-9]*

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### Tamanho

As informações do aplicativo são limitadas a **1024** caracteres por chamada (uma vez codificadas em JSON pelo SDK).

No exemplo anterior, o JSON enviado para o servidor tem 44 caracteres:

	{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=AcomDC_0615_2016-->