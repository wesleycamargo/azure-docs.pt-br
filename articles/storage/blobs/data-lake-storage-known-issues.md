---
title: Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre as limitações e problemas conhecidos com o Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: bc6f59922286663435782623a72345c19adb494c
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995024"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs

Este artigo contém problemas conhecidos e limitações temporárias com o Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>Interoperabilidade da API

APIs de armazenamento de B’lob e APIs do Azure Data Lake Gen2 não são interoperáveis.

Se você precisar usar a mesma ferramenta para trabalhar com todo o conteúdo que carregar à sua conta, em seguida, não habilite a namespaces hierárquicos em sua conta de armazenamento de Blob até que essas APIs tornam-se interoperáveis entre si. Usar uma conta de armazenamento sem o namespace hierárquico significa que, em seguida, você não tem acesso a recursos específicos do Azure Data Lake Storage Gen2, como o sistema de arquivos e diretório listas de controle de acesso.

## <a name="blob-storage-apis"></a>APIs de armazenamento de Blob

APIs de armazenamento de Blob ainda não estão disponíveis para contas de armazenamento do Azure Data Lake Storage Gen2.

Essas APIs estão desabilitadas para evitar problemas de acesso acidental de dados que poderiam surgir porque as APIs de armazenamento de Blob ainda não estão interoperáveis com Azure Data Lake Gen2 APIs.

Se você usou essas APIs para carregar dados antes que fossem desabilitados, e você tem um requisito para acessar os dados de produção, em seguida, entre em contato com o Suporte da Microsoft com as seguintes informações:

* ID da assinatura (o GUID, não o nome)

* Nome da conta de armazenamento

* Se você ativamente é afetados em produção e nesse caso, para quais contas de armazenamento?

* Mesmo se você não será afetado ativamente em produção, conte-nos se você precisa dos dados a serem copiados para outra conta de armazenamento por algum motivo e nesse caso, por que?

Nessas circunstâncias, podemos restaurar o acesso à API do Blob por um período de tempo limitado para que você possa copiar esses dados para uma conta de armazenamento que não tenha namespaces hierárquicos habilitados.

Discos não gerenciados de Máquina Virtual (VM) dependem dessas APIs de Armazenamento de Blobs desabilitadas, portanto, se você quiser habilitar namespaces hierárquicos em uma conta de armazenamento, considere colocar os discos não gerenciados de VM em uma conta de armazenamento que não tem namespaces hierárquicos habilitados.

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

Para exibir ou gerenciar contas do Azure Data Lake Storage Gen2 usando o Gerenciador de Armazenamento do Microsoft Azure, você deve ter pelo menos versão `1.6.0` da ferramenta que está disponível como [download gratuito](https://azure.microsoft.com/features/storage-explorer/).

Observe a versão do Gerenciador de Armazenamento que está incorporado no Portal do Azure atualmente não dá suporte para exibir ou gerenciar contas do Azure Data Lake Storage Gen2 com namespaces hierárquicos habilitados.

## <a name="blob-viewing-tool"></a>Ferramenta de visualização de Blob

A ferramenta de visualização no portal do Azure de Blob tem somente suporte limitado para Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Aplicativo de terceiros

Aplicativos de terceiros podem não dar suporte a armazenamento do Azure Data Lake Storage Gen2.

O suporte é o critério de cada provedor de aplicativo de terceiros. Atualmente, as APIs de armazenamento de Blob e Azure Data Lake Storage Gen2 APIs não podem ser usadas para gerenciar o mesmo conteúdo. Estamos trabalhando para habilitar essa interoperabilidade, é possível que muitas ferramentas de terceiros terá suporte automático do Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Suporte do AzCopy

A versão 8 do AzCopy não dá suporte a armazenamento do Azure Data Lake Storage Gen2.

Em vez disso, use a versão de visualização mais recente do AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) que ofereça suporte a pontos de extremidade do armazenamento do Azure Data Lake Storage Gen2.

## <a name="azure-event-grid"></a>Grade de Eventos do Azure

[A Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) não recebe eventos de contas do Azure Data Lake Gen2, porque essas contas ainda não os geraram.  

## <a name="soft-delete-and-snapshots"></a>Instantâneos e exclusão reversível

Instantâneos e a exclusão reversível não estão disponíveis para contas de armazenamento do Azure Data Lake Storage Gen2.

Todos os recursos de controle de versão, incluindo [instantâneos](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) e [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) ainda não estão disponíveis para contas de armazenamento que têm namespaces hierárquicos habilitados.

## <a name="object-level-storage-tiers"></a>Camadas de armazenamento no nível de objeto

As camadas de armazenamento no nível de objeto (quente, frio e arquivo) ainda não estão disponíveis para contas de armazenamento do Azure Data Lake Storage Gen 2, mas eles estão disponíveis para contas de armazenamento que não têm espaços hierárquicos habilitados.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Políticas de gerenciamento (visualização) do ciclo de vida de armazenamento de Blob do Azure

Políticas de gerenciamento (visualização) do ciclo de vida de armazenamento de Blob do Azure ainda não estão disponíveis para contas de armazenamento do Azure Data Lake Storage Gen2.

Essas políticas estão disponíveis para contas de armazenamento que não têm espaços hierárquicos habilitados.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Os logs de diagnóstico não estão disponíveis para contas de armazenamento do Azure Data Lake Storage Gen2.

Para solicitar os logs de diagnóstico, entre em contato com o suporte do Azure. Forneça seu nome de conta e o período de tempo para o qual você precisa de logs.
