---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: ec75cf35bb503e49885664a6682e3be319cc45f6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553244"
---
| Recurso | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos Premium (versão prévia) |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | Nenhum mínimo; pago conforme o uso | 100 GiB |
| Tamanho máximo de um compartilhamento de arquivos | 5 TiB | 5 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivos | Sem limite | Sem limite |
| Máximo de IOPS por compartilhamento | IOPS DE 1.000 | Linha de base 5.120 IOPS<br />15.360 IOPS com intermitência |
| Número máximo de políticas de acesso armazenadas por arquivo compartilhar | 5 | 5 |
| Taxa de transferência de destino para um compartilhamento de arquivo único | Até 60 MiB/s | Até 612 MiB/s (provisionado) |
| Máximo de identificadores abertos por arquivo | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2.048 caracteres | 2.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |