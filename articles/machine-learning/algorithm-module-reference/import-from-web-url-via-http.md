---
title: 'Importar da URL da Web via HTTP: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar a importação de URL da Web por meio do módulo HTTP no serviço de Azure Machine Learning para ler dados de uma página da Web pública para uso em um experimento de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f0847e9dd90267d985b75be3c3a07ce8fae98a9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029664"
---
# <a name="import-from-web-url-via-http-module"></a>Importar da URL da Web por meio do módulo HTTP

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para ler dados de uma página da Web pública para uso em um experimento de machine learning.

As seguintes restrições se aplicam a dados publicados em uma página da web:

- Dados devem estar em um dos formatos com suporte: CSV, TSV, ARFF ou SvmLight. Outros dados causará erros.
- Nenhuma autenticação é necessária ou com suporte. Dados devem estar publicamente disponíveis. 

Há duas maneiras de obter dados: use o Assistente para configurar a fonte de dados ou configurá-lo manualmente.

## <a name="use-the-data-import-wizard"></a>Use o Assistente de importação de dados

1. Adicione a **importação de dados** módulo ao seu experimento. Você pode encontrar o módulo na interface, além de **dados de entrada e saída** categoria.

2. Clique em **iniciar o Assistente de importação de dados** e selecione a URL da Web via HTTP.

3. Cole a URL e selecione um formato de dados.

4. Quando a configuração foi concluída.

Para editar uma conexão de dados existentes, inicie o assistente novamente. O assistente carrega todos os detalhes de configuração anterior, para que você não precisa começar novamente do zero

## <a name="manually-set-properties-in-the-import-data-module"></a>Definir manualmente as propriedades no módulo importar dados

As etapas a seguir descrevem como configurar manualmente a origem de importação.

1. Adicione a [importação de dados](import-data.md) módulo ao seu experimento. Você pode encontrar o módulo na interface, além de **dados de entrada e saída** categoria.

2. Para **fonte de dados**, selecione **URL da Web via HTTP**.

3. Para **URL**, digite ou cole a URL completa da página que contém os dados que você deseja carregar.

    A URL deve incluir a URL do site e o caminho completo, com o nome de arquivo e extensão, para a página que contém os dados a serem carregados.

    Por exemplo, a página a seguir contém o conjunto de dados íris de repositório da Universidade da Califórnia, Irvine de aprendizado de máquina:

    `http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Para **formato de dados**, selecione um dos dados com suporte a formatos na lista.

    É recomendável que você sempre verifique os dados com antecedência para determinar o formato. A página de UC Irvine usa o formato CSV. Outros formatos de dados com suporte são TSV, ARFF e SvmLight.

5. Se os dados estiverem no formato CSV ou TSV, use o **arquivo tem uma linha de cabeçalho** opção para indicar se a fonte de dados inclui uma linha de cabeçalho. A linha de cabeçalho é usada para atribuir nomes de coluna.

6. Selecione o **usar resultados armazenados em cache** opções se você não espera que os dados de mudar muito ou se você quiser evitar a recarregar os dados de cada vez que você executar o teste.

    Quando essa opção é selecionada, o experimento carrega o tempo de dados primeiro o módulo é executado e consequentemente usa uma versão em cache do conjunto de dados.

    Se você deseja recarregar o conjunto de dados em cada iteração do teste de conjunto de dados, desmarque a **usar resultados armazenados em cache** opção. Os resultados também são recarregados se houver alterações aos parâmetros de [importação de dados](import-data.md).

7. Execute o experimento.

## <a name="results"></a>Resultados

Ao concluir, clique o conjunto de dados de saída e selecione **visualizar** para ver se os dados foram importados com êxito.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 