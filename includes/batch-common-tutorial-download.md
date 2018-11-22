---
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a8ea55a40f1ee4681b6aec147e02b7bce6f1d7cf
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279411"
---
### <a name="retrieve-output-files"></a>Recuperar arquivos de saída

Você pode usar o Portal do Azure para baixar os arquivos MP3 de saída gerados pelas tarefas de ffmpeg. 

1. Clique em **Todos os serviços** > **Contas de armazenamento** e, depois, clique no nome da sua conta de armazenamento.
2. Clique em **Blobs** > *saída*.
3. Clique com o botão direito em um dos arquivos MP3 de saída e, em seguida, clique em **Baixar**. Siga as instruções em seu navegador para abrir ou salvar o arquivo.

![Baixar o arquivo de saída](./media/batch-common-tutorial-download/download.png)

Embora não seja mostrado neste exemplo, você também pode baixar os arquivos de forma programática a partir dos nós de computação ou do contêiner de armazenamento.
