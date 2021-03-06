---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966322"
---
Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Verifique se todos os arquivos que são carregados estão em conformidade com esses limites.

| Tipo de objeto do Azure | Limite de upload                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | ~ 4,75 TB                                                 |
| Blob de páginas         | 1 TB <br> Todos os arquivos carregados no formato de Blob de Páginas precisarão ser alinhados com 512 bytes (um integral múltiplo), caso contrário o upload falhará. <br> O VHD e o VHDX são alinhados com 512 bytes. |
| Arquivos do Azure         | 1 TB <br> Todos os arquivos carregados no formato de Blob de Páginas precisarão ser alinhados com 512 bytes (um integral múltiplo), caso contrário o upload falhará. <br> O VHD e o VHDX são alinhados com 512 bytes. |

> [!IMPORTANT]
> Criação de arquivos (independentemente do tipo de armazenamento) é permitida até 5 TB. No entanto, se você criar um arquivo cujo tamanho é maior que o limite de carregamento definido na tabela anterior, o arquivo não será carregado. Você precisará excluir manualmente o arquivo para recuperar o espaço.