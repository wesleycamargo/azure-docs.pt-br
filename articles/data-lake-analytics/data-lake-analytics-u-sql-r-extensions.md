---
title: Estender scripts U-SQL com R no Azure Data Lake Analytics | Microsoft Docs
description: "Saiba como executar o código R em Scripts U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Tutorial: introdução à extensão do U-SQL com o R

O exemplo a seguir ilustra as etapas básicas para implantar código R:
* Use a instrução REFERENCE ASSEMBLY para habilitar as extensões do R para o Script U-SQL.
* Use a operação REDUCE para particionar os dados de entrada em uma chave.
* As extensões do R para o U-SQL incluem um redutor interno (Extension.R.Reducer) que executa o código R em cada vértice atribuído ao redutor. 
* O uso de quadros de dados nomeados dedicados chamou inputFromUSQL e outputToUSQL respectivamente para passar dados entre USQL e R. Os nomes de identificador DataFrame de entrada e saída são fixos (ou seja, os usuários não podem alterar esses nomes predefinidos dos identificadores DataFrame de entrada e saída).


--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-r-integrates-with-u-sql"></a>Como o R se integra com o U-SQL

### <a name="datatypes"></a>Tipos de dados
* As colunas numéricas e de cadeia de caracteres do U-SQL são convertidas como estão entre R DataFrame e U-SQL [tipos com suporte: duplo, cadeia de caracteres, booliano, inteiro, byte].
* Não há suporte para o tipo de dados do fator em U-SQL.
* byte[] deve ser serializado como uma cadeia de caracteres codificada em base64.
* As cadeias de caracteres de U-SQL poderão ser convertidas em fatores no código R, depois que U-SQL criar quadro de dados de entrada R ou definindo o parâmetro redutor stringsAsFactors: true.

### <a name="schemas"></a>Esquemas
* Conjuntos de dados U-SQL não podem ter nomes de coluna duplicados.
* Nomes de coluna de conjuntos de dados U-SQL devem ser cadeias de caracteres.
* Nomes de coluna devem ser iguais em scripts U-SQL e R.
* A coluna somente leitura não pode fazer parte do dataframe de saída. Porque colunas somente leitura são injetadas automaticamente na tabela U-SQL caso ela faça parte do esquema de saída do UDO.

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)

