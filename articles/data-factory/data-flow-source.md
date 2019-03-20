---
title: Transformação de Fonte do Fluxo de Dados de mapeamento do Azure Data Factory
description: Transformação de Fonte do Fluxo de Dados de mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 20491981cb02e428ff4114b9456d74b0de651be8
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569018"
---
# <a name="mapping-data-flow-source-transformation"></a>Transformação de Fonte de Fluxo de Dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A Transformação de Fonte configura uma fonte de dados que você deseja usar para trazer dados para seu fluxo de dados. Você pode ter mais de uma Transformação de Fonte em um único Fluxo de Dados. Sempre começar a criar seus fluxos de dados com uma transformação de origem.

> [!NOTE]
> Cada Fluxo de Dados requer pelo menos uma Transformação de Fonte. Adicione quantas fontes adicionais forem necessárias para concluir suas transformações de dados. Você pode associar essas fontes com uma transformação de Junção ou União. Quando você depura seu fluxo de dados em sessões de depuração, os dados serão lidos da origem usando a configuração de amostragem ou limites de origem de depuração. No entanto, nenhum dado será gravado em um coletor até que você execute seu fluxo de dados de uma atividade de fluxo de dados do pipeline. 

![Opções de Transformação de Fonte](media/data-flow/source.png "fonte")

Cada transformação de fluxo de dados de origem deve ser associada a exatamente um conjunto de dados do Data Factory. O conjunto de dados define a forma e o local dos dados para gravar ou ler. Você pode usar listas de curingas e arquivos em sua fonte para trabalhar com mais de um arquivo por vez ao usar fontes de arquivo.

## <a name="data-flow-staging-areas"></a>Áreas de preparo de Fluxo de Dados

O Fluxo de Dados funciona com conjuntos de dados "de preparo" que estão todos no Azure. Esses conjuntos de dados de fluxo de dados são usados para o preparo de dados para realizar suas transformações de dados. O Data Factory tem acesso a aproximadamente 80 conectores nativos diferentes. Para incluir dados dessas outras fontes em seu fluxo de dados, primeiro prepare esses dados em uma dessas áreas de preparo do conjunto de dados do Fluxo de Dados usando a Atividade de Cópia.

## <a name="options"></a>Opções

### <a name="allow-schema-drift"></a>Permitir o descompasso de esquema
Selecione Permitir o descompasso de esquema se as colunas de origem forem ser alteradas com frequência. Essa configuração permitirá que todos os campos de entrada da fonte fluam pelas transformações para o Coletor.

### <a name="validate-schema"></a>Validar esquema

![Fonte pública](media/data-flow/source1.png "Fonte pública 1")

Se a versão de entrada dos dados de origem não corresponder ao esquema definido, a execução do fluxo de dados falhará.

### <a name="sampling"></a>amostragem
Use a amostragem para limitar o número de linhas de sua fonte.  Isso é útil ao testar ou amostragem de dados do seu código-fonte para fins de depuração.

## <a name="define-schema"></a>Definir esquema

![Transformação de Fonte](media/data-flow/source2.png "fonte 2")

Para tipos de arquivo de origem que não são fortemente tipados (isto é, arquivos simples em vez de arquivos Parquet), você deve definir os tipos de dados para cada campo aqui na transformação de fonte. Posteriormente, você pode alterar os nomes de coluna em uma Transformação de Seleção e tipos de dados em uma transformação de coluna derivada. 

![Transformação de Fonte](media/data-flow/source003.png "tipos de dados")

Para fontes de fortemente tipado, você pode modificar os tipos de dados em uma transformação de Select subsequente. 

### <a name="optimize"></a>Otimizar

![Partições de Fonte](media/data-flow/sourcepart.png "particionamento")

Na guia Otimizar da Transformação de Fonte, você verá um tipo de particionamento adicional chamado "Fonte". Isso se destacará apenas quando você tiver selecionado o BD SQL do Azure como fonte. Isso ocorre porque o ADF desejará paralelizar as conexões para executar grandes consultas em sua fonte do Banco de Dados SQL do Azure.

O particionamento de dados em sua fonte do Banco de Dados SQL é opcional, mas é útil para consultas grandes. Você tem duas opções:

### <a name="column"></a>Coluna

Selecione uma coluna para a partição em sua tabela de origem. Você também deve definir o número máximo de conexões.

### <a name="query-condition"></a>Condição de consulta

Você pode optar por particionar as conexões com base em uma consulta. Para essa opção, basta inserir o conteúdo de um predicado WHERE. Isto é, ano > 1980

## <a name="source-file-management"></a>Gerenciamento de arquivos de origem
![Novas configurações de fonte](media/data-flow/source2.png "Novas configurações")

* Caminho de caractere curinga para escolher uma série de arquivos da sua pasta de origem que corresponde a um padrão. Isso substituirá qualquer arquivo que você definiu em sua definição de conjunto de dados.
* Lista de Arquivos. Mesmo que um conjunto de arquivos. Aponte para um arquivo de texto que você cria com uma lista de arquivos do caminho relativo para processar.
* A coluna para armazenar o nome do arquivo armazenará o nome do arquivo da fonte em uma coluna em seus dados. Insira um novo nome para armazenar a cadeia de caracteres de nome de arquivo.
* Após a conclusão (você pode optar por não fazer nada com o arquivo de origem após a execução de fluxo de dados, excluir os arquivos de origem ou mover os arquivos de origem). Os caminhos para movimentação são caminhos relativos.

### <a name="sql-datasets"></a>Conjuntos de dados SQL

Quando você estiver usando o BD SQL do Azure ou o DW SQL do Azure como fonte, você terá opções adicionais.

* Consulta: Insira uma consulta SQL para sua fonte. Definir uma consulta substituirá qualquer tabela que você tenha escolhido no conjunto de dados. Observe que as cláusulas Order By não têm suporte aqui.

* Tamanho do lote: Insira um tamanho de lote para dividir dados grandes em leituras de tamanho de lote.

> [!NOTE]
> As configurações de operação do arquivo serão executadas somente quando o fluxo de dados for executado de uma execução de pipeline (depuração de pipeline ou realização da execução) usando a atividade Executar Fluxo de Dados me um pipeline. As operações de arquivo NÃO são executadas no modo de depuração do Fluxo de Dados.

### <a name="projection"></a>Projeção

![Projeção](media/data-flow/source3.png "Projeção")

Semelhante aos esquemas em conjuntos de dados, a Projeção na fonte define as colunas de dados, os tipos de dados e os formatos de dados dos dados de origem. Se você tiver um arquivo de texto sem nenhum esquema definido, clique em “Detectar Tipo de Dados” para solicitar que o ADF tente realizar a amostragem e inferir os tipos de dados. Você pode definir os formatos de dados padrão para a detecção automática usando o botão “Definir Formato Padrão”. Você pode modificar os tipos de dados de coluna em uma transformação de Coluna Derivada subsequente. Os nomes de coluna podem ser modificados usando a Transformação de Seleção.

![Formatos padrão](media/data-flow/source2.png "Formatos padrão")

## <a name="next-steps"></a>Próximas etapas

Comece a criar sua transformação de dados com a [Coluna Derivada](data-flow-derived-column.md) e a [Seleção](data-flow-select.md).
