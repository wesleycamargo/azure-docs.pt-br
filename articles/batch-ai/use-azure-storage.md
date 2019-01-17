---
title: Armazenando a IA do lote de trabalho de entrada e saída com o armazenamento do Azure | Microsoft Docs
description: Como usar o Armazenamento do Microsoft Azure com a IA do Lote para armazenamento em nuvem rápido e fácil dos arquivos de entrada e saída
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: c1ebcae04b51fbf18da22872e3e4160a37a7e2fe
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201152"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Entrada e saída do trabalho da IA do Lote com Armazenamento do Microsoft Azure

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Este guia descreve como usar o Armazenamento do Microsoft Azure para armazenar arquivos de entrada e saídas ao executar um trabalho. O Armazenamento do Microsoft Azure é uma das várias opções de armazenamento compatíveis com IA do lote. A IA do lote se integra com o Armazenamento do Microsoft Azure ao montar sistemas de armazenamento do Azure para uma IA do lote cluster ou trabalho de sistema de arquivos, permitindo o acesso contínuo aos arquivos armazenados na nuvem. 

## <a name="introduction-to-azure-storage"></a>Introdução ao Armazenamento do Azure

O Armazenamento do Azure é uma solução de armazenamento em nuvem. A IA do lote é compatível com a montagem os contêineres de Blob do Azure e compartilhamentos de arquivos do Azure para trabalhos de IA do lote ou clusters, permitindo que os arquivos sejam acessados de um trabalho como se estivessem no sistema de arquivos nativo. A IA do lote monta os contêineres de BLOBs do Azure com [blobfuse](https://github.com/Azure/azure-storage-fuse)e compartilhamentos de arquivo do Azure por meio do protocolo SMB. Para obter mais informações sobre o armazenamento do Azure, consulte [Introdução ao Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Armazenamento de conjunto de dados e scripts de entrada no Armazenamento do Microsoft Azure

Quando você escolhe o Armazenamento do Microsoft Azure para seu ambiente de IA do lote, é recomendável que você armazene seus arquivos de entrada (como conjuntos de dados) em um contêiner de Blob, que tem a maior taxa de transferência, e armazenar a saída de treinamento em um compartilhamento de arquivo, que oferece suporte a streaming (permitindo a leitura dos logs de saída enquanto o trabalho está em execução simultânea). 

Antes de usar o armazenamento do Azure, você deve [criar uma conta de Armazenamento do Microsoft Azure](../storage/common/storage-quickstart-create-account.md). A IA do lote é compatível com volumes de montagem de contas de Armazenamento do Microsoft Azure de uso geral v1 (GPv1) e de uso geral v2 (GPv2). A conta de armazenamento do Armazenamento do Microsoft Azure pode manter vários contêineres de Blob ou instâncias de compartilhamento de arquivos. Considere seus requisitos de desempenho e custo ao escolher o tipo de conta de armazenamento para criar. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md). 

Para criar um contêiner de Blob e carregar o conjunto de dados para um contêiner de BLOBs do Azure, escolha um dos seguintes métodos:
- [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) para carregar com uma GUI baseada na web. Para carregar um pequeno número de arquivos, o portal do Azure fornece a operação mais simples.
- [CLI de Armazenamento do Microsoft Azure](../storage/blobs/storage-quickstart-blobs-cli.md) para carregar por meio da linha de comando (dá suporte ao carregamento de diretório). Para carregar os diretórios dos arquivos, use `az storage blob upload-batch`.
- [outras técnicas](../storage/common/storage-moving-data.md), incluindo o uso de SDKs do aplicativo.

