<properties
	pageTitle="Procedimentos de atualização do SDK para Web do Azure Mobile Engagement | Microsoft Azure"
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


# Procedimentos de atualização do SDK para Web do Azure Mobile Engagement

Se você já tiver integrado uma versão anterior do SDK no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você precisará seguir vários procedimentos se perdeu várias versões do SDK. Por exemplo, se você migrar do 1.4.0 para 1.6.0 você tem que primeiro seguir o procedimento "de 1.4.0 a 1.5.0” e depois o procedimento "de 1.5.0 a 1.6.0".

Seja qual for a versão de atualização, você precisa substituir o `azure-engagement.js` pelo novo.

## De 1.2.1 para 2.0.0

O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Mobile Engagement do Azure. Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para 1.2.1 e depois aplicar o procedimento a seguir.

Essa versão do SDK para Web do Engagement não é compatível com samsung-tv, OperaTV, webOS e o recurso Reach.

>[AZURE.IMPORTANT] O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement.

### Arquivos JavaScript

Substitua o arquivo `capptain-sdk.js` pelo arquivo `azure-engagement.js` e atualize as importações de script adequadamente.

### Remover o Capptain Reach

Essa versão do SDK para Web do Engagement não é compatível com o recurso Reach. Se você tiver integrado o Capptain Reach ao seu aplicativo, ele precisará ser removido.

Remova a importação de css do Reach da sua página e exclua o arquivo css relacionado (por padrão, capptain-reach.css).

Exclua os recursos do Reach: a imagem de fechamento (capptain-close.png, por padrão) e o ícone da marca (capptain-notification-icon, por padrão).

Remova a interface de usuário do Reach para notificações no aplicativo, o layout padrão se parece com este:

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

Remova a interface de usuário do Reach para anúncios e pesquisas de texto\\Wb, o layout padrão se parece com este:

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

Remova o objeto `reach` da sua configuração, se houver. Ele se parece com o seguinte:

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Remova qualquer outra personalização do Reach, como categorias.

### Remover APIs substituídas

Algumas das APIs do Capptain foram substituídas na versão do SDK do Engagement.

Remova todas as chamadas para as seguintes APIs: `agent.connect`, `agent.disconnect`, `agent.pause`, `agent.sendMessageToDevice`.

Remova os seguintes retornos de chamada, se houver, da configuração do Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, `onPushMessageReceived`.

### Configuração

Agora o Engagement usa uma cadeia de conexão para configurar os identificadores SDK, como o identificador do aplicativo.

Substitua a ID do aplicativo pela sua cadeia de conexão. Observe também que o objeto global da configuração do SDK é movido de `capptain` para `azureEngagement`.

Antes da migração:

	window.capptain = {
	  appId: ...,
	  [...]
	};

Após a migração:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

### APIs do JavaScript

O objeto `window.capptain` global do JavaScript foi renomeado para `window.azureEngagement`, mas você pode usar o alias `window.engagement` para chamadas de API (você não pode usar o alias para definir a configuração do SDK).

Por exemplo: `capptain.deviceId` se torna `engagement.deviceId`, `capptain.agent.startActivity` se torna `engagement.agent.startActivity` etc...

<!---HONumber=AcomDC_0615_2016-->