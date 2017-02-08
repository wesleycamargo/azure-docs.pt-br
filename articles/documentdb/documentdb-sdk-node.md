---
title: API do Node.js e Recursos do SDK - Azure DocumentDB | Microsoft Docs
description: "Saiba tudo sobre o SDK e a API do Node.js, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Node.js para o Banco de Dados de Documentos."
services: documentdb
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: 43d658a67eb55a2d2e35f79080d63c3effb6387e


---
# <a name="documentdb-apis-and-sdks"></a>SDKs e APIs de Banco de Dados de Documentos
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-nodejs-api-and-sdk"></a>SDK e API do Node.js para o Banco de Dados de Documentos
<table>

<tr><td>**Baixar o SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência da API do Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Instruções de instalação do SDK**</td><td>[Instruções de instalação](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Exemplos**</td><td>[Exemplos de código do Node.js](documentdb-nodejs-samples.md)</td></tr>

<tr><td>**Tutorial de introdução**</td><td>[Introdução ao SDK do Node.js](documentdb-nodejs-get-started.md)</td></tr>

<tr><td>**Tutorial do aplicativo Web**</td><td>[Compilar um aplicativo Web do Node.js usando o DocumentDB](documentdb-nodejs-application.md)</td></tr>

<tr><td>**Plataforma atual com suporte**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name11011101a"></a><a name="1.10.1"/>1.10.1</a>
* Desabilitar verificação de SSL ao usar o SDK visando o emulator(hostname=localhost).
* Adicionado suporte para habilitar o registro em log de script durante a execução do procedimento armazenado.

### <a name="a-name11001100a"></a><a name="1.10.0"/>1.10.0</a>
* Adicionado suporte para várias consultas paralelas de partição.
* Adição de suporte a consultas TOP/ORDER BY de coleções particionadas.

### <a name="a-name190190a"></a><a name="1.9.0"/>1.9.0</a>
* Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações limitadas recebem uma exceção muito grande de taxa de solicitação, código de erro 429.) Por padrão, o Banco de Dados de Documentos tenta cada solicitação novamente nove vezes quando o código de erro 429 é encontrado, respeitando o tempo retryAfter no cabeçalho de resposta. Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições. O Banco de Dados de Documentos agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Este tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.
* O Banco de Dados de Documentos agora retorna x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para denotar a contagem de repetições limitadas e o tempo cumulativo que a solicitação aguardou entre as tentativas.
* A classe RetryOptions foi adicionada, expondo a propriedade RetryOptions na classe ConnectionPolicy, que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name="a-name180180a"></a><a name="1.8.0"/>1.8.0</a>
* Suporte adicionado para contas de banco de dados de várias regiões.

### <a name="a-name170170a"></a><a name="1.7.0"/>1.7.0</a>
* Adicionado o suporte para o recurso TTL (vida útil) para documentos.

### <a name="a-name160160a"></a><a name="1.6.0"/>1.6.0</a>
* Implementação de [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md).

### <a name="a-name156156a"></a><a name="1.5.6"/>1.5.6</a>
* O bug RangePartitionResolver.resolveForRead foi corrigido, pois ele não estava retornando links devido a uma concatenação incorreta dos resultados.

### <a name="a-name155155a"></a><a name="1.5.5"/>1.5.5</a>
* hashPartitionResolver resolveForRead() corrigido: quando nenhuma chave de partição fornecida estava emitindo exceção, em vez de retornar uma lista de todos os links registrados.

