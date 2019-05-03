---
title: 'Importar do armazenamento de BLOBs do Azure: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba que este tópico descreve como usar a importação do módulo de armazenamento de BLOBs do Azure no serviço de Azure Machine Learning para ler dados do armazenamento de BLOBs do Azure, para que você possa usar os dados em um experimento de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029679"
---
# <a name="import-from-azure-blob-storage-module"></a>Importar do módulo de armazenamento de BLOBs do Azure

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para ler dados do armazenamento de BLOBs do Azure, para que você possa usar os dados em um experimento de machine learning.  

É o serviço de Blob do Azure para armazenar grandes quantidades de dados, incluindo dados binários. Blobs do Azure podem ser acessados de qualquer lugar, usando HTTP ou HTTPS. A autenticação pode ser necessária dependendo do tipo de armazenamento de BLOBs. 

- Blobs públicos podem ser acessados por qualquer pessoa, ou por usuários que têm uma URL SAS.
- Blobs privados exigem um logon e credenciais.

Importar do armazenamento de BLOBs exige que os dados sejam armazenados em blobs que usam o **blob de blocos** formato. Os arquivos armazenados no blob devem usar separados por vírgulas (CSV) ou separados por tabulações (TSV) formatos. Quando você lê o arquivo, os registros e quaisquer cabeçalhos de atributo aplicáveis são carregados como linhas na memória como um conjunto de dados.


É altamente recomendável que você analise seus dados antes de importar, para certificar-se de que o esquema é conforme o esperado. O processo de importação verifica algum número de linhas de cabeçalho para determinar o esquema, mas linhas posteriores podem conter colunas adicionais ou dados que causam erros.



## <a name="manually-set-properties-in-the-import-data-module"></a>Definir manualmente as propriedades no módulo importar dados

As etapas a seguir descrevem como configurar manualmente a origem de importação.

1. Adicione a **importação de dados** módulo ao seu experimento. Você pode encontrar esse módulo na interface, além de **dados de entrada e saída**

2. Para **fonte de dados**, selecione **armazenamento de BLOBs do Azure**.

3. Para **tipo de autenticação**, escolha **público (URL de SAS)** se você souber que as informações foi fornecidas como uma fonte de dados públicos. Uma URL SAS é uma URL com limite de tempo para acesso público que você possa gerar usando um utilitário de armazenamento do Azure.

    Caso contrário, escolha **conta**.

4. Se os dados estiverem em uma **pública** blob que pode ser acessado usando uma URL de SAS, não é necessário credenciais adicionais porque a cadeia de caracteres de URL contém todas as informações necessárias para download e autenticação.

    No **URI** campo, digite ou cole o URI completo que define a conta e o blob público.



5. Se os dados estiverem em uma **privada** conta, você deve fornecer credenciais, incluindo o nome da conta e a chave.

    - Para **nome da conta**, digite ou cole o nome da conta que contém o blob que você deseja acessar.

        Por exemplo, se a URL completa da conta de armazenamento está `http://myshared.blob.core.windows.net`, você digitaria `myshared`.

    - Para **chave de conta**, cole a chave de acesso de armazenamento que está associada com a conta.

        Se você não souber a chave de acesso, consulte a seção, "Gerenciar suas contas de armazenamento do Azure" neste artigo: [Sobre contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Para **caminho para o contêiner, diretório ou blob**, digite o nome do blob específico que você deseja recuperar.

    Por exemplo, se você carregou um arquivo chamado **data01.csv** no contêiner **trainingdata** em uma conta denominada **mymldata**, a URL completa para o arquivo seria: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    Portanto, no campo **caminho para o contêiner, diretório ou blob**, você digitaria: `trainingdata/data01.csv`

    Para importar vários arquivos, você pode usar os caracteres curinga `*` (asterisco) ou `?` (ponto de interrogação).

    Por exemplo, supondo que o contêiner `trainingdata` contém vários arquivos de um formato compatível, você pode usar a seguinte especificação para ler todos os arquivos a partir do `data`e concatená-las em um único conjunto de dados:

    `trainingdata/data*.csv`

    Você não pode usar caracteres curinga em nomes de contêiner. Se você precisar importar arquivos de vários contêineres, use uma instância separada do **importar dados** módulo para cada contêiner e, em seguida, mesclagem de conjuntos de dados usando o [adicionar linhas](./add-rows.md) módulo.

    > [!NOTE]
    > Se você tiver selecionado a opção **usar resultados armazenados em cache**, quaisquer alterações feitas aos arquivos no contêiner não disparam uma atualização dos dados no teste.

7. Para **formato de arquivo de Blob**, selecione uma opção que indica o formato dos dados que são armazenados no blob, para que o Azure Machine Learning pode processar os dados adequadamente. Há suporte para os seguintes formatos:

    - **CSV**: Valores separados por vírgulas (CSV) são o formato de armazenamento padrão para exportar e importar arquivos no Azure Machine Learning. Se os dados já contém uma linha de cabeçalho, certifique-se de selecionar a opção **arquivo tem uma linha de cabeçalho**, ou o cabeçalho será tratado como uma linha de dados.

       

    - **TSV**: Valores separados por tabulações (TSV) são um formato usado por muitas ferramentas de aprendizado de máquina. Se os dados já contém uma linha de cabeçalho, certifique-se de selecionar a opção **arquivo tem uma linha de cabeçalho**, ou o cabeçalho será tratado como uma linha de dados.

       

    - **ARFF**: Esse formato dá suporte à importação de arquivos no formato usado pelo conjunto de ferramentas Weka. 

   

8. Execute o experimento.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 