Da mesma forma, para criar um compartilhamento de arquivos do Azure, escolha um dos seguintes métodos:
- [Portal do Azure](../storage/files/storage-how-to-use-files-portal.md)
- [CLI de Armazenamento do Microsoft Azure](../storage/files/storage-how-to-use-files-cli.md)
- [outras técnicas](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Armazenamento automático com IA do Lote do Azure

Como alternativa, você pode criar uma conta de Armazenamento do Microsoft Azure com um compartilhamento de arquivos do Azure e o contêiner de Blob (e automaticamente montar esses volumes para um cluster de IA do lote) usando o `--use-auto-storage` parâmetro com `az batchai cluster create`. Para saber mais, clique [aqui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Armazenamento do Microsoft Azure 

### <a name="mount-volumes-to-a-job"></a>Montar volumes a um trabalho

Montar um volume de Armazenamento do Microsoft Azure permite que ele seja acessada por meio do sistema de arquivos criado para cada trabalho. Portanto, um trabalho pode ler e gravar arquivos diretamente para o armazenamento em nuvem como se fossem arquivos locais.

Para montar um volume de Armazenamento do Microsoft Azure para um trabalho criado com a CLI do Azure, use a `mountVolumes` propriedade em seu `job.json` durante a execução do arquivo `az batchai job create`. Por exemplo, consulte o [Recibo Distribuído GPU do TensorFlow](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json). O esquema para `mountVolumes` é:
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` e `azureBlobFileSystems` ambas as matrizes de objetos que representam os volumes de montagem. Descrições dos espaços reservados:

- < RELATIVE_MOUNT_PATH > - o volume será montado nesse caminho. Por exemplo, se `relativeMountPath` for `jobs`, o volume estará localizado em `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`)
- <NOME_DA_CON<STORAGE_ACCOUNT_NAME> - o nome da conta de Armazenamento do Microsoft Azure que contém o compartilhamento de arquivos ou um contêiner de Blob
- < FILE_SHARE_NAME > - o nome do compartilhamento de arquivo
- <BLOB_CONTAINER_NAME> - O nome do contêiner de blob

Para montar volumes de Armazenamento do Microsoft Azure com SDKs de IA do Lote do Azure, defina a propriedade `mount_volumes` (Python) ou `MountVolumes` (C#, Java) `JobCreateParameters`. Forneça as credenciais da conta de armazenamento ao montar volumes com SDKs de IA do Lote do Azure. Exibir os esquemas para montar volumes [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet), e [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai.mountvolumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>Montar volumes a um cluster

A IA do lote também é compatível com volumes de Armazenamento do Microsoft Azure para um cluster de IA do lote de montagem. Quando um volume está montado em um cluster, todos os trabalhos em execução no cluster podem usar volumes montados para aquele cluster. Enquanto a montagem de nível de trabalho fornece mais flexibilidade (permitindo que cada trabalho para ter diferentes volumes montados), a montagem de nível de cluster pode ser suficiente em cenários simples.

Para montar um volume de Armazenamento do Microsoft Azure para um trabalho criado com a CLI do Azure, use a `mountVolumes` propriedade em seu `cluster.json` durante a execução do arquivo `az batchai cluster create`. O esquema para `mountVolumes` quando montar um cluster é igual ao montar a um trabalho. 

Da mesma forma, você pode usar a propriedade `mount_volumes` (Python) ou `MountVolumes` (C#, Java) `ClusterCreateParameters` ao montar com SDKs de IA do Lote do Azure. 

## <a name="access-mounted-filesystem-in-a-job"></a>Acesso do sistema de arquivo montado em um trabalho

### <a name="azbatchaijobmountroot-environment-variable"></a>Variável de ambiente $AZ_BATCHAI_JOB_MOUNT_ROOT

O diretório que contém os sistemas de armazenamento montado dentro do ambiente de execução do trabalho, poderá ser acessado com a `$AZ_BATCHAI_JOB_MOUNT_ROOT` variável de ambiente (se você usar a montagem de nível de trabalho). Se você usar a montagem de nível de cluster, essa variável de ambiente será `$AZ_BATCHAI_MOUNT_ROOT`. Os exemplos a seguir pressupõem que você use a montagem de nível de trabalho.

Para fornecer o caminho de dados em um volume montado, use a variável de ambiente `$AZ_BATCHAI_JOB_MOUNT_ROOT` junto com o caminho montado. Por exemplo, se o script de treinamento `train.py` foi carregado para um arquivo do Azure compartilhamento montado no caminho da montagem relativo `scripts`, o arquivo estará disponível em `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Se seu script de treinamento requer conhecimento de um caminho, você deve passá-lo como um argumento de linha de comando. Por exemplo, se você armazenou seus dados em uma pasta chamada `train_data` em um contêiner de BLOBs do Azure montado no caminho `data`, você poderia passar `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` como um argumento de linha de comando para o seu script. Da mesma forma, você deve modificar o script para aceitar argumentos de linha de comando.

### <a name="abbreviate-input-paths"></a>Abreviar caminhos de entrada

Para abreviar caminhos de entrada como uma variável de ambiente, use a `inputDirectories` propriedade do seu `job.json` arquivo (ou `models.JobCreateParameters.input_directories` se estiver usando o SDK do IA do Lote do Azure). O esquema para `inputDirectories` é:

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Cada caminho especificado será colocado em uma variável de ambiente chamada `$AZ_BATCHAI_INPUT_<ID>`. Usar esse método pode simplificar os caminhos para arquivos ou diretórios de entrada. Por exemplo, para abreviar o caminho para um script de treinamento: se `"id"` for `"SCRIPT"` e `"path"` for `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, em seguida, esse caminho está disponível em `$AZ_BATCHAI_INPUT_SCRIPT` no ambiente de execução do trabalho.

Para saber mais, clique [aqui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Abreviar caminhos de saída

Para abreviar caminhos de saída como uma variável de ambiente, use a `outputDirectories` propriedade do seu `job.json` arquivo (ou `models.JobCreateParameters.output_directories` se estiver usando o SDK do IA do Lote do Azure). Usar esse método pode simplificar os caminhos para arquivos ou diretórios de entrada. O esquema para `outputDirectories` é:

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Cada caminho especificado será colocado em uma variável de ambiente chamada `$AZ_BATCHAI_OUTPUT_<ID>`. O `pathPrefix` determina o volume montado para armazenar a saída (por exemplo, `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`). O `pathSuffix` determina o nome da pasta de saída (por exemplo, `"logs"`, `"checkpoints"`). O caminho real da saída é uma concatenação das `pathPrefix`, `jobOutputDirectoryPathSegment` (gerado automaticamente para cada trabalho) e `pathSuffix`.

Para saber mais, clique [aqui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Recuperar a saída do trabalho de Armazenamento do Microsoft Azure

### <a name="use-azure-portal"></a>Usar o portal do Azure

O portal do Azure é uma maneira conveniente de exibir a saída de trabalhos usando o Explorador de arquivos uma GUI. No entanto, se você quiser exibir a saída de Stdout e Stderr ou de um caminho na `outputDirectories`, os arquivos são colocados em um caminho gerado automaticamente no seu volume de Armazenamento do Microsoft Azure. Consulte abaixo para obter mais informações.

### <a name="access-stdout-and-stderr-output"></a>Saída stderr e stdout de lista

Use a `stdOutErrPathPrefix` propriedade `job.json` para informar o trabalho onde colocar os logs de execução e a saída Stdout e Stderr do trabalho. Por exemplo, se você tiver montado um compartilhamento de arquivos no caminho da montagem relativo `outputs`, e você especifique o `stdOutErrPathPrefix` para ser `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`, em seguida, a saída Stdout e Stderr do trabalho estará disponível em `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` nesse volume montado. Esse caminho gerado automaticamente é usado para evitar colisões de saída entre os trabalhos de mesmo nome.

Para saber mais, clique [aqui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>Relacione os arquivos de saída

Você pode usar a CLI do Azure para listar os arquivos de saída disponível de um trabalho com o comando `az batchai job file list`. Por exemplo, para listar os arquivos no diretório de saída padrão de um trabalho, use `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

Para obter mais informações e exemplos, consulte [aqui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Arquivos de saída de fluxo

Você pode usar a CLI do Azure para arquivos de fluxo com o comando `az batchai job file stream`. Por exemplo, para visualizar:
- STDOUT: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

Para obter mais informações e exemplos, consulte [aqui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre os comandos da CLI para interagir com o Armazenamento do Microsoft Azure, veja a [documentação da CLI da IA do Lote do Azure](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Para obter mais exemplos de uso da IA Azure, incluindo o armazenamento de montar e ler arquivos de saída, consulte as [receitas de anotações do Jupyter Notebook de IA do Lote do Azure](https://github.com/Azure/BatchAI).
- Explore outras opções para montar o armazenamento, incluindo [montagem de um servidor NFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) e [montar seu próprio cluster NFS, cifs ou GlusterFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)