---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 99314538f90404d7c2b72da0dd2da2d8ac60a08a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291765"
---
| Recurso | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos Premium (versão prévia) |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | Nenhum mínimo; pago conforme o uso | 100 giB; provisionado |
| Tamanho máximo de um compartilhamento de arquivos | 5 TiB | 5 TiB (visualização pública), 100 TiB (visualização pública limitada) |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivos | Sem limite | Sem limite |
| Máximo de IOPS por compartilhamento | IOPS DE 1.000 | IOPS base 5.120 com limite de intermitência 15.360 (visualização pública), 100.000 IOPS (visualização pública limitada)|
| Número máximo de políticas de acesso armazenadas por arquivo compartilhar | 5 | 5 |
| Taxa de transferência de destino para um compartilhamento de arquivo único | Até 60 MiB/s | Ver os valores de entrada e saída de compartilhamento de arquivo do premium|
| Egresso máximo para um compartilhamento de arquivo único | Consulte a taxa de transferência de destino de compartilhamento de arquivo padrão | Até 368 MiB/s (visualização pública), até 6,204 MiB/s (visualização pública limitada) |
| Máximo de entrada para um compartilhamento de arquivo único | Consulte a taxa de transferência de destino de compartilhamento de arquivo padrão | Até 245 MiB/s (visualização pública), até 4.136 MiB/s (visualização pública limitada) |
| Máximo de identificadores abertos por arquivo | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2.048 caracteres | 2.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |