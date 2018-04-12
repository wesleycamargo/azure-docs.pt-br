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
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
| Recurso | Limite |
| --- | --- |
| Unidades de escala | 10 por região<sup>1</sup> |
| Cache | 5 GB por unidade<sup>1</sup> |
| Conexões simultâneas de back-end<sup>2</sup> por autoridade HTTP | 2048 por unidade<sup>3</sup> |
| Tamanho máximo de resposta em cache | 10MB |
| Máximos domínios de gateway personalizados | 20 por instância de serviço<sup>4</sup> |


<sup>1</sup>Os limites do Gerenciamento de API são diferentes para cada camada de preços. Para ver as camadas de preço e seus limites associados acesse [Preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> As conexões são agrupadas e usadas novamente, a menos que explicitamente fechadas pelo back-end.
<sup>3</sup> Camadas Basic, Standard e Premium. A camada do desenvolvedor é limitada a 1024.
<sup>4</sup> Disponível somente na camada Premium.


