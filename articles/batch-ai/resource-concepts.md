---
title: Sobre os recursos da IA do Lote – Azure | Microsoft Docs
description: Visão geral dos workspaces, clusters, servidores de arquivos, experimentos e trabalhos no serviço de IA do Lote no Microsoft Azure.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 4454448809411c6390bc1f65c33b4abe76094ded
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407738"
---
# <a name="overview-of-resources-in-batch-ai"></a>Visão geral dos recursos da IA do Lote

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

É recomendável que, ao começar a usar o serviço de IA do Lote, você compreenda os recursos da IA do Lote que estão disponíveis. Como ocorre com outros serviços do Azure, você cria recursos da IA do Lote no Azure em um ou mais *grupos de recursos*. Crie um ou mais *workspaces* da IA do Lote em um grupo de recursos. Cada workspace contém uma combinação de *clusters*, *servidores de arquivos* e *experimentos* da IA do Lote. Um experimento da IA do Lote encapsula um grupo de *trabalhos*.

A imagem a seguir mostra um exemplo de hierarquia de recursos da IA do Lote. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

As seções a seguir descrevem com mais detalhes os recursos da IA do Lote.

## <a name="workspace"></a>Workspace

Um workspace na IA do Lote é uma coleção de nível superior de todos os tipos de recursos da IA do Lote. Os workspaces ajudam a separar o trabalho pertencente a diferentes grupos ou projetos. Por exemplo, você pode criar um workspace de teste e um de desenvolvimento.

## <a name="cluster"></a>HDInsight

Um cluster da IA do Lote contém os recursos de computação para executar trabalhos. Todos os nós em um cluster têm o mesmo tamanho de VM e a mesma imagem do sistema operacional. A IA do Lote oferece várias opções para a criação de clusters que são personalizados para diferentes necessidades. Normalmente, você configura um cluster diferente para cada categoria de poder de processamento necessária para concluir um projeto. Escale e reduza verticalmente os clusters da IA do Lote com base na demanda e no orçamento. Para obter mais informações, confira [Trabalhar com clusters de IA do Lote](clusters.md).

## <a name="file-server"></a>Servidor de arquivos

Opcionalmente, crie um servidor de arquivos na IA do Lote para armazenar dados, scripts de treinamento e logs de saída. Um servidor de arquivos da IA do Lote é um NFS gerenciado de nó único, que pode ser montado automaticamente em nós de cluster para fornecer um local de armazenamento centralizado de fácil acesso para os trabalhos. Na maioria dos casos, somente um servidor de arquivos é necessário em um workspace e você pode separar os dados de seus trabalhos de treinamento em diretórios diferentes. Se o NFS não for adequado para suas cargas de trabalho, a IA do Lote dará suporte a outras opções de armazenamento, incluindo o [Armazenamento do Azure](use-azure-storage.md) ou soluções personalizadas como um sistema de arquivos Gluster ou Lustre.

## <a name="experiment"></a>Experimento

Um experimento agrupa uma coleção de trabalhos relacionados que você consulta e gerencia simultaneamente. Cada workspace pode ter vários experimentos, em que cada experimento tenta resolver um problema específico.

## <a name="job"></a>Trabalho

Um trabalho é uma única tarefa ou script que precisa ser executado, por exemplo, para treinar um modelo de aprendizado profundo. Cada trabalho executa um script específico em um cluster no workspace. (O script pode ser armazenado em um servidor de arquivos do IA do Lote ou em outra solução de armazenamento). Cada trabalho do IA do Lote tem um tipo de estrutura associado: TensorFlow, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, MPI personalizado ou personalizado. Para cada estrutura, o serviço de IA do Lote configura a infraestrutura necessária e gerencia os processos de trabalho. Cada experimento pode ter vários trabalhos semelhantes, além de algumas alterações para parâmetros diferentes.

## <a name="next-steps"></a>Próximas etapas

* Execute seu primeiro [trabalho de treinamento do IA do Lote](quickstart-tensorflow-training-cli.md).

* Confira as [receitas de treinamento](https://github.com/Azure/BatchAI/tree/master/recipes) de exemplo para estruturas diferentes.