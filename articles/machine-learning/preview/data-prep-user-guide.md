---
title: "Guia detalhado sobre como usar a Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma visão geral e detalhes sobre como resolver problemas de dados com a Preparação de Dados do Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: a75ad760fe7f9663c5b43500581170d5f8671a13
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="data-preparations-user-guide"></a>Guia do usuário de Preparação de Dados 
A experiência de Preparação de Dados do Azure Machine Learning fornece muitas funções avançadas. Este artigo aborda as partes mais profundos dessa experiência.

### <a name="step-execution-history-and-caching"></a>Execução de etapa, histórico e caching 
O histórico de etapas de Preparação de Dados mantém uma série de caches por motivos de desempenho. Se você clicar em uma etapa e ela atingir um cache, ela não será executada novamente. Se você tiver um bloco de gravação no final do histórico de etapas e alternar entre as etapas, mas não fizer alterações, a gravação não será disparada depois da primeira vez. Uma nova gravação ocorrerá e substituirá a anterior se você:

- Fizer alterações no bloco de gravação.
- Adicionar um novo bloco de transformação e movê-lo para uma posição acima do bloco de gravação, gerando uma invalidação de cache.
- Alterar as propriedades de um bloco acima do bloco de gravação, gerando uma invalidação de cache.
- Selecionar atualizar em um exemplo (invalidando, dessa forma, todo o cache).

### <a name="error-values"></a>Valores de erro

Transformações de dados podem falhar para um valor de entrada por não ser possível tratá-lo adequadamente. Por exemplo, no caso de operações de coerção de tipo, a coerção falhará se o valor da cadeia de caracteres de entrada não puder ser convertido para o tipo de destino especificado. Uma operação de coerção de tipo pode converter uma coluna de tipo de cadeia de caracteres em um tipo numérico ou booliano, ou então tentar duplicar uma coluna que não existe. (Essa falha ocorre devido à movimentação da operação *excluir coluna X* antes da operação *duplicar coluna X*.)

Nesses casos, a Preparação de Dados produzirá um valor de erro como a saída. Valores de erro indicam que uma operação anterior falhou para o valor especificado. Internamente, eles são tratados como um tipo de valor de primeira classe, mas sua presença não altera o tipo subjacente de uma coluna, mesmo que uma coluna seja composta inteiramente por valores de erro.

É muito fácil identificar os valores de erro. Eles são realçados em vermelho e indicam “Erro”. Para determinar o motivo do erro, focalize um valor de erro para ver uma descrição da falha.

Valores de erro se propagam. Depois que um valor de erro ocorre, ele se propaga na maioria dos casos como um erro pela maioria das operações. Há três maneiras de substituí-los ou removê-los:

* Substitua
    -  Clique com o botão direito do mouse em uma coluna e selecione **Substituir valores de erro**. Então você pode escolher um valor de substituição para cada valor de erro encontrado na coluna.

* Remover
    - A Preparação de Dados inclui filtros interativos para preservar ou remover valores de erro.
    - Clique com o botão direito do mouse em uma coluna e selecione **Filtrar Coluna**. Para preservar ou remover valores de erro, crie uma condicional com a condição *"é erro"* ou *"não é erro"*.

