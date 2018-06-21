---
title: Migrar dos Serviços de Mídia do Azure v2 para v3 | Microsoft Docs
description: Este artigo descreve as alterações introduzidas nos Serviços de Mídia do Azure v3 e mostra as diferenças entre as duas versões.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/22/2018
ms.author: juliako
ms.openlocfilehash: 4e644db12a74d6ef132a0c8d64ef517a0c2253cc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658860"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrar dos Serviços de Mídia v2 para v3

> [!NOTE]
> A versão mais recente do Azure Media Services está em versão prévia e pode ser chamada de v3.

Este artigo descreve as alterações introduzidas no AMS (Serviços de Mídia do Azure) v3 e mostra as diferenças entre as duas versões.

## <a name="why-should-you-move-to-v3"></a>Por que você deveria migrar para a v3?

### <a name="api-is-more-approachable"></a>A API é mais acessível

*  A v3 é baseada em uma superfície de API unificada que expõe a funcionalidade de operações e gerenciamento compilada no Azure Resource Manager. Os modelos do Azure Resource Manager podem ser usados para criar e implantar Transformações, Pontos de Extremidade de Streaming, LiveEvents e muito mais.
* Documento de Especificação Open API (aka Swagger).
* SDKs disponíveis para .Net, .Net Core, Node.js, Python, Java e Ruby.
* Integração da CLI do Azure.

### <a name="new-features"></a>Novos recursos

* A codificação agora dá suporte para ingestão HTTPS (entrada baseada em URL).
* As transformações são novas na v3. Uma Transformação é usada para compartilhar configurações, criar modelos do Azure Resource Manager e isolar configurações de codificação para um cliente ou locatário específico. 
* Um Ativo pode ter vários StreamingLocators, cada um com diferentes configurações de Empacotamento Dinâmico e Criptografia Dinâmica.
* A proteção de conteúdo dá suporte para recursos de várias chaves. 
* A Versão Prévia do LiveEvent dá suporte para Empacotamento Dinâmico e Criptografia Dinâmica. Isso permite proteção de conteúdo na Versão Prévia, bem como empacotamento HLS e DASH.
* O LiveOuput é mais simples de usar do que a entidade mais antiga do Programa. 
* O suporte RBAC em entidades foi adicionado.

## <a name="changes-from-v2"></a>Alterações da v2

* Os SDKs dos Serviços de Mídia são desacoplados do SDK de Armazenamento, o que proporciona mais controle sobre o SDK de Armazenamento usado e evita problemas de versão. 
* Na v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições do Media Encoder Standard do REST v2. Por exemplo, a taxa de bits na v2 seria especificada como 128, mas em v3 seria 128000. 
* AssetFiles, AccessPolicies, IngestManifests não existem na v3.
* ContentKeys não são mais uma entidade, propriedade do StreamingLocator.
* O suporte para Grade de Eventos substitui o NotificationEndpoints.
* Algumas entidades foram renomeadas

  * JobOutput substitui Tarefa, agora apenas parte do Trabalho.
  * LiveEvent substitui o Canal.
  * LiveOutput substitui o Programa.
  * StreamingLocator substitui o Localizador.

## <a name="code-changes"></a>Alterações de código

### <a name="create-an-asset-and-upload-a-file"></a>Criar um ativo e enviar um arquivo 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Enviar um trabalho

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Publicar um ativo com criptografia AES 

#### <a name="v2"></a>v2

1. Criar ContentKeyAuthorizationPolicyOption
2. Criar ContentKeyAuthorizationPolicy
3. Criar AssetDeliveryPolicy
4. Criar Ativo e carregar conteúdo OU Enviar trabalho e usar ativo de saída
5. Associar AssetDeliveryPolicy com Ativo
6. Criar ContentKey
7. Anexar ContentKey ao Ativo
8. Criar AccessPolicy
9. Criar Localizador

#### <a name="v3"></a>v3

1. Criar Política de Chave de Conteúdo
2. Criar Ativo
3. Carregar conteúdo ou usar Ativo como JobOutput
4. Criar Localizador

## <a name="next-steps"></a>Próximas etapas

Para ver como é fácil começar a codificar e transmitir arquivos de vídeo, confira [Arquivos de transmissão](stream-files-dotnet-quickstart.md). 

