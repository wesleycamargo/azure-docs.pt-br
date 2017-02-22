---
title: "Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure | Microsoft Docs"
description: Marcas e dados de sensor de fluxos IoT com o processamento de dados em tempo real e Stream Analytics
keywords: "solução iot, introdução ao iot, ferramentas"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 83b267a610a1d910fab09f8e42f079d269e3f0bb
ms.openlocfilehash: e11e6070002275544fa5a82923cdcad4412e40b7

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure
O Stream Analytics do Azure dá suporte a UDFs (Funções Definidas pelo Usuário) escritas em JavaScript. Com o conjunto avançado dos métodos String, RegExp, Math, Array e Date fornecidos pelo JavaScript, as transformações de dados complexas com trabalhos do Stream Analytics se tornam mais fáceis de serem criadas.

## <a name="overview"></a>Visão geral
As UDFs do JavaScript dão suporte a funções escalares sem monitoração de estado somente para computação que não requerem conectividade externa. O valor retornado de uma função pode ser apenas um valor escalar (único). Uma vez adicionada a um trabalho, a função pode ser usada em qualquer lugar na consulta como uma função escalar interna.

Aqui estão alguns cenários de exemplo em que você considerará as UDFs do JavaScript úteis:
* Análise e manipulação de cadeia de caracteres com funções de expressão regular, por exemplo, Regexp_Replace() e Regexp_Extract()
* Decodificação e codificação de dados, por exemplo, conversão de binário em hexadecimal
* Cálculos matemáticos com funções matemáticas do JavaScript
* Operações de matriz como classificação, junção, localização e preenchimento

A seguir estão ações que não podem ser realizadas com uma UDF do JavaScript no Stream Analytics:
* Chamada de pontos de extremidade REST externos, por exemplo, pesquisa inversa de IP ou extração de dados de referência de uma fonte externa
* Serialização ou desserialização de formato de evento personalizado em entradas/saídas
* Agregações personalizadas

Embora elas não estejam bloqueadas na definição de funções, você deve evitar usar funções como Date.GetDate() ou Math.random(). Essas funções **não** retornam o mesmo resultado sempre que você as chama e o serviço Stream Analytics do Azure não mantém um diário das invocações da função e dos resultados retornados. Portanto, se uma função retorna resultados diferentes nos mesmos eventos, a capacidade de repetição não será garantida quando um trabalho for reiniciado por você ou pelo serviço Stream Analytics.

## <a name="adding-a-javascript-udf-from-azure-portal"></a>Adicionando uma UDF do JavaScript do Portal do Azure
Para criar uma função definida pelo usuário do JavaScript simples em um trabalho do Stream Analytics existente, siga essas etapas.

1.  Abra o portal do Azure.

2.  Localize seu trabalho do Stream Analytics, clique no link de funções em **TOPOLOGIA DO TRABALHO**.
 
3.  Você verá uma lista vazia de funções existentes, clique no ícone **Adicionar** para adicionar uma nova UDF.

4.  Na folha **Nova Função**, selecione JavaScript como o Tipo de Função e você verá um modelo de função padrão aparecer no editor.
 
5.  Coloque _hex2Int_ como o alias da UDF e altere a implementação da função como a seguir

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Clique no botão **Salvar**, sua função aparecerá na lista de funções. 

7.  Clique na nova função **hex2Int** e você pode verificar a definição da função. Observe que todas as funções terão o prefixo “UDF” adicionado na frente do alias da função. Você precisará chamar a função **com o prefixo** em sua consulta do Stream Analytics, neste caso **UDF.hex2Int**.
 
## <a name="calling-javascript-udf-in-a-query"></a>Chamando a UDF do JavaScript em uma consulta

1. Abra o editor de consulta clicando em **Consulta** em **TOPOLOGIA DO TRABALHO**. 

2.  Edite a consulta e chame a UDF que você acabou de adicionar, conforme mostrado a seguir:

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Clique com o botão direito do mouse na entrada do trabalho para carregar o arquivo de dados de exemplo.
 
4.  Clique em **Testar** para testar sua consulta.


## <a name="supported-javascript-objects"></a>Objetos JavaScript com suporte
A UDF do JavaScript do Stream Analytics do Azure dá suporte a objetos internos padrão da linguagem JavaScript. Para obter a lista de objetos, consulte o documento em [Objetos Globais](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversão de tipo do Stream Analytics e JavaScript

Há diferenças entre os tipos com suporte na linguagem de consulta do Stream Analytics e no JavaScript. A tabela a seguir lista os mapeamentos de conversão entre os dois.


---
Análise de fluxo | JavaScript
--- | ---
bigint | Número (observe que o JavaScript pode representar apenas o inteiro até 2^53 exatamente)
DateTime | Data (observe que o JavaScript tem suporte apenas para milissegundos) 
double | Número
nvarchar(MAX) | Cadeia de caracteres
Registro | Objeto
Matriz | Matriz
NULO | Nulo


As conversões de JavaScript para ASA também são listadas.

---
JavaScript | Análise de fluxo
--- | ---
Número | Bigint se o número for arredondado e entre long.MinValue e long.MaxValue, caso contrário, double
Data | DateTime
Cadeia de caracteres | nvarchar(MAX)
Objeto | Registro
Matriz | Matriz
Null, Undefined | NULO
qualquer outro tipo, por exemplo, Function, Error etc. | Sem suporte – erro de tempo de execução

## <a name="troubleshooting"></a>Solucionar problemas
Os erros de tempo de execução do JavaScript serão considerados fatais e exibidos no Log de atividades. Para recuperar o log do Portal do Azure, vá para o seu trabalho e clique em **Log de atividades**.
 

## <a name="other-javascript-udf-usage-patterns"></a>Outros padrões de uso da UDF do JavaScript

### <a name="writing-nested-json-to-output"></a>Gravando JSON aninhado na saída
Gravar uma cadeia de caracteres JSON na saída é uma tarefa comum quando você tem a etapa de processamento de acompanhamento que usa a saída do trabalho do Stream Analytics como entrada e requer um formato JSON. O exemplo abaixo chama a função JSON.stringify() para empacotar todos os pares nome-valor da entrada e gravá-las como um valor de cadeia de caracteres único na saída. 

### <a name="javascript-udf-definition"></a>Definição de UDF do JavaScript:

```
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de exemplo:**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



<!--HONumber=Feb17_HO1-->


