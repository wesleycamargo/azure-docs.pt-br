---
title: Diferenças de armazenamento do Azure pilha e considerações | Microsoft Docs
description: Entenda as diferenças entre o armazenamento de pilha do Azure e armazenamento do Azure, junto com as considerações de implantação de pilha do Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviwer: xiaofmao
ms.openlocfilehash: 2a6cb3f1a1f8009af411ba4d97a23194f6f089ae
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604453"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Armazenamento do Azure de pilha: diferenças e considerações

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Armazenamento do Azure pilha é o conjunto de serviços de nuvem de armazenamento na pilha do Microsoft Azure. Armazenamento de pilha do Azure fornece o blob, tabela, fila e funcionalidade de gerenciamento de conta com a semântica consistente com o Azure.

Este artigo resume as diferenças de armazenamento do Azure pilha conhecidas dos serviços de armazenamento do Azure. Ela também lista de coisas a considerar ao implantar o Azure pilha. Para saber mais sobre as diferenças de alto nível entre global do Azure e a pilha do Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.

## <a name="cheat-sheet-storage-differences"></a>Roteiro: diferenças de armazenamento

| Recurso | Azure (global) | Azure Stack |
| --- | --- | --- |
|Armazenamento de arquivos|Compartilhamentos de arquivos SMB baseado em nuvem com suporte|Ainda não tem suporte
|Criptografia do serviço de armazenamento do Azure para dados em repouso|criptografia AES de 256 bits|Criptografia de AES de 128 bits do BitLocker
|Tipo de conta de armazenamento|Contas de armazenamento de blob do Azure e de uso geral|Para fins gerais apenas.
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
|Instantâneo de blob|O número máximo de instantâneos de um blob não é limitado.|O número máximo de instantâneos de um blob é 1.000.|

Também há diferenças com as métricas de armazenamento:

* Os dados de transações em métricas de armazenamento não diferenciam largura de banda de rede internos ou externos.
* Os dados de transações em métricas de armazenamento não tem acesso de máquina virtual para os discos montados.

## <a name="api-version"></a>Versão da API

As seguintes versões têm suporte com o armazenamento do Azure pilha:

APIs de serviços de armazenamento do Azure:

Atualizar 1802 ou mais recente:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Versões anteriores:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

APIs de gerenciamento de serviços de armazenamento do Azure:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Versões do SDK

Armazenamento de pilha do Azure suporta as seguintes bibliotecas de cliente:

| Biblioteca do cliente | Versão com suporte a pilha do Azure | Link                                                                                                                                                                                                                                                                                                                                     | Especificação de ponto de extremidade       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | De 6.2.0 para 8.7.0.          | Pacote do NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | arquivo App. config              |
| Java           | De 4.1.0 para 6.1.0           | Pacote de Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Configuração de cadeia de conexão      |
| Node.js        | De 1.1.0 para 2.7.0           | Link NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Por exemplo: executar "instalação de npm azure-storage@2.7.0")<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Declaração da instância de serviço |
| C++            | De 2.4.0 para 3.1.0           | Pacote do NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Configuração de cadeia de conexão      |
| PHP            | De 0.15.0 para 1.0.0          | Versão do GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Instalar por meio do criador (veja os detalhes abaixo)                                                                                                                                                                                                                  | Configuração de cadeia de conexão      |
| Python         | De 0.30.0 para 1.0.0          | Versão do GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Declaração da instância de serviço |
| Ruby           | De 0.12.1 para 1.0.1          | Pacote de RubyGems:<br>Comuns:<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>Fila: https://rubygems.org/gems/azure-storage-queue/<br>Tabela: https://rubygems.org/gems/azure-storage-table/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Configuração de cadeia de conexão      |

## <a name="next-steps"></a>Próximas etapas

* [Introdução às ferramentas de desenvolvimento de armazenamento de pilha do Azure](azure-stack-storage-dev.md)
* [Introdução ao armazenamento de pilha do Azure](azure-stack-storage-overview.md)
