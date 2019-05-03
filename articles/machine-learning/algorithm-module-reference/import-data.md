---
title: 'Importar dados: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo importar dados no serviço de Azure Machine Learning para carregar dados em um teste de machine learning existente dados dos serviços de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028299"
---
# <a name="import-data-module"></a>Módulo de importação de dados

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para carregar dados em um teste de machine learning existente dados dos serviços de nuvem.  
O módulo agora apresenta um Assistente para ajudá-lo a escolher uma opção de armazenamento e selecionar dentre as assinaturas existentes e contas para configurar rapidamente todas as opções. Necessidade de editar uma conexão de dados existente? Sem problemas. o assistente carrega todos os detalhes de configuração anterior, para que você não precisa começar novamente do zero. 
  
Depois de definir os dados que você quer e se conectar à fonte de [importação de dados](./import-data.md) infere o tipo de dados de cada coluna com base nos valores que ele contém e carrega os dados em seu espaço de trabalho do Azure Machine Learning. A saída de [importação de dados](./import-data.md) é um conjunto de dados que pode ser usado com qualquer experimento.

  
Se sua fonte de dados for alterado, você pode atualizar o conjunto de dados e adicionar novos dados ao executar novamente [importação de dados](./import-data.md). No entanto, se você não quiser ler novamente da fonte de cada vez que executar o experimento, selecione a **usar resultados armazenados em cache** opção como TRUE. Quando essa opção é selecionada, o módulo verifica se o teste foi executado anteriormente usando a mesma origem e as mesmas opções de entrada. Se uma execução anterior for encontrada, os dados no cache são usados, em vez de recarregar os dados da origem.
 

## <a name="data-sources"></a>Fontes de dados

O módulo importar dados suporta as seguintes fontes de dados. Clique nos links para obter instruções detalhadas e exemplos de uso de cada fonte de dados. 
 
Se você não tiver certeza de como ou quando você deve armazenar seus dados, consulte este guia para cenários comuns de dados no processo de ciência de dados:  [Cenários para análises avançadas no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Fonte de dados| Usar com|
|-----------|-----------|  
|[URL da Web via HTTP](./import-from-web-url-via-http.md)|Obtém os dados que estão hospedados em uma URL da web que usa HTTP e que foi fornecido nos formatos CSV, TSV, ARFF ou SvmLight|  
|[Importar do armazenamento de BLOBs do Azure](./import-from-azure-blob-storage.md) |Obter dados armazenados no serviço blob do Azure|  

## <a name="how-to-use-import-data"></a>Como usar a importação de dados
 
1. Adicione a **importação de dados** módulo ao seu experimento. Você pode encontrar esse módulo na **dados de entrada e saída** categoria na interface.

2. Clique em **iniciar o Assistente de importação de dados** para configurar a fonte de dados usando um assistente.

    O assistente obtém o nome da conta e as credenciais e ajudam você a definir outras opções. Se você estiver editando uma configuração existente, ele carrega os valores atuais pela primeira vez.

3. Se você não quiser usar o assistente, clique em **fonte de dados**e escolha o tipo de armazenamento baseado em nuvem que você estiver lendo. 

    Configurações adicionais dependem do tipo de armazenamento que você escolher, e se o armazenamento é protegido ou não. Você talvez precise fornecer o nome da conta, tipo de arquivo ou as credenciais. Algumas fontes não exigem autenticação; para outras pessoas, você talvez precise saber o nome da conta, uma chave ou o nome do contêiner.

4. Selecione o **usar resultados armazenados em cache** opção se você quiser armazenar em cache o conjunto de dados para reutilização em execuções sucessivas.

    Supondo que não houve nenhuma outra alteração para os parâmetros do módulo, o experimento carrega o tempo de dados somente o primeiro, o módulo é executado e consequentemente usa uma versão em cache do conjunto de dados.

    Desmarque essa opção se você precisar recarregar os dados sempre que você executar o teste.

5. Execute o experimento.

    Quando a importação de dados carrega os dados para a interface, ele infere o tipo de dados de cada coluna com base nos valores que ele contém, numéricos ou categóricos.

    - Se um cabeçalho estiver presente, o cabeçalho é usado para nomear as colunas do conjunto de dados de saída.

    - Se não houver nenhum cabeçalho de coluna existentes nos dados, os novos nomes de coluna são gerados usando o formato col1 col2,... , coln *.

## <a name="results"></a>Resultados

Quando a importação for concluída, clique o conjunto de dados de saída e selecione **visualizar** para ver se os dados foram importados com êxito.

Se você quiser salvar os dados para reutilização, em vez de importar um novo conjunto de dados sempre que o teste é executado, a saída e selecione **Salvar como conjunto de dados**. Escolha um nome para o conjunto de dados. O conjunto de dados salvo preserva os dados no momento da economia e dados não são atualizados quando o teste for executado novamente, mesmo se o conjunto de dados no teste for alterado. Isso pode ser útil para tirar instantâneos de dados.

Depois de importar os dados, ele pode ser necessário algumas preparações adicionais para modelagem e análise:


- Use [editar metadados](./edit-metadata.md) alterar nomes de coluna, para lidar com uma coluna como um tipo de dados diferente ou para indicar que algumas colunas são rótulos ou recursos.

- Use [selecionar colunas no conjunto de dados](./select-columns-in-dataset.md) para selecionar um subconjunto de colunas para transformar ou use na modelagem. As colunas transformadas ou removidas facilmente podem ser reintegradas ao conjunto de dados original usando o [adicionar colunas](./add-columns.md) módulo.  

- Use [partição e exemplo](./partition-and-sample.md) para dividir o conjunto de dados, executar a amostra ou obter as primeiras n linhas.

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 