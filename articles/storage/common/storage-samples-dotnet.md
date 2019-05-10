---
title: Exemplos do Armazenamento do Azure usando .NET | Microsoft Docs
description: Exiba, baixe e execute exemplos de código e aplicativos para o Armazenamento do Azure. Descubra exemplos de introdução a blobs, filas, tabelas e arquivos usando as bibliotecas do cliente de armazenamento do .NET.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/03/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: df7c14f1ee83015303657f9a0babde3d60c92292
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209703"
---
# <a name="azure-storage-samples-using-net"></a>Exemplos de Armazenamento do Azure usando .NET

## <a name="net-sample-index"></a>Exemplo de índice do .NET

A tabela a seguir fornece uma visão geral do nosso repositório de exemplos e os cenários abordados em cada exemplo. Clique nos links para exibir o código de exemplo correspondente no GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Ponto de extremidade</th><th style="font-size:110%">Cenário</th><th style="font-size:110%">Código de Exemplo</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Acrescentar Blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Introdução aos blobs</a></td> 
</tr> 
<tr> 
<td>Bloquear Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web de Galeria de Fotos do Armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Criptografia do cliente</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Exemplos de criptografia de blob</a></td>
</tr> 
<tr> 
<td>Copiar blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução aos blobs</a></td>
</tr> 
<tr> 
<td>Create Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web de Galeria de Fotos do Armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web de Galeria de Fotos do Armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Delete Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução aos blobs</a></td>
</tr> 
<tr> 
<td>Metadados/propriedades/estatísticas de blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução aos blobs</a></td>
</tr> 
<tr> 
<td>ACL/metadados/propriedades de contêiner</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplicativo Web de Galeria de Fotos do Armazenamento de Blobs do Azure</a></td>
</tr> 
<tr> 
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução aos blobs</a></td>
</tr> 
<tr> 
<td>Blob/contêiner de concessão</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução aos blobs</a></td>
</tr> 
<tr> 
<td>Blob/contêiner de lista</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introdução aos blobs</a></td>
</tr> 
<tr> 
<td>Blob de Páginas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introdução aos blobs</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução aos blobs</a></td>
</tr>   
<tr> 
<td>Propriedades do serviço</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introdução aos blobs</a></td>
</tr>           
<tr> 
<td>Blob de instantâneo</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Fazer backup de discos de máquina virtual do Azure com instantâneos incrementais</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Arquivo</b></td>
<td>Criar compartilhamentos/diretórios/arquivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Excluir compartilhamentos/diretórios/arquivos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Introdução ao Serviço de Arquivos do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Propriedades/metadados de diretório</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Baixar arquivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Metadados/propriedades/métricas de arquivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço de arquivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Listar diretórios e arquivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Listar compartilhamentos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr>
<tr> 
<td>Compartilhar metadados/propriedades/estatísticas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemplo de Armazenamento de Arquivos do .NET para Armazenamento do Azure</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Fila</b></td>
<td>Adicionar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao Serviço Fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Criptografia do cliente</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Criptografia de fila do lado do cliente do .NET para Armazenamento do Azure</a></td> 
</tr> 
<tr> 
<td>Criar filas</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao Serviço Fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Excluir mensagem/fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao Serviço Fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Espiar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao Serviço Fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>ACL/metadados/estatísticas de fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Introdução ao Serviço Fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço de fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Introdução ao Serviço Fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Atualização de mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Introdução ao Serviço Fila do Azure no .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Criar tabela</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerenciando a simultaneidade usando o Armazenamento do Azure — aplicativo de amostra</a></td> 
</tr> 
<tr> 
<td>Excluir entidade/tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introdução ao Armazenamento de Tabela do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Inserir/mesclar/substituir entidade</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerenciando a simultaneidade usando o Armazenamento do Azure — aplicativo de amostra</a></td> 
</tr> 
<tr> 
<td>Query Entities</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introdução ao Armazenamento de Tabela do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Consultar tabelas</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introdução ao Armazenamento de Tabela do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Propriedades/ACL de tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Introdução ao Armazenamento de Tabela do Azure no .NET</a></td> 
</tr> 
<tr> 
<td>Update Entity</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gerenciando a simultaneidade usando o Armazenamento do Azure — aplicativo de amostra</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteca de Exemplos de Código do Azure

Para visualizar a biblioteca completa de exemplos, vá para a biblioteca [Exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage), que contém exemplos para o Armazenamento do Azure que podem ser baixados e executados localmente. A Biblioteca de Exemplo de Código fornece exemplo de código em formato .zip. Como alternativa, você pode navegar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando por instruções sobre como instalar e começar a usar as Bibliotecas de cliente de armazenamento do Azure.

* [Introdução ao Serviço Blob do Azure no .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Introdução ao Serviço Fila do Azure no .NET](../storage-dotnet-how-to-use-queues.md)
* [Introdução ao Serviço Tabela do Azure no .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introdução ao Serviço de Arquivos do Azure no .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre exemplos para outras linguagens:

* Java: [Exemplos de Armazenamento do Azure usando Java](storage-samples-java.md)
* Todas as outras linguagens: [Exemplos do Armazenamento do Azure](../storage-samples.md)
