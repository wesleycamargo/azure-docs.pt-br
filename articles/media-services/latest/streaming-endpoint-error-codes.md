---
title: Erros de empacotamento e a origem de serviços de mídia do Azure | Microsoft Docs
description: Este tópico descreve os erros que você pode receber do serviço do Azure Media Services Streaming de ponto de extremidade (Orgin).
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: ebcda6026f79bc88df91471d8be88316ba57bfc6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411382"
---
# <a name="streaming-endpoint-origin-errors"></a>Erros (origem) do ponto de extremidade de streaming 

Este tópico descreve os erros que você pode receber de serviços de mídia do Azure [serviço de ponto de extremidade de Streaming](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Solicitação Inválida

A solicitação contém informações inválidas e será rejeitada com esses códigos de erro e devido a um dos seguintes motivos:

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Um erro de sintaxe ou formato de URL. Os exemplos incluem solicitações para um tipo inválido, um fragmento inválido ou uma faixa de inválido. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|A solicitação não tem nenhuma marca de criptografia na URL. Solicitações CMAF exigem uma marcação de criptografia na URL. Outros protocolos que são configurados com mais de um tipo de criptografia também exigem a marca de criptografia para eliminar a ambiguidade. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|A solicitação para o armazenamento para atender à solicitação falhou com um erro de solicitação incorreta. |

## <a name="403-forbidden"></a>403 Proibido

A solicitação não foi permitida devido a um dos seguintes motivos:

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|A solicitação para o armazenamento para atender à solicitação falhou com uma falha de autenticação. Isso pode ocorrer se as chaves de armazenamento foram giradas e o serviço não foi possível sincronizar as chaves de armazenamento. <br/><br/>Entre em contato com o Azure dão suporte ao acessar [ajuda + suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Erro de operação de armazenamento, acesso falhou devido a permissões insuficientes de conta. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |A solicitação para o armazenamento para atender à solicitação falhou porque a conta de armazenamento está desabilitado. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Erro de operação de armazenamento, acesso falhou devido a erros genéricos. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |O formato de saída é bloqueado devido à configuração no StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |A criptografia é necessária para o conteúdo, política de entrega é necessária para o formato de saída. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |A criptografia não é definida nas configurações de política de entrega. |

## <a name="404-not-found"></a>404 Não Encontrado

A operação está tentando atuar em um recurso que não existe mais. Por exemplo, o recurso pode ter já foi excluído.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |A faixa solicitada não foi encontrada. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |O recurso solicitado não for encontrado. |
|MPE_UNAUTHORIZED |0x80890244 |O acesso é autorizado. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |O carimbo de hora solicitado não foi encontrado. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |O filtro de manifesto dinâmico solicitado não foi encontrado. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |O índice de fragmento solicitado está além do intervalo válido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Em tempo real de entradas não foi encontradas ao obter buffer moov de mídia. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Não é possível localizar o fragmento no momento solicitado para uma trilha específica.<br/><br/>Pode ser que o fragmento não está no armazenamento. Tente uma camada diferente da apresentação que pode ter um fragmento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Não é possível encontrar a entrada de mídia para a taxa de bits solicitada no manifesto. <br/><br/>Pode ser que o jogador solicitado para uma faixa de vídeo de uma determinada taxas de bits que não estava no manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Não é possível encontrar alguns metadados no manifesto ou não é possível localizar a troca de base de armazenamento. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Erro de operação de armazenamento, o recurso não foi encontrado. |

## <a name="409-conflict"></a>409 Conflito

A ID fornecida para um recurso em um `PUT` ou `POST` operação tiver sido tomada por um recurso existente. Use outra ID do recurso para resolver esse problema.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Erro de operação de armazenamento, erro de conflito.  |

## <a name="410"></a>410

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Transmissão ao vivo, quando o filtro que tem forceEndTimestamp definido como true, o início ou término de carimbo de hora está fora da janela DVR atual.|

## <a name="412-precondition-failure"></a>Falha de pré-condição 412

A operação especificou uma eTag diferente da versão disponível no servidor, ou seja, um erro de simultaneidade otimista. Repita a solicitação depois de ler a versão mais recente do recurso e atualizar o eTag na solicitação.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |O fragmento solicitado não está pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Erro de operação de armazenamento, uma falha de pré-condição.|

## <a name="415-unsupported-media-type"></a>Tipo de mídia sem suporte 415

O formato do conteúdo enviado pelo cliente está em um formato sem suporte.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Não deve aplicar a criptografia no conteúdo já criptografado.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A criptografia é inválida para o formato de entrada.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Tipo de política de entrega é inválido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |As configurações originais podem ser compartilhadas por vários formatos de saída.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Não há suporte para o formato de mídia ou o tipo. Por exemplo, os serviços de mídia não dá suporte a contagem de nível de qualidade mais de 64. Na marca de vídeo FLV, os serviços de mídia não oferece suporte a um quadro de vídeo com várias SPS e PPS vários.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Não há suporte para o formato de entrada do ativo solicitado. Os serviços de mídia dá suporte a (ao vivo), Smooth MP4 (VoD) e progressivo baixar formatos.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Não há suporte para o formato de saída solicitado. Os serviços de mídia dá suporte a progressivo, Smooth, DASH (CSF, CMAF) e HLS (v3, v4, CMAF), baixe os formatos.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Tipo de criptografia sem suporte encontrado.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Não há suporte para o tipo de mídia solicitado pelo formato de saída. Os tipos suportados são vídeo, áudio ou subtítulo "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|A mídia de origem ativo foi codificada com um formato de mídia que não é compatível com o formato de saída.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Ativo de origem foi codificado com um formato de vídeo não é compatível com o formato de saída. Há suporte para H.264 AVC, H.265 (HEVC, hev1 ou hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Ativo de origem foi codificado com um formato de áudio que não é compatível com o formato de saída. Formatos de áudio com suporte são AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|O ativo de origem protegido não pode ser convertido para o formato de saída.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|O formato de proteção não é compatível com o formato de saída.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|O formato de proteção não é compatível com o formato de entrada.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Vídeo NAL unidade inválida, por exemplo, somente a primeira NAL no exemplo pode ser um AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Tamanho da unidade de NAL inválido.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valor de tamanho de unidade NAL inválido.|
|MPE_FILTER_INVALID|0x80890236|Filtros de manifesto dinâmico inválidos.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versões de filtro inválido ou sem suporte.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo de filtro inválido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Intervalo inválido é especificado pelo filtro.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Atributo de controle inválido é especificado pelo filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Duração da janela de apresentação inválido é especificada pelo filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Ao vivo de retirada de inválido é especificado pelo filtro.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Há suporte para apenas um elemento absTimeInHNS filtros herdados.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Não há mais nenhum fluxos em todos os após a aplicação de filtros.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|A retirada em tempo real está além da janela DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|A retirada em tempo real é maior que a janela de apresentação.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Excedido o filtros do máximo permitido padrão dez (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Vários primeiro operador de qualidade de vídeo não é permitido em filtros de solicitação combinado.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|O número de atributos de taxa de bits de qualidade primeiro deve ser um (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Duração do segmento HLS deve ser menor do que um terço da janela DVR e HLS fazer.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Durações de fragmento devem ser menor ou igual a aproximadamente 20 segundos, ou os níveis de qualidade de entrada não são alinhado de tempo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Erro específico do DTS, não é possível localizar o ReservedBox quando ela deve apresentar no DTSSpecficBox durante a análise de caixa DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Erro específico do DTS, nenhum canal encontrado no DTSSpecficBox durante a análise do DTS caixa.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Erro específico do DTS, incompatibilidade de tipo de exemplo no DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Erro específico do DTS, vários ativos é definido, mas a incompatibilidade de tipo de exemplo DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Erro específico do DTS, o tamanho do fluxo principal é inválido.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Erro específico do DTS, resolução da amostra é inválida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Índice de extensão de subpropriedades de fluxo de erro específico do DTS, é inválido.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Erro específico do DTS, o número de bloco de fluxo subpropriedades é inválido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Erro específico do DTS, a frequência de amostragem é inválida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|O código de relógio de referência na extensão de subpropriedades de fluxo de erro específico do DTS, é inválido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Erro específico do DTS, o número do conjunto de remapeamento de alto-falantes é inválido.|

Para artigos de criptografia e exemplos, consulte:

- [Conceito: proteção de conteúdo](content-protection-overview.md)
- [Conceito: Políticas de chave de conteúdo](content-key-policy-concept.md)
- [Conceito: Políticas de streaming](streaming-policy-concept.md)
- [Exemplo: proteger com criptografia AES](protect-with-aes128.md)
- [Exemplo: proteger com DRM](protect-with-drm.md)

Para obter diretrizes de filtro, consulte:

- [Conceito: os manifestos dinâmicos](filters-dynamic-manifest-overview.md)
- [Conceito: filtros](filters-concept.md)
- [Exemplo: criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Exemplo: criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplo: criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

Para artigos em tempo real e exemplos, consulte:

- [Conceito: Visão geral de streaming ao vivo](live-streaming-overview.md)
- [Conceito: Eventos ao vivo e saídas em tempo real](live-events-outputs-concept.md)
- [Exemplo: tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 intervalo não satisfatório

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Erro de operação de armazenamento, retornado erro de http 416, intervalo inválido.|

## <a name="500-internal-server-error"></a>500 Erro Interno do Servidor

Durante o processamento da solicitação, os Serviços de Mídia encontram algum erro que impede o processamento de continuar.  

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Recebido e traduzido do Winhttp código de erro ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Recebido e traduzido do Winhttp código de erro ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Recebido e traduzido do Winhttp código de erro ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Erro de operação de armazenamento, InternalError geral de um dos erros HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Erro de operação de armazenamento, OperationTimedOut geral de um dos erros HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Erro de operação de armazenamento, outros erros HTTP 500 Erro interno ou OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Serviço Indisponível

Atualmente, o servidor não pode receber solicitações. Esse erro pode ter sido causado por excesso de solicitações ao serviço. O mecanismo de aceleração dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de caracteres do código de erro para obter informações mais detalhadas sobre o motivo pelo qual você obteve o erro 503. Esse erro nem sempre significa limitação.
> 

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Erro de operação de armazenamento, recebeu o erro ocupado do servidor HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Consulte também

- [Códigos de erro de codificação](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Conceitos de serviços de mídia do Azure](concepts-overview.md)
- [Cotas e limitações](limits-quotas-constraints.md)

## <a name="next-steps"></a>Próximas etapas

[Exemplo: acessar o código de erro e a mensagem de ApiException com .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
