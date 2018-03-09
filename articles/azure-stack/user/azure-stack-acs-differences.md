---
title: "Armazenamento do Azure de pilha: As diferenças e considerações"
description: "Entenda as diferenças entre o armazenamento de pilha do Azure e armazenamento do Azure, junto com as considerações de implantação de pilha do Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 7c4f030018f388302c3b60a41086bbd97c86513d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Armazenamento do Azure de pilha: As diferenças e considerações

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Pilha de armazenamento do Azure é o conjunto de serviços de nuvem de armazenamento na pilha do Microsoft Azure. Pilha de armazenamento do Azure fornece o blob, tabela, fila e funcionalidade de gerenciamento de conta com a semântica consistente com o Azure.

Este artigo resume as diferenças de armazenamento do Azure pilha conhecidas do armazenamento do Azure. Ele também resume outras considerações para ter em mente quando você implanta a pilha do Azure. Para saber mais sobre as diferenças de alto nível entre a pilha do Azure e o Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.

## <a name="cheat-sheet-storage-differences"></a>Roteiro: diferenças de armazenamento

| Recurso | Azure (global) | Azure Stack |
| --- | --- | --- |
|Armazenamento de arquivos|Compartilhamentos de arquivos SMB baseado em nuvem com suporte|Ainda não tem suporte
|Criptografia do Serviço de Armazenamento do Azure para dados em repouso|criptografia AES de 256 bits|Criptografia de AES de 128 bits do BitLocker
|Tipo de conta de armazenamento|Contas de armazenamento de Blob do Azure e de uso gerais|Para fins gerais apenas.
|Opções de replicação|Armazenamento com redundância local, armazenamento com redundância geográfica, armazenamento com redundância geográfica com acesso de leitura e armazenamento com redundância de zona|Armazenamento redundante localmente.
|Armazenamento Premium|Com suporte total|Pode ser provisionado, mas nenhum limite de desempenho ou garantia.
|Discos gerenciados|Premium e standard com suporte|Ainda não tem suporte.
|Nome de blob|1.024 caracteres (2.048 bytes)|880 caracteres (1,760 bytes)
|Tamanho máximo do blob de bloco|4,75 TB (100 MB X 50.000 blocos)|4,75 TB (100 MB x 50.000 blocos) para a atualização de 1802 ou versão mais recente. 50.000 x 4 MB (aproximadamente 195 GB) para versões anteriores.
|Cópia do instantâneo de blob de página|Backup do Azure não gerenciado VM discos anexados a uma VM em execução com suporte|Ainda não tem suporte.
|Cópia de instantâneo incremental do blob de página|Premium e blobs de página do Azure padrão com suporte|Ainda não tem suporte.
|Camadas de armazenamento para armazenamento de blob|Quente, legal e níveis de arquivamento.|Ainda não tem suporte.
Exclusão reversível para armazenamento de blob|Visualização|Ainda não tem suporte.
|Tamanho máximo do blob de página|8 TB|1 TB
|Tamanho de página de blob de página|512 bytes|4 KB
|Tamanho de chave de linha e chave de partição de tabela|1.024 caracteres (2.048 bytes)|400 caracteres (800 bytes)

### <a name="metrics"></a>Métricas
Também há algumas diferenças com as métricas de armazenamento:
* Os dados de transações em métricas de armazenamento não diferenciam largura de banda de rede internos ou externos.
* Os dados de transações em métricas de armazenamento não tem acesso de máquina virtual para os discos montados.

## <a name="api-version"></a>Versão da API
As seguintes versões têm suporte com o armazenamento do Azure pilha:

APIs de serviços de armazenamento do Azure:

Atualizar 1802 ou mais recente:
 - [2017-04-17](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08 ](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)

Versões anteriores:
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)


APIs de gerenciamento de serviços de armazenamento do Azure:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Versões do SDK

As bibliotecas de cliente a seguir têm suporte com o armazenamento do Azure pilha:

| Biblioteca do cliente | Versão com suporte a pilha do Azure | Link                                                                                                                                                                                                                                                                                                                                     | Especificação de ponto de extremidade       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | De 6.2.0 para 8.7.0.          | Pacote do NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | arquivo App. config              |
| Java           | De 4.1.0 para 6.1.0           | Pacote de Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Configuração de cadeia de conexão      |
| Node.js        | De 1.1.0 para 2.7.0           | Link NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Por exemplo: executar "instalação de npm azure-storage@2.7.0")<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Declaração da instância de serviço |
| C++            | De 2.4.0 para 3.1.0           | Pacote do NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Configuração de cadeia de conexão      |
| PHP            | De 0.15.0 para 1.0.0          | Versão do GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Instalar por meio do criador (veja os detalhes abaixo)                                                                                                                                                                                                                  | Configuração de cadeia de conexão      |
| Python         | De 0.30.0 para 1.0.0          | Versão do GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Declaração da instância de serviço |
| Ruby           | De 0.12.1 para 1.0.1          | Pacote de RubyGems:<br>Comuns:<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>Queue: https://rubygems.org/gems/azure-storage-queue/<br>Table: https://rubygems.org/gems/azure-storage-table/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Configuração de cadeia de conexão      |

## <a name="next-steps"></a>Próximas etapas

* [Introdução às ferramentas de desenvolvimento de armazenamento de pilha do Azure](azure-stack-storage-dev.md)
* [Introdução ao armazenamento de pilha do Azure](azure-stack-storage-overview.md)

