---
title: Carregar dados no Azure Data Lake Store usando o Azure Data Factory | Microsoft Docs
description: Usar o Azure Data Factory para copiar dados para o Azure Data Lake Store
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 4446f83563293d0834f241dcca382ccf6ea99403
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>Carregar dados no Azure Data Lake Store usando o Azure Data Factory

O [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) é um repositório em hiper-escala corporativo para cargas de trabalho de análise de big data. O Azure Data Lake permite capturar dados de qualquer tamanho, tipo e velocidade de ingestão. Os dados são capturados em um único local para análise operacional e exploratória.

O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. Você pode usar o serviço para preencher o lago com dados do sistema existente e economizar tempo ao criar soluções de análise.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Azure Data Lake Store usando:

* **Fácil de configurar**: assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte de armazenamento de dados avançados**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e compatível**: os dados são transferidos por HTTPS, ou ExpressRoute. A presença do serviço global garante que os dados nunca saiam do limite geográfico.
* **Alto desempenho**: velocidade de carregamento de dados de até 1-GB no Azure Data Lake Store. Para obter detalhes, consulte [Desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de cópia de dados do Data Factory para _carregar dados do Amazon S3 no Azure Data Lake Store_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
> Para obter mais informações, consulte [Copiar dados para ou da Azure Data Lake Store usando o Azure Data Factory](connector-azure-data-lake-store.md).
>
> Este artigo aplica-se à versão 2 do Azure Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está disponibilidade geral (GA), consulte a [atividade de cópia no Azure Data Factore versão 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>pré-requisitos

* Assinatura do Azure: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Azure Data Lake Store: Se você não tiver uma conta do Data Lake Store, veja as instruções em [Criar uma conta do Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3: Este artigo mostra como copiar dados do Amazon S3. Você pode usar outros repositórios de dados seguindo as etapas semelhantes.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**:
   
   ![Cria um novo data factory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir: 
      
   ![Página de novo data factory](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nome**: insira um nome exclusivo para o seu Azure data factory. Se você receber o erro "Nome do Data factory \"LoadADLSDemo\" não está disponível," digite um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione a assinatura do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: selecione **V2 (Versão prévia)**.
    * **Local**: selecione um local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, Azure Storage, Azure SQL Database e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Página inicial da data factory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-data-lake-store"></a>Carregar dados no Azure Data Lake Store

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data: 

   ![Bloco Ferramenta Copy Data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na página **Propriedades**, especifique Copy Data, especifique **CopyFromAmazonS3ToADLS** para o campo **Nome da tarefa** e clique em **Avançar**:

    ![Página Propriedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **Amazon S3** e selecione **Avançar**:

    ![Página Armazenamento de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Na página **Especificar conexão Amazon S3**, faça o seguinte: 
   1. Especifique o valor da **ID da chave de acesso**.
   2. Especifique o valor da **chave de acesso secreta**.
   3. Selecione **Avançar**.
   
   ![Especifique a conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. Na página **Escolher arquivo de entrada ou pasta** página, navegue até a pasta e o arquivo que você deseja copiar. Selecione a pasta/arquivo, selecione **Escolher**e, em seguida, selecione **Avançar**:

    ![Escolha a pasta ou arquivo de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Na página **Armazenamento de dados de destino**, selecione **Azure Data Lake Store** e selecione**Avançar**:

    ![Página Armazenamento de dados de destino](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Escolha o comportamento de cópia marcando as opções **Copiar arquivos recursivamente** e **Cópia binária** (copiar arquivos como-são). Selecione **Avançar**:

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Na página **Especificar conexão Data Lake Store**, faça o seguinte: 

   1. Selecione seu Data Lake Store para o **nome de conta do Data Lake Store**.
   2. Especifique as informações de entidade de serviço, **locatário**, **ID da entidade de serviço**, e **chave principal do serviço**.
   3. Selecione **Avançar**.
   
   > [!IMPORTANT]
   > Neste passo a passo, você deve usar uma _entidade de serviço_ para autenticar o Data Lake Store. Certifique-se de conceder as permissões corretas da entidade de serviço no Azure Data Lake Store seguindo [essas instruções](connector-azure-data-lake-store.md#using-service-principal-authentication).
   
   ![Especifique a conta do Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Em **Escolher o arquivo de saída ou pasta** insira **copyfroms3** como o nome da pasta de saída e selecione **Avançar**: 

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na página **Configurações**, selecione **Avançar**:

    ![Página Configurações](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na página **Resumo**, examine as configurações e selecione **Avançar**:

    ![Página Resumo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **Página Implantação**, selecione **Monitor** para monitorar o pipeline (tarefa):

    ![Página Implantação](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente:

    ![Monitorar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline, assim, você vê apenas uma entrada. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** em **Ações** no modo de exibição de monitoramento de atividades. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas:

    ![Monitorar detalhes da execução da atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique se que os dados são copiados em seu repositório Azure Data Lake: 

    ![Verifique saída do Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Próximas etapas

Ir para o seguinte artigo para saber mais sobre o suporte do Azure Data Lake Store: 

> [!div class="nextstepaction"]
>[Azure Data Lake Store connector](connector-azure-data-lake-store.md) (Conector do Azure Data Lake Store)