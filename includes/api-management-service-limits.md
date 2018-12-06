---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: e01eebe41010135d0dc0a2cb4170e6b6687ff546
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292664"
---
| Recurso | Limite |
| --- | --- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho do cache | 5 GB por unidade<sup>2</sup> |
| Conexões de back-end simultâneas <sup>3</sup> por autoridade HTTP | 2048 por unidade<sup>4</sup> |
| Tamanho máximo de resposta em cache | 2 MB |
| Tamanho máximo do documento da política | 256KB<sup>5</sup> | 
| Domínios de gateway personalizado máximo por instância de serviço<sup>6</sup> | 20 | 
| Número máximo de instâncias de serviço por assinatura <sup>7</sup> | 5 | 
| Número máximo de assinaturas por instância de serviço <sup>7</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço <sup>7</sup> | 50 | 
| Número máximo de APIs por instância de serviço <sup>7</sup> | 50 | 
| Número máximo de operações de API por instância de serviço <sup>7</sup> | 1000 | 
| Duração total máxima da solicitação <sup>7</sup> | 30 segundos | 
| Tamanho máximo de carga útil do buffer <sup>7</sup> | 2 MB | 


<sup>1</sup> limites de colocação em escala dependem do tipo de preço. Para ver as camadas de preço e seus limites associados acesse [Preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> O tamanho do cache por unidade depende da camada de preços. Para ver as camadas de preço e seus limites associados acesse [Preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Conexões são agrupadas e reutilizadas, a menos que explicitamente fechadas pelo backend.<br/>
<sup>4</sup> Por unidade das camadas Básica, Padrão e Premium. A camada Developer está limitada a 1024. Não se aplica à camada de consumo.<br/> 
<sup>5</sup> Nas camadas Básica, Padrão e Premium. Na camada de consumo, o tamanho do documento de política é limitado a 4KB.<br/>
<sup>6</sup> Disponível apenas na camada Premium.<br/>
<sup>7</sup> Aplica-se apenas ao nível de consumo.<br/>



