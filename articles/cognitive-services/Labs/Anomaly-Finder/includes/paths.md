---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228876"
---
<a name="paths"></a>
## <a name="paths"></a>Caminhos

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Detectar pontos de anomalias para os pontos de dados de série temporal solicitados
```
POST /anomalydetection
```


#### <a name="parameters"></a>parâmetros

|Type|NOME|DESCRIÇÃO|Esquema|
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



