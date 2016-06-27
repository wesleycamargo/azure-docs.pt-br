<properties
	pageTitle="Integração do SDK para Web do Azure Mobile Engagement | Microsoft Azure"
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
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#Como integrar o Engagement em um aplicativo Web

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimento descreve a maneira mais simples de ativar as funções de Análise e Monitoramento do Engagement em seu aplicativo Web.

As etapas a seguir são suficientes para ativar o relatório de logs necessário para calcular todas as estatísticas referentes a Usuários, Sessões, Atividades, Falhas e Suporte Técnico. O relatório de logs necessários para calcular outras estatísticas, como Trabalhos, Erros e Eventos deve ser feito manualmente usando a API do Engagement (consulte [How to use the advanced Mobile Engagement tagging API in a Web application](mobile-engagement-web-use-engagement-api.md) (Como usar a API de marcação do Mobile Engagement em um aplicativo Web)), já que essas estatísticas são dependentes do aplicativo.

## Introdução

Baixe [aqui](http://aka.ms/P7b453) o SDK para Web. O SDK é fornecido como um único arquivo JavaScript chamado **azure-engagement.js** que você precisa incluir em cada página do seu site ou seu aplicativo Web.

Esse script **DEVE** ser carregado **DEPOIS** de um trecho de código ou script que você deve escrever para configurar o Engagement para seu aplicativo.

## Compatibilidade de navegador

O SDK para Web do Engagement usa codificação/decodificação nativa JSON e solicitações AJAX entre domínios (que dependem da especificação W3C CORS).

* Edge 12 e superior
* IE 10+
* Firefox 3.5 e superior
* Chrome 4+
* Safari 6+
* Opera 12 e superior

## Configurar o Engagement

Escreva um script que crie um objeto JavaScript **azureEngagement** global como o que se segue.
 
Como seu site pode conter várias páginas, este exemplo supõe que esse script também esteja incluído em todas as páginas, e nós o chamaremos de `azure-engagement-conf.js` neste procedimento.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

A `connectionString` do seu aplicativo é exibida no Portal do Azure.

> [AZURE.NOTE] `appVersionName` e `appVersionCode` são opcionais; é recomendável configurá-los para que a análise processe informações de versão.

## Incluir scripts do Engagement em suas páginas

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

Ou

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## Alias

Uma vez carregado, o script do SDK cria o alias **engagement** para acessar as APIs do SDK (ele não pode ser usado para definir a configuração do SDK). Esse alias será usado como uma referência nesta documentação.

Observe que se o alias padrão estiver em conflito com outra variável global da sua página, você poderá redefini-lo na configuração antes de carregar o SDK como se segue:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## Relatórios básicos

### Rastreamento de sessão

Uma sessão do Engagement é dividida em uma sequência de atividades identificadas por um nome.

Em um site da Web clássico, é recomendável declarar uma atividade diferente em cada página do site. Em um site da Web ou aplicativo Web que nunca muda a página atual, talvez seja conveniente rastrear as atividades de uma maneira mais adequada.

De qualquer forma, para iniciar ou alterar a atividade do usuário atual, chame a função `engagement.agent.startActivity` como neste exemplo:

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

Uma sessão aberta será encerrada automaticamente pelo servidor do Engagement em 3 minutos depois que a página de aplicativo é fechada.

Como alternativa, você também pode encerrar uma sessão manualmente chamando `engagement.agent.endActivity`; isso definirá a atividade do usuário atual como 'ociosa' e encerrará de fato a sessão 10 segundos depois disso, a não ser que uma nova chamada a `engagement.agent.startActivity` retome a sessão nesse meio tempo.
 
Esse atraso de 10 segundos pode ser configurado no objeto **engagement** global:

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] `engagement.agent.endActivity` não pode ser usado no retorno de chamada `onunload`, uma vez que não é possível fazer chamadas Ajax nesse estágio.

## Relatórios avançados

Como opção, se quiser relatar `events`. `errors` e `jobs` específicos de aplicativo, você precisará usar a API do Engagement por meio do objeto `engagement.agent`.

A API do Engagement permite usar todos os recursos avançados do Engagement e é detalhada em [Como usar o API do Engagement no Windows Universal](mobile-engagement-web-use-engagement-api.md).

## Personalizar as URLs usadas para chamadas AJAX

Você pode personalizar as URLs usadas pelo SDK. Por exemplo, para redefinir a URL de log (ponto de extremidade SDK para registro em log), você pode substituir a configuração como esta:

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

Se suas funções de URL retornarem uma cadeia de caracteres começando com `/`, `//`, `http://` ou `https://`, o esquema padrão não será usado.

Por padrão, o esquema `https://` é usado para essas URLs. Se desejar personalizar o esquema padrão, substitua a configuração desta forma:

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->