---
title: Copiar arquivos de vários contêineres com o Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para copiar arquivos de vários contêineres com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966279"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiar arquivos de vários contêineres com o Azure Data Factory

O modelo de solução descrito neste artigo ajuda a copiar arquivos de vários arquivos, contêineres ou buckets entre os armazenamentos de arquivos. Por exemplo, talvez você queira migrar o Data Lake do AWS S3 para Azure Data Lake Store. Ou, talvez você queira replicar tudo de uma conta do Armazenamento de Blobs do Azure para outra conta do Armazenamento de Blobs do Azure. Esse modelo foi projetado para esses casos de uso.

Se você quiser copiar arquivos de um único contêiner ou bucket, é mais eficiente usar a **Ferramenta Copiar Dados** para criar um pipeline com uma única atividade Copiar. Esse modelo é o mais adequado para esse caso de uso simples.

## <a name="about-this-solution-template"></a>Sobre esse modelo de solução

Esse modelo enumera os contêineres do repositório de armazenamento de origem e, em seguida, copia cada um dos contêineres do repositório de armazenamento de origem para o repositório de destino. 

O modelo contém três atividades:
-   Uma atividade **GetMetadata** para examinar o repositório de armazenamento de origem e obter a lista de contêineres.
-   Uma atividade **ForEach** para obter a lista de contêineres da atividade **GetMetadata** e, em seguida, iterar sobre a lista e passar cada contêiner para a atividade Copiar.
-   Uma atividade **Copiar** para copiar cada contêiner do repositório de armazenamento de origem para o repositório de destino.

O modelo define dois parâmetros:
-   O parâmetro *SourceFilePath* é o caminho do repositório da fonte de dados, onde você poderá obter uma lista dos contêineres ou buckets. Na maioria dos casos, o caminho é o diretório raiz, que contém várias pastas de contêiner. O valor padrão desse parâmetro é `/`.
-   O parâmetro *DestinationFilePath* é o caminho no qual os arquivos serão copiados no repositório de destino. O valor padrão desse parâmetro é `/`.

## <a name="how-to-use-this-solution-template"></a>Como usar esse modelo de solução

1. Vá para o modelo **Copiar vários contêineres de arquivos entre os Repositórios de Arquivos** e crie uma **nova conexão** para o repositório de armazenamento de origem. O repositório de armazenamento de origem é o local onde você quer copiar arquivos de vários contêineres ou buckets.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Crie uma **nova conexão** no repositório de destino.

    ![Criar uma nova conexão para o destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Clique em **Usar este modelo**.

    ![Usar esse modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Você verá o pipeline disponível no painel, conforme mostrado no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Clique em **Depurar**, insira **Parâmetros** e clique em **Concluir**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Revise o resultado.

    ![Revisar o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
