---
title: Usar o conector de Vídeo do Office 365 nos seus Aplicativos Lógicos | Microsoft Docs
description: Começar a usar o conector de Vídeo do Office 365 nos seus aplicativos lógicos do serviço de aplicativo do Microsoft Azure
services: ''
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe

---
# Introdução ao conector de Vídeo do Office365
Conecte-se ao Vídeo do Office 365 para obter informações sobre um vídeo do Office 365, obter uma lista de vídeos e muito mais. O conector de Vídeo do Office 365 pode ser usado em:

* Aplicativos lógicos 

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Não há suporte para esse conector em nenhuma versão do esquema anterior.
> 
> 

Com o Vídeo do Office 365, é possível:

* Criar seu fluxo de negócios baseado nos dados obtidos do Vídeo do Office 365. 
* Usar ações que verificam o status do portal do vídeo, obter uma lista de todos os vídeos em um canal e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, é possível usar o conector da Pesquisa do Bing para pesquisar vídeos do Office 365 e usar o conector do Vídeo do Office 365 para obter informações sobre determinado vídeo. Se o vídeo atender às suas necessidades, você poderá postá-lo no Facebook. 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O conector de Vídeo do Office 365 tem as ações a seguir disponíveis. Não há nenhum gatilho.

| Gatilhos | Ações |
| --- | --- |
| Nenhum |<ul><li>Verifica o status do portal do vídeo</li><li>Obter todos os Canais visíveis</li><li>Obter URL para reprodução do manifesto dos Serviços de Mídia do Azure para obter um vídeo</li><li>Obter o token de portador para obter acesso e descriptografar o vídeo</li><li>Obtém informações sobre determinado vídeo do Office 365</li><li>Lista todos os vídeos do Office 365 presentes em um canal</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o conector do Vídeo do Office 365
Ao adicionar esse conector aos seus aplicativos lógicos, é necessário entrar em sua conta de Vídeo do Office 365 e permitir que os aplicativos lógicos se conectem à sua conta.

> [!INCLUDE [Etapas para criar uma conexão com o Vídeo do Office 365](../../includes/connectors-create-api-office365video.md)]
> 
> 

Depois de criar a conexão, insira as propriedades do vídeo do Office 365, como o nome de locatário ou a ID do canal. A **Referência da API REST** neste tópico descreve essas propriedades.

> [!TIP]
> É possível usar essa mesma conexão do Vídeo do Office 365 em outros aplicativos lógicos.
> 
> 

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Verifica o status do portal do vídeo
Verifica o status do portal do vídeo para ver se os serviços de vídeo estão habilitados.```GET: /{tenant}/IsEnabled```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| locatário |string |sim |path |nenhum |O nome do locatário para o diretório do qual o usuário faz parte |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 404 |Não encontrado |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

### Obter todos os Canais visíveis
Obtém todos os canais aos quais o usuário tem acesso de exibição.```GET: /{tenant}/Channels```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| locatário |string |sim |path |nenhum |O nome do locatário para o diretório do qual o usuário faz parte |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 404 |Não encontrado |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### Lista todos os vídeos do Office365 presentes em um canal
Lista todos os vídeos do Office 365 presentes em um canal.```GET: /{tenant}/Channels/{channelId}/Videos```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| locatário |string |sim |path |nenhum |O nome do locatário para o diretório do qual o usuário faz parte |
| channelId |string |sim |path |nenhum |A ID do canal do qual os vídeos precisam ser obtidos |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 404 |Não encontrado |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### Obtém informações sobre um determinado vídeo do Office365
Obtém informações sobre determinado vídeo do Office 365.```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| locatário |string |sim |path |nenhum |O nome do locatário para o diretório do qual o usuário faz parte |
| channelId |string |sim |path |nenhum |A id do canal |
| videoId |string |sim |path |nenhum |A id de vídeo |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 404 |Não encontrado |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### Obtém a URL de reprodução do manifesto dos Serviços de Mídia do Azure para um vídeo
Obtém a URL para reprodução do manifesto dos Serviços de Mídia do Azure para um vídeo.```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| locatário |string |sim |path |nenhum |O nome do locatário para o diretório do qual o usuário faz parte |
| channelId |string |sim |path |nenhum |A id do canal |
| videoId |string |sim |path |nenhum |A id de vídeo |
| streamingFormatType |string |sim |query |nenhum |Tipo do formato de streaming. 1 - Smooth Streaming ou MPEG-DASH. 0 - Streaming HLS |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 404 |Não encontrado |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### Obter o token de portador para obter acesso para descriptografar o vídeo
Obter o token de portador para obter acesso e descriptografar o vídeo.```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| locatário |string |sim |path |nenhum |O nome do locatário para o diretório do qual o usuário faz parte |
| channelId |string |sim |path |nenhum |A id do canal |
| videoId |string |sim |path |nenhum |A id de vídeo |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 404 |Não encontrado |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## Definições de objeto
#### Canal: classe de canal
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| Title |string |não |
| Descrição |string |não |

#### Vídeo
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| Title |string |não |
| Descrição |string |não |
| CreationDate |string |não |
| Proprietário |string |não |
| ThumbnailUrl |string |não |
| VideoUrl |string |não |
| VideoDuration |inteiro |não |
| VideoProcessingStatus |inteiro |não |
| ViewCount |inteiro |não |

## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0525_2016-->