* Use uma expressão Python para operar condicionalmente em valores de erro. Para obter mais informações, consulte a [seção sobre extensões do Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>amostragem
Um arquivo de Fontes de Dados obtém dados brutos de uma ou mais fontes, seja do sistema de arquivos local ou de um local remoto. O bloco de Exemplo permite que você especifique se deseja trabalhar com um subconjunto dos dados gerando exemplos. Trabalhar com uma amostra dos dados em vez de um grande conjunto de dados geralmente leva a um melhor desempenho ao realizar operações nas etapas posteriores.

É possível gerar e armazenar várias amostras para cada arquivo de Fontes de Dados. No entanto, apenas uma amostra pode ser definida como a amostra ativa. Você pode criar, editar ou excluir amostras no assistente de Fonte de Dados ou ao editar o Bloco de Exemplo. Quaisquer arquivos de Preparação de Dados que façam referência a uma Fonte de Dados usa inerentemente a amostra especificada no arquivo da Fonte de Dados.

Há várias estratégias de amostragem disponíveis, cada uma com diferentes parâmetros configuráveis.

#### <a name="top"></a>Top
Essa estratégia pode ser aplicada a arquivos locais ou remotos. Ela usa as N primeiras linhas (especificadas pela Contagem) na fonte de dados.

#### <a name="random-n"></a>N aleatória 
Essa estratégia só pode ser aplicada a arquivos locais. Ela usa N linhas aleatórias (especificadas pela Contagem) na fonte de dados. Você pode fornecer uma semente específica para garantir que a mesma amostra seja gerada, desde que a Contagem também seja a mesma.

#### <a name="random-"></a>Aleatório % 
Essa estratégia pode ser aplicada a arquivos locais ou remotos. Em ambos os casos, uma probabilidade e uma semente devem ser fornecidas, de modo similar à estratégia de N aleatório.

Para obter amostras de arquivos remotos, é necessário fornecer parâmetros adicionais:

- Gerador de amostras 
  - Selecione um cluster do Spark ou destino de computação remota do Docker a ser usado para a geração de exemplo. O destino de computação deve ser criado para o projeto com antecedência para que apareça na lista. Siga as etapas na seção "Criar um novo destino de computação" em [Como usar a GPU no Azure Machine Learning](how-to-use-gpu.md) para criar destinos de computação.
- Armazenamento de amostra 
  - Informe um local de armazenamento intermediário para armazenar o exemplo remoto. Esse caminho deve ser um diretório diferente do local do arquivo de entrada.

#### <a name="full-file"></a>Arquivo completo 
Essa estratégia só pode ser aplicada a arquivos locais, levando o arquivo completo para a fonte de dados. Se o arquivo for muito grande, essa opção poderá causar lentidão em operações futuras no aplicativo. Talvez seja mais apropriado usar uma estratégia de amostragem diferente.


### <a name="fork-merge-and-append"></a>Criar fork, mesclar e acrescentar

Ao aplicar um filtro em um conjunto de dados, a operação divide os dados em dois conjuntos de resultados: um conjunto representa registros bem-sucedidos no filtro e o outro representa os registros malsucedidos. Em ambos os casos, o usuário pode escolher qual conjunto de resultados exibir. O usuário pode descartar o outro conjunto de dados ou colocá-lo em um novo fluxo de dados. A última opção é conhecida como criar fork.

Para criar fork: 
1. Selecione uma coluna, clique com o botão direito do mouse e selecione a coluna **Filtro**.

2. Em **Eu quero**, selecione **Manter Linhas** para exibir o conjunto de resultados que é aprovado no filtro.

3. Selecione **Remover Linhas** para exibir o conjunto com falha.

4. Após **Condições**, selecione **Criar Fluxo de Dados Contendo as Linhas Filtradas** para criar fork no conjunto de resultados não exibidos em um novo fluxo de dados.


Essa prática geralmente é usada para separar um conjunto de dados que exige preparação adicional. Depois de arrebanhar o conjunto de dados com fork, é comum mesclar os dados com o conjunto de resultados no fluxo de dados original. Para executar uma mesclagem (o inverso de uma operação de criar fork), execute uma das seguintes ações:

- **Acrescentar Linhas**. Mesclar dois ou mais fluxos de dados verticalmente (por linha). 
- **Acrescentar Colunas**. Mesclar dois ou mais fluxos de dados horizontalmente (por coluna).


>[!NOTE]
>Acrescentar Colunas falhará se ocorrer uma colisão de coluna.


Após uma operação de mesclagem, um ou mais fluxos de dados serão referenciados por um fluxo de dados de origem. A Preparação de Dados mostrará uma notificação no canto inferior direito do aplicativo, abaixo da lista de etapas.


Qualquer operação no fluxo de dados referenciado exige que o fluxo de dados pai atualize a amostra usada no fluxo de dados referenciado. Nesse caso, uma caixa de diálogo de confirmação substitui a notificação de referência de fluxo de dados no canto inferior direito. A caixa de diálogo confirma que você precisa atualizar o fluxo de dados para sincronizar as alterações em quaisquer fluxos de dados de dependência.

### <a name="list-of-appendices"></a>Lista de apêndices 
* [Fontes de dados com suporte](data-prep-appendix2-supported-data-sources.md)  
* [Transformações com suporte](data-prep-appendix3-supported-transforms.md)  
* [Inspetores com suporte](data-prep-appendix4-supported-inspectors.md)  
* [Destinos com suporte](data-prep-appendix5-supported-destinations.md)  
* [Exemplo de expressões de filtro em Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Exemplo de expressões de fluxo de dados de transformação em Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Exemplo de fontes de dados em Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Exemplo de conexões de destino em Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Exemplo de transformações de coluna em Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
