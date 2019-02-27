---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333923"
---
As limitações a seguir se aplicam aos dados conforme são movidos para o Azure.

- Sugerimos que mais de um dispositivo não deve gravar no mesmo contêiner.
- Se você tiver um objeto existente do Azure (como um blob ou arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, o dispositivo substituirá o arquivo na nuvem.
- Uma hierarquia de diretórios vazios (sem nenhum arquivo) criada sob as pastas de compartilhamento não é carregada nos contêineres de blob.
- Para arquivos grandes, é recomendável usar o robocopy, porque ele tenta executar novamente a operação de cópia para erros intermitentes.
- Se o compartilhamento associado ao contêiner de armazenamento do Azure carregar blobs que correspondam ao tipo de blob definido para o compartilhamento no momento da criação, esses blobs não serão atualizados. Por exemplo, crie um compartilhamento de blob de blocos no dispositivo. Associe o compartilhamento de um contêiner de nuvem existente que tenha os blobs de páginas. Atualize esse compartilhamento para baixar os arquivos. Modifique alguns dos arquivos atualizados que já estão armazenados como blobs de páginas na nuvem. Você verá falhas de carregamento.
