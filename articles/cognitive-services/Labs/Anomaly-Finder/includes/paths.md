---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364001"
---
<a name="paths"></a>
## <a name="paths"></a>Caminhos

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Detectar pontos de anomalias para os pontos de dados de série temporal solicitados
```
POST /anomalydetection
```


#### <a name="parameters"></a>parâmetros

|type|NOME|DESCRIÇÃO|Esquema|
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



