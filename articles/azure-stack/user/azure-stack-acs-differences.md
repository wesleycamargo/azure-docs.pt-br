---
title: Diferenças do armazenamento do Azure stack e considerações | Microsoft Docs
description: Entenda as diferenças entre o armazenamento do Azure stack e o armazenamento do Azure, juntamente com as considerações de implantação do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.openlocfilehash: 1393dd32aea8cb6d348092ea1fc56752f659beab
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717855"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Armazenamento do Azure Stack: Diferenças e considerações

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O armazenamento do Azure Stack é o conjunto de serviços de nuvem de armazenamento no Microsoft Azure Stack. O armazenamento do Azure Stack fornece blob, tabela, fila e funcionalidade de gerenciamento de conta com a semântica consistente do Azure.

Este artigo resume as diferenças do armazenamento do Azure Stack conhecidas dos serviços de armazenamento do Azure. Ela também lista as coisas a considerar ao implantar o Azure Stack. Para saber mais sobre as diferenças de alto nível entre global do Azure e o Azure Stack, consulte o [considerações da chave](azure-stack-considerations.md) artigo.

## <a name="cheat-sheet-storage-differences"></a>Folha de dados: Diferenças do armazenamento

| Recurso | (Global) do Azure | Azure Stack |
| --- | --- | --- |
|Armazenamento de arquivos|Compartilhamentos de arquivos SMB baseado em nuvem com suporte|Ainda não tem suporte
|Criptografia do serviço de armazenamento do Azure para dados em repouso|criptografia AES de 256 bits. Dá suporte à criptografia usando chaves gerenciadas pelo cliente no cofre de chaves.|Criptografia de AES de 128 bits do BitLocker. Não há suporte para criptografia usando chaves gerenciadas pelo cliente.
|Tipo de conta de armazenamento|Contas de armazenamento de BLOBs, V1 e V2 para fins gerais|Uso geral V1 apenas.
|Opções de replicação|Armazenamento com redundância local, armazenamento com redundância geográfica, armazenamento com redundância geográfica de acesso de leitura e armazenamento com redundância de zona|Armazenamento com redundância local.
|Armazenamento Premium|Com suporte total|Pode ser provisionado, mas nenhum limite de desempenho ou garantia.
|Discos gerenciados|Premium e standard com suporte|Suporte quando você usa a versão 1808 ou posterior.
|Nome de blob|1.024 caracteres (2.048 bytes)|880 caracteres (1,760 bytes)
|Tamanho máximo do blob de blocos|4,75 TB (100 MB X 50.000 blocos)|4,75 TB (100 MB x 50.000 blocos) para a atualização 1802 ou a versão mais recente. 50.000 x 4 MB (aproximadamente 195 GB) para versões anteriores.
|Cópia de instantâneo de blob de página|Backup do Azure não gerenciado VM discos anexados a uma VM em execução com suporte|Ainda não tem suporte.
|Cópia de instantâneo incremental de blob de página|Premium e blobs de página padrão do Azure com suporte|Ainda não tem suporte.
|Camadas de armazenamento para o armazenamento de BLOBs|Frequente, esporádica ou de camadas de armazenamento de arquivos.|Ainda não tem suporte.
|Exclusão reversível para o armazenamento de BLOBs|Geral disponível|Ainda não tem suporte.
|Tamanho máximo do blob de página|8 TB|1 TB
|Tamanho de página de blob de página|512 bytes|4 KB
|Tamanho de chave de linha e chave de partição de tabela|1.024 caracteres (2.048 bytes)|400 caracteres (800 bytes)
|Instantâneo de blob|O número máximo de instantâneos de um blob não é limitado.|O número máximo de instantâneos de um blob é 1.000.
|Autenticação do Azure AD para o armazenamento|Na visualização|Ainda não tem suporte.
|Blobs imutáveis|Geral disponível|Ainda não tem suporte.
|Regras de firewall e rede virtual para o armazenamento|Geral disponível|Ainda não tem suporte.|

Também há diferenças com métricas de armazenamento:

* Os dados de transação nas métricas de armazenamento não diferenciam largura de banda de rede interno ou externo.
* Os dados de transação nas métricas de armazenamento incluem o acesso de máquina virtual para os discos montados.

## <a name="api-version"></a>Versão da API

As seguintes versões têm suporte com o armazenamento do Azure Stack:

APIs de serviços de armazenamento do Azure:

1811 ou versões mais recentes de atualização:

 - [11-2017-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
 - [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

atualização do 1802 1809 Update:

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

Versões anteriores:

 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 
## <a name="sdk-versions"></a>Versões do SDK

O armazenamento do Azure Stack oferece suporte as bibliotecas de cliente a seguir:

| Biblioteca do cliente | Versão com suporte do Azure Stack | Link                                                                                                                                                                                                                                                                                                                                     | Especificação de ponto de extremidade       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | De 6.2.0 para 8.7.0.          | Pacote do NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | arquivo App. config              |
| Java           | De 4.1.0 para 6.1.0           | Pacote do Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Configuração de cadeia de conexão      |
| Node.js        | Da versão 1.1.0 para 2.7.0           | Link do NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Por exemplo: execute "npm instalar azure-storage@2.7.0")<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Declaração de instância de serviço |
| C++            | De 2.4.0 para 3.1.0           | Pacote do NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Configuração de cadeia de conexão      |
| PHP            | De 0.15.0 para 1.0.0          | Versão do GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Instalar por meio do compositor (veja os detalhes abaixo)                                                                                                                                                                                                                  | Configuração de cadeia de conexão      |
| Python         | De 0.30.0 para 1.0.0          | Versão do GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Declaração de instância de serviço |
| Ruby           | De 0.12.1 para 1.0.1          | Pacote do RubyGems:<br>Comuns:<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>Fila: https://rubygems.org/gems/azure-storage-queue/<br>Tabela: https://rubygems.org/gems/azure-storage-table/<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Configuração de cadeia de conexão      |

## <a name="next-steps"></a>Próximas etapas

* [Introdução às ferramentas de desenvolvimento do armazenamento do Azure Stack](azure-stack-storage-dev.md)
* [Introdução ao armazenamento do Azure Stack](azure-stack-storage-overview.md)
