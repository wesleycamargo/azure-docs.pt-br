---
title: Atributos da entidade Artigo na API de Conhecimento Acadêmico | Microsoft Docs
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Artigo na API de Conhecimento Acadêmico em Serviços Cognitivos.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 75efba2c2ce8842f233f766876ca00844338fb25
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094704"
---
# <a name="paper-entity"></a>Entidade Artigo

<sub> *Os atributos a seguir são específicos da entidade Artigo. (Ty = '0') </sub>


NOME    |DESCRIÇÃO                                        |Tipo       | Operações
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                                          |Int64      |É igual a
Ti      |Título do artigo                                        |Cadeia de caracteres     |Igual a,<br/>StartsWith
L       |Código de idioma do artigo separado por "\@@@"            |Cadeia de caracteres     |É igual a
S       |Ano do artigo                                         |Int32      |Igual a,<br/>IsBetween
D       |Data do artigo                                         |Data       |Igual a,<br/>IsBetween
CC      |Contagem de citações                                     |Int32      |Nenhum  
ECC     |Contagem estimada de citações                           |Int32      |Nenhum
AA.AuN  |Nome do autor                                        |Cadeia de caracteres     |Igual a,<br/>StartsWith
AA.AuId |ID do autor                                          |Int64      |É igual a
AA.AfN  |Nome da associação do autor                            |Cadeia de caracteres     |Igual a,<br/>StartsWith
AA.AfId |ID da associação do autor                              |Int64      |É igual a
AA.S    |Ordem do autor para o artigo                         |Int32      |É igual a
F.FN    |Nome do campo de estudo                                |Cadeia de caracteres     |Igual a,<br/>StartsWith
F.FId   |ID do campo de estudo                                  |Int64      |É igual a
J.JN    |Nome do periódico                                       |Cadeia de caracteres     |Igual a,<br/>StartsWith
J.JId   |ID do periódico                                         |Int64      |É igual a
C.CN    |Nome da série de conferências                             |Cadeia de caracteres     |Igual a,<br/>StartsWith
C.CId   |ID da série de conferências                               |Int64      |É igual a
RId     |ID dos artigos de referência                              |Int64[]    |É igual a
W       |Palavras do título e do resumo do artigo                |String[]   |É igual a
E       |Metadados estendidos (veja a tabela abaixo)                |Cadeia de caracteres     |Nenhum  
        


## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

NOME    | DESCRIÇÃO               
--------|---------------------------    
DN      | Nome de exibição do artigo 
S       | Fontes – lista das fontes da Web do artigo, ordenadas por classificação estática
S.Ty    | Tipo de fonte (1:HTML, 2:Texto, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | URL de origem
VFN     | Nome completo do local – nome completo do periódico ou conferência
VSN     | Nome curto do local – nome curto do periódico ou conferência
V       | Volume – volume do periódico
BV      | Nome do periódico
BT      | 
PB      | Abreviações do periódico
I       | Edição – edição do periódico
FP      | FirstPage – primeira página do artigo
LP      | LastPage – última página do artigo
DOI     | Identificador de Objeto Digital
CC      | Contextos de citação – lista de IDs dos artigos de referência e o contexto correspondente no artigo (por exemplo, [{123:[“as raposas marrons são conhecidas por pular conforme relatado no artigo”, “os cachorros preguiçosos são um equívoco histórico conforme mostrado no artigo 123”]})
IA      | Resumo invertido
IA.IndexLength| Número de itens no índice (contagem de palavras do resumo)
IA.InvertedIndex| Lista de palavras do resumo e sua posição correspondente no resumo original (por exemplo, [{"a": [0, 15, 30]}, {"raposa": [1]}, {"marrom":[2]}])
