---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59736849"
---
| Recurso | Destino        |
|----------|---------------|
| Tamanho máximo de um único contêiner de blob | Mesmo que a capacidade da conta de armazenamento máximo |
| Número máximo de blocos em um bloco de BLOBs ou de acréscimo | 50.000 blocos |
| Tamanho máximo de um bloco em um blob de blocos | 100 MiB |
| Tamanho máximo de um blob de blocos | 50.000 x 100 MiB (aproximadamente 4,75 TiB) |
| Tamanho máximo de um bloco em um blob de acréscimo | 4 MiB |
| Tamanho máximo de um blob de acréscimo | 50.000 x 4 MiB (aproximadamente 195 GiB) |
| Tamanho máximo de um blob de página | 8 TiB |
| Número máximo de políticas de acesso armazenadas por contêiner de blob | 5 |
|Taxa de transferência de destino para blob único |Até os limites de entrada/saída de conta de armazenamento<sup>1</sup> |

<sup>1</sup> taxa de transferência do único objeto depende de vários fatores, incluindo, mas não limitado a: simultaneidade, o tamanho da operação, o nível de desempenho, velocidade do código-fonte para carregamentos e destino para downloads.