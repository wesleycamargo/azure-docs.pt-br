---
title: Notas de versão dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: db68f979239a5783338d99360209ae231a75c936
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945028"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure v3 

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas
* Planos de alterações

## <a name="october-2018---ga"></a>Outubro de 2018 - GA

Esta seção descreve as atualizações de outubro do AMS (Serviços de Mídia do Azure).

### <a name="rest-v3-ga-release"></a>Versão GA v3 de REST

A [versão GA v3 de REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para Live, filtros de manifesto de nível de Conta/Ativo e suporte a DRM.

#### <a name="azure-resource-management"></a>Gerenciamento de Recursos do Azure 

O suporte para o Gerenciamento de Recursos do Azure permite o gerenciamento unificado e a API de operações (agora, tudo em um só lugar).

A partir desta versão, é possível usar modelos do Resource Manager para criar Eventos ao Vivo.

#### <a name="improvement-of-asset-operations"></a>Melhoria das operações de Ativos 

As seguintes melhorias foram introduzidas:

- Ingestão de URLs HTTP(s) ou URLs de SAS do Armazenamento de Blobs do Azure.
- Especifique seus próprios nomes de contêiner para os Ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com o Azure Functions.

#### <a name="new-transform-object"></a>Novo objeto de Transformação

O novo objeto de **Transformação** simplifica o modelo de Codificação. O novo objeto facilita a criação e o compartilhamento de modelos e predefinições de codificação do Resource Manager. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticação do Azure Active Directory e RBAC

A Autenticação do Azure AD e o RBAC (Controle de Acesso Baseado em Função) permitem Transformações seguras, LiveEvents, Políticas de Chave de Conteúdo, Ativos por Função ou Usuários no Azure AD.

#### <a name="client-sdks"></a>SDKs do cliente  

Linguagens com suporte nos Serviços de Mídia v3: .NET Core, Java, Node.js, Ruby, Datilografado, Python, Go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação ao vivo

As seguintes atualizações de codificação ao vivo são apresentadas:

- Novo modo de baixa latência para ao vivo (10 segundos de ponta a ponta).
- Suporte aprimorado do RTMP (maior estabilidade e mais suporte de codificador de código-fonte).
- Ingestão segura de RTMPS.

    Ao criar um LiveEvent, você recebe 4 URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId) e apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS. 
- Suporte de transcodificação 24 horas por dia. 
- Suporte aprimorado de sinalização de anúncio no RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte aprimorado a Grade de Eventos

É possível ver os seguintes aprimoramentos de suporte à Grade de Eventos:

- Integração Azure EventGrid para facilitar o desenvolvimento com Aplicativos Lógicos e Azure Functions. 
- Inscreva-se para eventos sobre Codificação, Canais ao vivo, e muito mais.

### <a name="cmaf-support"></a>Suporte CMAF

Suporte de criptografia CMAF e 'cbcs' para players Apple HLS (iOS 11+) e MPEG-DASH que dão suporte a CMAF.

### <a name="video-indexer"></a>Video Indexer

A versão de GA do Video Indexer foi anunciada em agosto. Para obter novas informações sobre recursos atualmente com suporte, consulte [O que é Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planos de alterações

#### <a name="azure-cli-20"></a>CLI do Azure 2.0
 
O módulo da CLI 2.0 do Azure que inclui operações em todos os recursos (incluindo Live, Políticas de Chave de Conteúdo, Filtros de Conta/Ativo, Políticas de Streaming) estará disponível em breve. 

### <a name="known-issues"></a>Problemas conhecidos

Somente os clientes que usaram a API de versão prévia para material ativo ou AccountFilters são afetados pelo problema a seguir.

Se você criou Ativos ou Filtros de Conta entre 09/28 e 10/12 com CLI ou APIs dos Serviços de Mídia v3, será necessário remover todos os Ativos e AccountFilters e recriá-los devido a um conflito de versões. 

## <a name="may-2018---preview"></a>Maio de 2018 - Versão prévia

### <a name="net-sdk"></a>SDK .NET

Os recursos a seguir estão presentes no SDK .NET:

* **Transformações** e **Trabalhos** para codificar ou analisar o conteúdo de mídia. Para obter exemplos, consulte [Transmitir arquivos por stream](stream-files-tutorial-with-api.md) e [Analisar](analyze-videos-tutorial-with-api.md).
* **StreamingLocators** para publicar e transmitir conteúdo aos dispositivos do usuário final
* **StreamingPolicies** e **ContentKeyPolicies** para configurar a distribuição de chaves e a proteção de conteúdo (DRM) durante a entrega de conteúdo.
* **LiveEvents** e **LiveOutputs** para configurar a ingestão e arquivamento de conteúdo de transmissão ao vivo.
* **Ativos** para armazenar e publicar o conteúdo de mídia no Armazenamento do Azure. 
* **StreamingEndpoints** para configurar e dimensionar o empacotamento dinâmico, criptografia e streaming de conteúdo de mídia ao vivo e sob demanda.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar para ingerir o vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. Atualmente, o AMS v3 não oferece suporte à codificação de transferência em partes sobre URLs HTTPS.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral](media-services-overview.md)
