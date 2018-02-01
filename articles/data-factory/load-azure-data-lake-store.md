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
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Carregar dados no Azure Data Lake Store usando o Azure Data Factory

O [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) é um repositório em hiper-escala corporativo para cargas de trabalho de análise de big data. O Azure Data Lake permite que você capture dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias.

O Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado, que pode ser usado para popular o lago com os dados de seu sistema existente e economizar tempo valioso ao criar suas soluções de análise. Aqui estão os principais benefícios de carregar dados no Azure Data Lake Store usando o Azure Data Factory:

* **Fácil de configurar**: assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte de armazenamento de dados avançados**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem, para ver lista detalhada na tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e compatível**: os dados são transferidos por HTTPS ou ExpressRoute e a presença global do serviço garante que os dados nunca saiam do limite geográfico
* **Alto desempenho**: até dados de 1 Gbps velocidade de carregamento no Azure Data Lake Store. Obtenha detalhes de [desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de cópia de dados do Data Factory para **carregar dados do Amazon S3 no Azure Data Lake Store**. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
>  Para obter informações gerais sobre as funcionalidades do Data Factory para copiar dados bidirecionalmente no Azure Data Lake Store, consulte o artigo [Copiar dados bidirecionalmente no Azure Data Lake Store usando o Azure Data Factory](connector-azure-data-lake-store.md).
>
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>pré-requisitos

* **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Azure Data Lake Store**. Se você não tiver uma conta do Data Lake Store, veja o artigo [Criar uma conta do Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account) para conhecer as etapas para criar um.
* **Amazon S3**. Este artigo mostra como copiar dados do Amazon S3. Você pode usar outros repositórios de dados seguindo as etapas semelhantes.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Clique em **NOVO** no menu à esquerda, clique em **Dados + Analytics** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, forneça os valores conforme mostrado na captura de tela a seguir: 
      
     ![Página de novo data factory](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **Name.** Insira um nome global exclusivo para o armazenamento de dados. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
            `Data factory name "LoadADLSDemo" is not available`

    * **Subscription.** Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos.** Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão.** Selecione **V2 (versão prévia)**.
    * **Local.** Selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Azure Data Lake Store, Banco de Dados SQL do Azure, etc.) usados pelo data factory podem estar em outros locais/regiões.

3. Clique em **Criar**.
4. Após a criação, vá ao seu data factory, a página do **Data Factory** será exibida conforme mostrado na imagem. Clique no bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada. 
   
   ![Página inicial da data factory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Carregar dados no Azure Data Lake Store

1. Na página de Introdução, clique no bloco **Copiar dados** para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na página **Propriedades** da ferramenta Copy Data, especifique **CopyFromAmazonS3ToADLS** para o **Nome da tarefa** e clique em **Avançar**. 

    ![Ferramenta Copy Data - página de propriedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **Amazon S3** e clique em **Avançar**.

    ![Página Armazenamento de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Na página **Especificar conexão Amazon S3**, faça o seguinte: 
    1. Especifique a **ID da chave de acesso**.
    2. Especifique a **chave de acesso secreta**.
    3. Clique em **Próximo**. 

        ![Especifique a conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. Na página **Escolha o arquivo de entrada ou a pasta**, navegue até o pasta/arquivo que você deseja copiar, selecione e clique em **Escolhes**, em seguida, clique em **Próximo**. 

    ![Escolha a pasta ou arquivo de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Na página **Armazenamento de dados de destino**, selecione **Azure Data Lake Store** e clique em **Avançar**. 

    ![Página Armazenamento de dados de destino](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Nessa página, escolha o comportamento de cópia marcando as opções **Copiar arquivos recursivamente** e **Cópia binária** (copiar arquivos como-são). Clique em **Próximo**.

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Na página **Especificar conexão Data Lake Store**, faça o seguinte: 

    1. Selecione seu Data Lake Store para o **nome de conta do Data Lake Store**.
    2. Especifique as informações de entidade de serviço, inclusive **locatário**, **ID da entidade de serviço**, e **chave principal do serviço**.
    3. Clique em **Próximo**. 

    > [!IMPORTANT]
    > Neste passo a passo, você deve usar uma **entidade de serviço** para autenticar o Data Lake Store. Siga as instruções [aqui](connector-azure-data-lake-store.md#using-service-principal-authentication) e verifique se você concedeu a permissão apropriada à entidade de serviço no Azure Data Lake Store.

    ![Especifique a conta do Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. Na página **Escolha o arquivo de saída ou a pasta**, especifique **copyfroms3**, em seguida, clique em **Próximo**. 

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. Na página **Configurações**, clique em **Avançar**. 

    ![Página Configurações](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na página **Resumo** examine as configurações e clique em **Avançar**.

    ![Página Resumo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **página Implantação**, clique em **Monitor** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. Confira os links para exibir detalhes de execução da atividade e executar novamente o pipeline da coluna **Ações**. 

    ![Monitorar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para ver as atividades em execução associadas com o pipeline de execução, clique em **Exibir atividades em execução** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline, então você vê apenas uma entrada. Para alternar novamente para a exibição de pipeline em execução, Clique em **Pipelines** na parte superior. Clique em **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Você pode monitorar ainda mais detalhes de execução de cada atividade de cópia, clicando no link **Detalhes** em **Ações** no modo de exibição de monitoramento de atividades. Ele mostra as informações, incluindo o volume de dados copiados da fonte para o coletor, a taxa de transferência, as etapas realizadas com a duração correspondente e as configurações usadas.

    ![Monitorar detalhes da execução da atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique se que os dados são copiados em seu repositório Azure Data Lake. 

    ![Verifique saída do Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Próximas etapas

Ir para o seguinte artigo para saber mais sobre o suporte do Azure Data Lake Store: 

> [!div class="nextstepaction"]
>[Azure Data Lake Store connector](connector-azure-data-lake-store.md) (Conector do Azure Data Lake Store)