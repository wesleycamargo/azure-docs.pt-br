<properties
pageTitle="Usar a API de Vídeo do Office 365 nos seus Aplicativos Lógicos | Microsoft Azure"
description="Começar a usar a API de Vídeo do Office 365 (conector) nos seus aplicativos lógicos do serviço de aplicativo do Microsoft Azure"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/18/2016"
ms.author="deonhe"/>

# Introdução à API de Vídeo do Office365

A API de Vídeo do Office 365 fornece uma API para trabalhar com canais e vídeos do Office 365.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview clique em [Conector de Vídeo do Office365](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md).

Com o Vídeo do Office365, você pode:

* Use-o para compilar aplicativos lógicos

Para adicionar uma operação a aplicativos lógicos, consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Agora vamos falar sobre gatilhos e ações

A API de Vídeo do Office365 pode ser usada como uma ação; não há nenhum gatilho. Todas as APIs dão suporte a dados nos formatos JSON e XML.

 A API de Vídeo do Office365 tem as seguintes ações e/ou gatilhos disponíveis:

### Ações de API de Vídeo do Office365
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|IsVideoPortalEnabled|Verifica o status de portal vídeo para ver se os serviços de vídeo estão habilitados|
|ListViewableChannels|Obtém todos os canais aos quais o usuário tem acesso de exibição|
|ListVideos|Lista todos os vídeos do Office365 presentes em um canal|
|GetVideo|Obtém informações sobre um determinado vídeo do Office365|
|GetPlaybackUrl|Obtém a URL de reprodução do manifesto dos Serviços de Mídia do Azure para um vídeo|
|GetStreamingKeyAccessToken|Obter o token de portador para obter acesso para descriptografar o vídeo|
ActionsTableReplaceMeLater## Crie uma conexão com a API de Vídeo do Office365 Para usar a API de Vídeo do Office365, crie primeiro uma **conexão** e, em seguida, forneça os detalhes dessas propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer as Credenciais do SharePoint Online|


>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos lógicos.

## Referência da API REST do Vídeo do Office365
#### Esta documentação destina-se à versão: 1.0


### Verifica o status de portal vídeo para ver se os serviços de vídeo estão habilitados
**```GET: /{tenant}/IsEnabled```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|string|sim|path|nenhum|O nome do locatário para o diretório do qual o usuário faz parte|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obtém todos os canais aos quais o usuário tem acesso de exibição
**```GET: /{tenant}/Channels```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|string|sim|path|nenhum|O nome do locatário para o diretório do qual o usuário faz parte|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Lista todos os vídeos do Office365 presentes em um canal
**```GET: /{tenant}/Channels/{channelId}/Videos```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|string|sim|path|nenhum|O nome do locatário para o diretório do qual o usuário faz parte|
|channelId|string|sim|path|nenhum|A ID do canal do qual os vídeos precisam ser obtidos|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obtém informações sobre um determinado vídeo do Office365
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|string|sim|path|nenhum|O nome do locatário para o diretório do qual o usuário faz parte|
|channelId|string|sim|path|nenhum|A id do canal|
|videoId|string|sim|path|nenhum|A id de vídeo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obtém a URL de reprodução do manifesto dos Serviços de Mídia do Azure para um vídeo
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|string|sim|path|nenhum|O nome do locatário para o diretório do qual o usuário faz parte|
|channelId|string|sim|path|nenhum|A id do canal|
|videoId|string|sim|path|nenhum|A id de vídeo|
|streamingFormatType|string|sim|query|nenhum|Tipo do formato de streaming. 1 - Smooth Streaming ou MPEG-DASH. 0 - Streaming HLS|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obter o token de portador para obter acesso para descriptografar o vídeo
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|locatário|string|sim|path|nenhum|O nome do locatário para o diretório do qual o usuário faz parte|
|channelId|string|sim|path|nenhum|A id do canal|
|videoId|string|sim|path|nenhum|A id de vídeo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|404|Não encontrado|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



## Definições de objeto: 

 **Canal**: classe de canal

Propriedades obrigatórias para o Canal:


Nenhuma das propriedades são obrigatórias.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|string|
|Title|string|
|Descrição|string|



 **Vídeo**: classe de vídeo

Propriedades obrigatórias para Vídeo:


Nenhuma das propriedades são obrigatórias.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|string|
|Title|string|
|Descrição|string|
|CreationDate|string|
|Proprietário|string|
|ThumbnailUrl|string|
|VideoUrl|string|
|VideoDuration|inteiro|
|VideoProcessingStatus|inteiro|
|ViewCount|inteiro|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->