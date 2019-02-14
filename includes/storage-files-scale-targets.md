---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 72b7bd722bc7634a707e10cbcd45768140ed527a
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55757618"
---
| Recurso | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos Premium (versão prévia) |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | (sem mínimo; pagamento conforme o uso) | 100 GiB |
| Tamanho máximo de um compartilhamento de arquivo | 5 TiB | 5 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivo | Sem limite | Sem limite |
| IOPS máximo por compartilhamento | 1.000 IOPS | Linha de base 5120 IOPS<br />15.360 IOPS com intermitência |
| Número máximo de políticas de acesso armazenadas por compartilhamento de arquivo | 5 | 5 |
| Taxa de transferência de destino para compartilhamento de arquivo único | Até 60 MiB/segundo | Até 612 MiB/s (provisionado) |
| Máximo de identificadores abertos por arquivo | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2048 caracteres | 2048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |