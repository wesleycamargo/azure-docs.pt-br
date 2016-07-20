<properties 
	pageTitle="SDK do Node.js para o Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba tudo sobre o SDK do Node.js, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Node.js para o Banco de Dados de Documentos." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="06/14/2016" 
	ms.author="andrl"/>

# SDK do Banco de Dados de Documentos

> [AZURE.SELECTOR]
- [SDK .NET](documentdb-sdk-dotnet.md)
- [SDK do Node.js](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [SDK do Python](documentdb-sdk-python.md)

##SDK do Node.js para o Banco de Dados de Documentos

<table>
<tr><td>**Baixar**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Colaborar**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Documentação**</td><td>[Documentação de referência do SDK do Node.js](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Exemplos**</td><td>[Exemplos de código do Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**Guia de introdução**</td><td>[Introdução ao SDK do Node.js] (documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Plataforma atual com suporte**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##Notas de versão

###<a name="1.8.0"/>1.8.0</a>

  - Suporte adicionado para contas de banco de dados de várias regiões.

###<a name="1.7.0"/>1.7.0</a>

- Adicionado o suporte para o recurso TTL (vida útil) para documentos.

###<a name="1.6.0"/>1.6.0</a>

- [Coleções particionadas](documentdb-partition-data.md) implementadas e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- O bug RangePartitionResolver.resolveForRead foi corrigido, pois ele não estava retornando links devido a uma concatenação incorreta dos resultados.

###<a name="1.5.5"/>1.5.5</a>

- hashPartitionResolver resolveForRead() corrigido: quando nenhuma chave de partição fornecida estava emitindo exceção, em vez de retornar uma lista de todos os links registrados.

###<a name="1.5.4"/>1.5.4</a>

- Corrige o problema [nº 100](https://github.com/Azure/azure-documentdb-node/issues/100) — Agente HTTPS Dedicado: evite modificar o agente global para fins do Banco de Dados de Documentos. Use um agente dedicado para todas as solicitações da biblioteca.

###<a name="1.5.3"/>1.5.3</a>

- Corrige o problema [nº 81](https://github.com/Azure/azure-documentdb-node/issues/81) — trate corretamente os traços em ids de mídia.

###<a name="1.5.2"/>1.5.2</a>

- Corrige o problema [nº 95](https://github.com/Azure/azure-documentdb-node/issues/95) — aviso de perda do ouvinte EventEmitter.

###<a name="1.5.1"/>1.5.1</a>

- Corrige o problema [nº 92](https://github.com/Azure/azure-documentdb-node/issues/90) — renomeie a pasta Hash para hash nos sistemas que diferenciam maiúsculas de minúsculas.

### <a name="1.5.0"/>1.5.0</a>

- Implemente o suporte a fragmentação ao adicionar os resolvedores de partição de hash e de intervalo.

### <a name="1.4.0"/>1.4.0</a>

- Implementar o Upsert. Novos métodos upsertXXX em documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Ignorado para alinhar os números de versão com outros SDKs.

### <a name="1.2.2"/>1.2.2</a>

- Slipt Q promete wrapper para o novo repositório.
- Atualização para o arquivo de pacote do registro npm.

### <a name="1.2.1"/>1.2.1</a>

- Implementa o roteamento com base em ID.
- Corrige o problema [nº 49](https://github.com/Azure/azure-documentdb-node/issues/49) -propriedade atual está em conflito com o método current().

### <a name="1.2.0"/>1.2.0</a>

- Suporte adicionado para índice geoespaciais.
- Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, &#47;&#47; ou terminar com um espaço.
- Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementa a política de indexação V2.

### <a name="1.0.3"/>1.0.3</a>

- Problema [nº 40](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurações eslint e grunt implementadas no núcleo e promessa de SDK.

### <a name="1.0.2"/>1.0.2</a>

- Problema [nº 45](https://github.com/Azure/azure-documentdb-node/issues/45) - Promessa de wrapper não inclui o cabeçalho com erro.

### <a name="1.0.1"/>1.0.1</a>

- Habilidade implementada de consultar conflitos por meio da adição de readConflicts, readConflictAsync e queryConflicts.
- Documentação da API atualizada.
- Problema [nº 41](https://github.com/Azure/azure-documentdb-node/issues/41) - Erro client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>

- SDK DO GA.

## Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

As solicitações feitas ao Banco de Dados de Documentos usando um SDK obsoleto serão rejeitadas pelo serviço.

> [AZURE.WARNING]
Todas as versões do SDK do Banco de Dados de Documentos do Azure para Node.js anteriores à versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.

<br/>

| Versão | Data do lançamento | Data de desativação 
| ---	  | ---	         | ---
| [1\.8.0](#1.8.0) | 14 de junho de 2016 |--- 
| [1\.7.0](#1.7.0) | 26 de abril de 2016 |--- 
| [1\.6.0](#1.6.0) | 29 de março de 2016 |--- 
| [1\.5.6](#1.5.6) | 08 de março de 2016 |--- 
| [1\.5.5](#1.5.5) | 02 de fevereiro de 2016 |--- 
| [1\.5.4](#1.5.4) | 01 de fevereiro de 2016 |--- 
| [1\.5.2](#1.5.2) | 26 de janeiro de 2016 |--- 
| [1\.5.2](#1.5.2) | 22 de janeiro de 2016 |--- 
| [1\.5.1](#1.5.1) | 4 de janeiro de 2016 |--- 
| [1\.5.0](#1.5.0) | 31 de dezembro de 2015 |--- 
| [1\.4.0](#1.4.0) | 06 de outubro de 2015 |--- 
| [1\.3.0](#1.3.0) | 06 de outubro de 2015 |--- 
| [1\.2.2](#1.2.2) | 10 de setembro de 2015 |--- 
| [1\.2.1](#1.2.1) | 15 de agosto 2015 |--- 
| [1\.2.0](#1.2.0) | 05 de agosto de 2015 |--- 
| [1\.1.0](#1.1.0) | 09 de julho de 2015 |--- 
| [1\.0.3](#1.0.3) | 04 de junho de 2015 |--- 
| [1\.0.2](#1.0.2) | 23 de maio de 2015 |--- 
| [1\.0.1](#1.0.1) | 15 de maio de 2015 |--- 
| [1\.0.0](#1.0.0) | 08 de abril de 2015 |--- 
| 0.9.4-prerelease | 06 de abril de 2015 | 29 de fevereiro de 2016 
| 0.9.3-prerelease | 14 de janeiro de 2015 | 29 de fevereiro de 2016 
| 0.9.2-prerelease | 18 de dezembro de 2014 | 29 de fevereiro de 2016 
| 0.9.1-prerelease | 22 de agosto de 2014 | 29 de fevereiro de 2016 
| 0.9.0-prerelease | 21 de agosto de 2014 | 29 de fevereiro de 2016


## Perguntas frequentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Consulte também

Para saber mais sobre o Banco de Dados de Documentos, confira a página de serviço do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0706_2016-->