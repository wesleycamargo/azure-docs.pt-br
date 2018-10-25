---
title: Copiar dados do Azure Data Lake Store Gen1 para Gen2 (versão prévia) com o Azure Data Factory
description: Usar o Azure Data Factory para copiar dados do Azure Data Lake Store Gen1 para Gen2 (versão prévia)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: jingwang
ms.openlocfilehash: 953585ffcc5a40d9ae48055f68a1c1fa84db25cc
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249325"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-preview-with-azure-data-factory"></a>Copiar dados do Azure Data Lake Store Gen1 para Gen2 (versão prévia) com o Azure Data Factory

O [Azure Data Lake Storage Gen2 (Versão prévia)](../storage/data-lake-storage/introduction.md) adiciona um protocolo com namespace de sistema de arquivos hierárquico e recursos de segurança ao Armazenamento de Blobs do Azure, facilitando a conexão de estruturas de análise a uma camada de armazenamento durável. No Data Lake Store Gen2 (versão prévia), todas as qualidades do armazenamento de objetos permanecem enquanto adicionam as vantagens de uma interface de sistema de arquivos.

Se você estiver usando atualmente o Azure Data Lake Store Gen1, você pode avaliar a nova funcionalidade do Gen2 ao copiar dados do Data Lake Store Gen1 para o Gen2 usando o Azure Data Factory.

O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. É possível usar o serviço para preencher o Data Lake com dados de um conjunto avançado de armazenamentos de dados locais e baseados em nuvem e economizar tempo ao compilar as soluções de análise. Para obter uma lista detalhada de conectores com suporte, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimentação de dados gerenciados de expansão. Devido à arquitetura de expansão do ADF, é possível ingerir dados com alta taxa de transferência. Para obter detalhes, consulte [Desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo descreve como usar a ferramenta Copiar Dados do Data Factory para copiar dados do _Azure Data Lake Store Gen1_ no _Azure Data Lake Store Gen2_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta do Azure Data Lake Store Gen1 contendo dados.
* Conta de Armazenamento do Microsoft Azure com Data Lake Store Gen2 habilitada: se você não tiver uma conta de Armazenamento, clique [here](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) para criar uma.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**:
   
   ![Cria um novo data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir: 
      
   ![Página de novo data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: insira um nome exclusivo para o seu Azure data factory. Se você receber o erro "Nome do Data factory \"LoadADLSDemo\" não está disponível," digite um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione a assinatura do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: selecione **V2**.
    * **Local**: selecione um local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. 

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Página inicial do data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados no Azure Data Lake Store Gen2

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data: 

   ![Bloco Ferramenta Copy Data](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na página **Propriedades**, especifique Copy Data, especifique **CopyFromADLSGen1ToGen2** para o campo **Nome da tarefa** e clique em **Avançar**:

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, clique em **+Criar nova conexão**:

    ![Página Armazenamento de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    Selecione **Azure Data Lake Store Gen1** na galeria de conectores e selecione **Continuar**
    
    ![Página de armazenamento de dados de origem do Azure Data Lake Store Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. Na página **Especificar conexão do Azure Data Lake Store Gen1**, siga as seguintes etapas:
   1. Selecione o Data Lake Store Gen1 para o nome da conta.
   2. Especifique ou valide o **Locatário** e selecione Concluir.
   3. Selecione **Avançar**.
   
   > [!IMPORTANT]
   > Neste passo a passo, você deve usar uma identidade gerenciada para recursos do Azure para autenticar o Data Lake Storage Gen1e. Verifique se você concedeu as permissões corretas no Azure Data Lake Store Gen1 ao MSI seguindo [essas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especifique a conta do Azure Data Lake Store Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
   
   4. Você verá uma nova conexão ser criada. Selecione **Avançar**.
   
5. Na página **Escolher arquivo de entrada ou pasta** página, navegue até a pasta e o arquivo que você deseja copiar. Selecione a pasta/arquivo, selecione **Escolher**:

    ![Escolha a pasta ou arquivo de entrada](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Especifique o comportamento da cópia, marcando as opções **Copiar arquivos recursivamente** e **Cópia binária**. Selecione **Avançar**:

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. Na página **Armazenamento de dados de destino**, clique em **+ Criar nova conexão**, e, em seguida, selecione **Azure Data Lake Store Gen2 (versão prévia)** e selecione **Continuar**:

    ![Página Armazenamento de dados de destino](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. Na página **Especificar conexão do Azure Data Lake Store Gen2**, siga as seguintes etapas:

   1. Selecione a conta do Data Lake Store Gen2 compatível na lista suspensa "Nome da conta de armazenamento".
   2. Selecione **Avançar**.
   
   ![Especificar a conta do Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. Em **Escolher o arquivo de saída ou pasta** insira **copyfromadlsgen1** como o nome da pasta de saída e selecione **Avançar**: 

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. Na página **Configurações**, selecione **Avançar** para usar as configurações padrão.

11. Na página **Resumo**, examine as configurações e selecione **Avançar**:

    ![Página Resumo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. Na **Página de implantação**, selecione **Monitorar** para monitorar o pipeline:

    ![Página Implantação](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente:

    ![Monitorar execuções de pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline, assim, você vê apenas uma entrada. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** (imagem de óculos) em **Ações** na exibição de monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas:

    ![Monitorar detalhes da execução da atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. Verifique se os dados são copiados para a conta do Data Lake Store Gen2.

## <a name="best-practices"></a>Práticas recomendadas

Ao copiar um grande volume de dados do armazenamento de dados baseado em arquivo, é recomendável:

- Particione os arquivos em conjuntos de arquivos de 10 TB a 30 TB cada.
- Não dispare muitas execuções de cópia simultâneas para evitar a limitação de armazenamentos de dados de coletor ou origem. É possível iniciar com uma execução de cópia e monitorar a taxa de transferência e, em seguida, adicionar mais gradualmente, conforme necessário.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da atividade de cópia](copy-activity-overview.md)
* [Conector do Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md)