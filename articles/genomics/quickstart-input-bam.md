---
title: "Guia de início rápido: Enviar um fluxo de trabalho usando uma entrada de arquivo BAM | Microsoft Docs"
titleSuffix: Azure
description: "O guia de início rápido pressupõe que você tem o cliente msgen instalado e executou com êxito os dados de exemplo por meio do serviço."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: 9887121593cad4931c86b55012f1c22686adf25f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Enviar um fluxo de trabalho usando uma entrada de arquivo do BAM

Este guia de início rápido demonstra como enviar um fluxo de trabalho para o serviço Microsoft Genomics se seu arquivo de entrada é um único arquivo BAM. Este tópico pressupõe que você já instalou e executou o cliente `msgen` e está familiarizado sobre como usar o Armazenamento do Azure. Se você enviou, com êxito, um fluxo de trabalho usando os dados de exemplo fornecidos, está pronto para continuar com este guia de início rápido. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configurar: carregar seu arquivo BAM no armazenamento do Azure
Vamos assumir que você tenha um arquivo BAM, *reads.bam*, e já o carregou na sua conta de armazenamento *myaccount* no Azure como **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**. Você tem a URL da API e sua chave de acesso. Você deseja ter saídas em **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Enviar o trabalho para o cliente `msgen` 


Aqui está o conjunto mínimo de argumentos que você precisará fornecer para o cliente `msgen`; quebras de linha foram adicionadas para maior clareza:

Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Para Unix

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Se você preferir usar um arquivo de configuração, é isto que ele deve conter:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envie o arquivo `config.txt` com esta invocação: `msgen submit -f config.txt`

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você carregou um arquivo BAM no Armazenamento do Azure e enviou um fluxo de trabalho para o serviço do Microsoft Genomics por meio do cliente Python `msgen`. Para obter informações adicionais sobre o envio de fluxo de trabalho e outros comandos que você pode usar com o serviço do Microsoft Genomics, consulte nossas [Perguntas frequentes](frequently-asked-questions-genomics.md). 
