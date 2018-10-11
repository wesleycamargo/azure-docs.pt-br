---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: fb02496d9c107a2c21acca6c65ef69fdfceb4597
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904559"
---
<a name="paths"></a>
## <a name="paths"></a>Caminhos

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Detectar pontos de anomalias para os pontos de dados de série temporal solicitados
```
POST /anomalydetection
```


#### <a name="parameters"></a>parâmetros

|Tipo|NOME|DESCRIÇÃO|Esquema|
|---|---|---|---|
|**Corpo**|**body**  <br>*obrigatório*|Os pontos de dados de série temporal e o período, se necessário.|[solicitação](#request)|


#### <a name="responses"></a>Respostas

|Código HTTP|DESCRIÇÃO|Esquema|
|---|---|---|
|**200**|Operação com êxito.|< [resposta](#response) > matriz|
|**400**|Não é possível analisar a solicitação JSON.|Sem conteúdo|
|**403**|O certificado fornecido não é aceito pelo servidor.|Sem conteúdo|
|**405**|Método não permitido.|Sem conteúdo|
|**500**|Erro Interno do Servidor.|Sem conteúdo|


#### <a name="consumes"></a>Consome

* `application/json`


#### <a name="produces"></a>Produz

* `application/json`


#### <a name="tags"></a>Marcas

* anomalydetection



