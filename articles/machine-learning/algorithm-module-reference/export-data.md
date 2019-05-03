---
title: 'Exporte dados: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo exportar dados no serviço de Azure Machine Learning para salvar dados de trabalho, dados intermediários e os resultados de suas experiências nos destinos de armazenamento de nuvem fora do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028314"
---
# <a name="export-data-module"></a>Módulo de exportação de dados

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para salvar dados de trabalho, dados intermediários e os resultados de suas experiências nos destinos de armazenamento de nuvem fora do Azure Machine Learning.

Este módulo oferece suporte a exportar ou salvar seus dados para os seguintes serviços de dados de nuvem:


- **Exportar para o armazenamento de BLOBs do Azure**: Salva dados para o serviço de Blob no Azure. Dados no serviço Blob podem ser compartilhados publicamente ou salvos em armazenamentos de dados de aplicativo protegidos.

  
## <a name="how-to-configure-export-data"></a>Como configurar a exportação de dados

1. Adicione a **exportar dados** módulo à sua experiência na interface. Você pode encontrar esse módulo na **de entrada e saída** categoria.

2. Conectar-se **exportar dados** para o módulo que contém os dados que você deseja exportar.

3. Clique duas vezes em **exportar dados** para abrir o **propriedades** painel.

4. Para **destino de dados**, selecione o tipo de armazenamento em nuvem onde você salvará seus dados. Se você fizer alterações a essa opção, todas as outras propriedades são redefinidas. Portanto, certifique-se de escolher essa opção primeiro!

5. Fornecem um método de autenticação e nome de conta necessário para acessar a conta de armazenamento especificado.

    **Exportar para o armazenamento de BLOBs do Azure** é a única opção na versão prévia privada. A seguir mostra como definir o módulo.
    1. É o serviço de BLOBs do Azure para armazenar grandes quantidades de dados, incluindo dados binários. Há dois tipos de armazenamento de BLOBs: blobs públicos e os blobs que exigem credenciais de logon.

    2. Para **tipo de autenticação**, escolha **pública (SAS)** se você souber que o armazenamento dá suporte ao acesso por meio de uma URL SAS.

          Uma URL SAS é um tipo especial de URL que pode ser gerado usando um utilitário de armazenamento do Azure e está disponível por apenas um período limitado.  Ele contém todas as informações necessárias para autenticação e o download.

        Para **URI**, digite ou cole o URI completo que define a conta e o blob público.

        Para o formato de arquivo CSV e TSV têm suporte.

    3. Para contas privadas, escolha **conta**e forneça o nome da conta e a chave de conta, para que o teste possa escrever para a conta de armazenamento.

         - **Nome da conta**: Digite ou cole o nome da conta em que você deseja salvar os dados. Por exemplo, se a URL completa da conta de armazenamento está `http://myshared.blob.core.windows.net`, você digitaria `myshared`.

        - **Chave de conta**: Cole a chave de acesso de armazenamento que está associada com a conta.

        -  **Caminho para o contêiner, diretório ou blob**: Digite o nome do blob onde os dados exportados serão armazenados. Por exemplo, para salvar os resultados do teste em um novo blob chamado **results01.csv** no contêiner **previsões** em uma conta denominada **mymldata**, a URL completa para o blob seria `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Portanto, no campo **caminho para o contêiner, diretório ou blob**, você especificaria o contêiner e nome de blob como segue: `predictions/results01.csv`

        - Se você especificar o nome de um blob que ainda não existir, o Azure cria o blob para você.

       -  Ao gravar um blob existente, você pode especificar que o conteúdo atual do blob substituído, definindo a propriedade **modo de gravação do armazenamento de BLOBs do Azure**. Por padrão, essa propriedade é definida como **erro**, que significa que um erro é gerado sempre que um arquivo de blob existente de mesmo nome for encontrado.


    4. Para **formato de arquivo para o arquivo de blob**, selecione o formato no qual os dados devem ser armazenados.

        - **CSV**: Valores separados por vírgulas (CSV) são o formato de armazenamento padrão. Para exportar os títulos de coluna junto com os dados, selecione a opção **linha de cabeçalho do blob de gravação**.  Para obter mais informações sobre a vírgula - formato delimitado usado no Azure Machine Learning, consulte [converter para CSV](./convert-to-csv.md).

        - **TSV**: Formato de valores separados por tabulações (TSV) é compatível com muitas ferramentas de aprendizado de máquina. Para exportar os títulos de coluna junto com os dados, selecione a opção **linha de cabeçalho do blob de gravação**.  

 
    5. **Usar resultados armazenados em cache**: Selecione esta opção se você quiser evitar a reconfiguração os resultados para o arquivo de blob sempre que você executar o teste. Se não houver nenhuma outra alteração para os parâmetros do módulo, o experimento grava os resultados apenas na primeira vez em que o módulo é executado, ou quando há alterações nos dados.

    6. Execute o experimento.

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 