### <a name="a-name154154a"></a><a name="1.5.4"/>1.5.4</a>
* Corrige o problema [nº&100;](https://github.com/Azure/azure-documentdb-node/issues/100) — Agente HTTPS Dedicado: evite modificar o agente global para os fins do Banco de Dados de Documentos. Use um agente dedicado para todas as solicitações da biblioteca.

### <a name="a-name153153a"></a><a name="1.5.3"/>1.5.3</a>
* Corrige o problema [nº&81;](https://github.com/Azure/azure-documentdb-node/issues/81) — trate corretamente os traços em IDs de mídia.

### <a name="a-name152152a"></a><a name="1.5.2"/>1.5.2</a>
* Corrige o problema [nº&95;](https://github.com/Azure/azure-documentdb-node/issues/95) — aviso de perda do ouvinte EventEmitter.

### <a name="a-name151151a"></a><a name="1.5.1"/>1.5.1</a>
* Corrige o problema [nº&92;](https://github.com/Azure/azure-documentdb-node/issues/90) — renomeie a pasta Hash para hash nos sistemas que diferenciam maiúsculas de minúsculas.

### <a name="a-name150150a"></a><a name="1.5.0"/>1.5.0</a>
* Implemente o suporte a fragmentação ao adicionar os resolvedores de partição de hash e de intervalo.

### <a name="a-name140140a"></a><a name="1.4.0"/>1.4.0</a>
* Implementar o Upsert. Novos métodos upsertXXX em documentClient.

### <a name="a-name130130a"></a><a name="1.3.0"/>1.3.0</a>
* Ignorado para alinhar os números de versão com outros SDKs.

### <a name="a-name122122a"></a><a name="1.2.2"/>1.2.2</a>
* Slipt Q promete wrapper para o novo repositório.
* Atualização para o arquivo de pacote do registro npm.

### <a name="a-name121121a"></a><a name="1.2.1"/>1.2.1</a>
* Implementa o roteamento com base em ID.
* Corrige o problema [nº&49;](https://github.com/Azure/azure-documentdb-node/issues/49) - a propriedade atual está em conflito com o método atual().

### <a name="a-name120120a"></a><a name="1.2.0"/>1.2.0</a>
* Suporte adicionado para índice geoespaciais.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, &#47;&#47; ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="a-name110110a"></a><a name="1.1.0"/>1.1.0</a>
* Implementa a política de indexação V2.

### <a name="a-name103103a"></a><a name="1.0.3"/>1.0.3</a>
* Problema [nº&40;](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurações eslint e grunt implementadas no núcleo e SDK de promessa.

### <a name="a-name102102a"></a><a name="1.0.2"/>1.0.2</a>
* Problema [nº&45;](https://github.com/Azure/azure-documentdb-node/issues/45) - Promessa de wrapper não inclui o cabeçalho com erro.

### <a name="a-name101101a"></a><a name="1.0.1"/>1.0.1</a>
* Habilidade implementada de consultar conflitos por meio da adição de readConflicts, readConflictAsync e queryConflicts.
* Documentação da API atualizada.
* Problema [nº&41;](https://github.com/Azure/azure-documentdb-node/issues/41) - Erro client.createDocumentAsync.

### <a name="a-name100100a"></a><a name="1.0.0"/>1.0.0</a>
* SDK DO GA.

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Banco de Dados de Documentos usando um SDK obsoleto será rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.10.1](#1.10.1) |22 de dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |3 de outubro de 2016 |--- |
| [1.9.0](#1.9.0) |07 de julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junho de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de março de 2016 |--- |
| [1.5.6](#1.5.6) |08 de março de 2016 |--- |
| [1.5.5](#1.5.5) |02 de fevereiro de 2016 |--- |
| [1.5.4](#1.5.4) |1 de fevereiro de 2016 |--- |
| [1.5.2](#1.5.2) |26 de janeiro de 2016 |--- |
| [1.5.2](#1.5.2) |22 de janeiro de 2016 |--- |
| [1.5.1](#1.5.1) |4 de janeiro de 2016 |--- |
| [1.5.0](#1.5.0) |31 de dezembro de 2015 |--- |
| [1.4.0](#1.4.0) |06 de outubro de 2015 |--- |
| [1.3.0](#1.3.0) |06 de outubro de 2015 |--- |
| [1.2.2](#1.2.2) |10 de setembro de 2015 |--- |
| [1.2.1](#1.2.1) |15 de agosto de 2015 |--- |
| [1.2.0](#1.2.0) |5 de agosto de 2015 |--- |
| [1.1.0](#1.1.0) |9 de julho de 2015 |--- |
| [1.0.3](#1.0.3) |04 de junho de 2015 |--- |
| [1.0.2](#1.0.2) |23 de maio de 2015 |--- |
| [1.0.1](#1.0.1) |15 de maio de 2015 |--- |
| [1.0.0](#1.0.0) |8 de abril de 2015 |--- |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre o Banco de Dados de Documentos, confira a página de serviço do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/) .




<!--HONumber=Jan17_HO4-->


