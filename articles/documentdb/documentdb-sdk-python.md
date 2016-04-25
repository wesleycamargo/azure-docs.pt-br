<properties 
	pageTitle="SDK do Python para o Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba tudo sobre o SDK do Python, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Python para o Banco de Dados de Documentos." 
	services="documentdb" 
	documentationCenter="python" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="rnagpal"/>

# SDK do Banco de Dados de Documentos

> [AZURE.SELECTOR]
- [SDK .NET](documentdb-sdk-dotnet.md)
- [SDK do Node.js](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [SDK do Python](documentdb-sdk-python.md)

##SDK do Python para o Banco de Dados de Documentos

<table>
<tr><td>**Baixar**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Colaborar**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Documentação**</td><td>[Documentação de referência do SDK do Python](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Guia de introdução**</td><td>[Introdução ao SDK do Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plataforma atual com suporte**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Notas de versão

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correções de bugs relacionadas ao particionamento no lado do servidor a fim de permitir caracteres especiais no caminho de partitionkey.

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- [Coleções particionadas](documentdb-partition-data.md) implementadas e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md). 

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Adicionar resolvedores de hash e intervalo para ajudar com a fragmentação de arquivos em várias partições.

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementar o Upsert. Novos métodos UpsertXXX adicionados para dar suporte ao recurso Upsert.
- Implementar o roteamento com base em ID. Nenhuma alteração pública de API, todas as alterações são internas.

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Oferece suporte ao índice Geoespacial.
- Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, \\ ou terminar com um espaço.
- Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementa a política de indexação V2

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Oferece suporte à conexão de proxy

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- SDK DO GA

## Datas de lançamento e de baixa
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

As solicitações feitas ao Banco de Dados de Documentos usando um SDK obsoleto serão rejeitadas pelo serviço.

> [AZURE.WARNING]
Todas as versões do SDK do Banco de Dados de Documentos do Azure para Python anteriores à versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.

<br/>

| Versão | Data do lançamento | Data de desativação 
| ---	  | ---	         | ---
| [1\.6.1](#1.6.1) | 08 de abril de 2016 |---
| [1\.6.0](#1.6.0) | 29 de março de 2016 |---
| [1\.5.0](#1.5.0) | 03 de janeiro de 2016 |---
| [1\.4.2](#1.4.2) | 06 de outubro de 2015 |---
| [1\.4.1](#1.4.1) | 06 de outubro de 2015 |---
| [1\.2.0](#1.2.0) | 06 de agosto de 2015 |---
| [1\.1.0](#1.1.0) | 09 de julho de 2015 |---
| [1\.0.1](#1.0.1) |25 de maio de 2015 |---
| [1\.0.0](#1.0.0) | 07 de abril de 2015 |---
| 0.9.4-prelease | 14 de janeiro de 2015 | 29 de fevereiro de 2016
| 0.9.3-prelease | 09 de dezembro de 2014 | 29 de fevereiro de 2016
| 0.9.2-prelease | 25 de novembro de 2014 | 29 de fevereiro de 2016
| 0.9.1-prelease | 23 de setembro de 2014 | 29 de fevereiro de 2016
| 0.9.0-prelease | 21 de agosto de 2014 | 29 de fevereiro de 2016

## Perguntas frequentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Consulte também

Para saber mais sobre o Banco de Dados de Documentos, confira a página de serviço do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0413_2016-->