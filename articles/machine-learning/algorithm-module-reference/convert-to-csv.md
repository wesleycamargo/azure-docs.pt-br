---
title: 'Converta para CSV: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar a converter em módulo CSV no serviço do Azure Machine Learning para converter um conjunto de dados em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script do R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028719"
---
# <a name="convert-to-csv-module"></a>Converter em CSV módulo

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para converter um conjunto de dados em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script do R ou Python.

### <a name="more-about-the-csv-format"></a>Mais informações sobre o formato CSV 

O formato CSV, o que significa "valores separados por vírgula", é um formato de arquivo usado por muitas ferramentas de aprendizado de máquina externa. CSV é um formato de intercâmbio comum ao trabalhar com linguagens de código-fonte aberto como R ou Python.

Mesmo se você fazer a maior parte do seu trabalho no Azure Machine Learning, há vezes quando você talvez ache útil para converter seu conjunto de dados em CSV para usar em ferramentas externas. Por exemplo: 

+ Baixe o arquivo CSV para abri-lo com o Excel ou importá-lo para um banco de dados relacional.  
+ Salve o arquivo CSV para o armazenamento em nuvem e conectá-lo do Power BI para criar visualizações.  
+ Use o formato CSV para preparar dados para uso em R e Python. Apenas clique com botão direito a saída do módulo para gerar o código necessário para acessar os dados diretamente do Python ou um bloco de anotações do Jupyter. 

Quando você converte um conjunto de dados para CSV, o arquivo é salvo no seu espaço de trabalho de AM do Azure. Você pode usar um utilitário de armazenamento do Azure para abrir e usar o arquivo diretamente, ou a saída do módulo com o botão direito e baixar o arquivo CSV para o computador ou usá-lo no código R ou Python.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar o converter para CSV

1.  Adicione a [converter para CSV](./convert-to-csv.md) módulo ao seu experimento. Você pode encontrar esse módulo na **conversões de formato de dados** grupo na interface. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   
  
3.  Execute o experimento.

### <a name="results"></a>Resultados
  

Clique duas vezes a saída de [converter para CSV](./convert-to-csv.md)e selecione uma destas opções.  

 + **Conjunto de dados de resultado -> Download**: Abrirá imediatamente uma cópia dos dados no formato CSV que você pode salvar em uma pasta local. Se você não especificar uma pasta, um nome de arquivo padrão será aplicado e o arquivo CSV é salvo no local **Downloads** biblioteca.


 + **Conjunto de dados de resultado -> Salvar como conjunto de dados**: Salva o arquivo CSV no espaço de trabalho de AM do Azure como um conjunto de dados separado.

 + **Gerar código de acesso a dados**: O Azure ML gera dois conjuntos de código para que você possa acessar os dados, usando o Python ou R. Para acessar os dados, copie o trecho de código para seu aplicativo. (*Gerar código de acesso de dados será disponibilizado em breve.* )

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 