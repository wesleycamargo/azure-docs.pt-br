---
title: Transmitir dados do Stream Analytics para o Data Lake Store | Microsoft Docs
description: "Usar o Stream Analytics do Azure para transmitir dados para o Repositório Azure Data Lake"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/07/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 2864fbf1fc4f070cb4d88d1bb3efbaaf408c68ce


---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Dados de transmissão do Blob de Armazenamento do Azure para o Repositório Data Lake usando o Stream Analytics do Azure
Neste artigo, você aprenderá como usar o Repositório Azure Data Lake como uma saída para um trabalho do Stream Analytics do Azure. Este artigo demonstra um cenário simples que lê dados de um blob de armazenamento do Azure (entrada) e grava os dados no Repositório Data Lake (saída).

> [!NOTE]
> No momento, há suporte para a criação e a configuração das saídas do Data Lake Store para o Stream Analytics apenas no [Portal Clássico do Azure](https://manage.windowsazure.com). Portanto, algumas partes deste tutorial usarão o Portal Clássico do Azure.
>
>

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite sua assinatura do Azure** para a Public Preview do Data Lake Store. Veja [instruções](data-lake-store-get-started-portal.md).
* **Conta de Armazenamento do Azure**. Você usará um contêiner de blob desta conta para os dados de entrada para um trabalho do Stream Analytics. Para esse tutorial, suponha que você criará uma conta de armazenamento chamada **datalakestoreasa** e um contêiner na conta chamado **datalakestoreasacontainer**. Depois de criar o contêiner, carregue um arquivo de dados de exemplo nele. Você pode obter um arquivo de dados de exemplo do [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Você pode usar vários clientes, como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/), para carregar dados em um contêiner de blob.

  > [!NOTE]
  > Se você criar a conta do Portal do Azure, certifique-se de criá-la com o modelo de implantação **Clássico** . Isso garante que a conta de armazenamento poderá ser acessada do Portal Clássico do Azure porque é ele que usamos para criar um trabalho do Stream Analytics. Para obter instruções sobre como criar uma conta de armazenamento do Portal do Azure usando a implantação Clássica, confira [Criar uma conta do Armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account).
  >
  > Ou, você pode criar uma conta de armazenamento do Portal 
Clássico do Azure.
  >
  >
* **Conta do Repositório Azure Data Lake**. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md).  

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho do Stream Analytics
Você começa ao criar um trabalho do Stream Analytics, que inclui uma fonte de entrada e um destino de saída. Para este tutorial, o código-fonte é um contêiner de blob do Azure e o destino é o Repositório Data Lake.

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Na parte inferior esquerda da tela, clique em **Novo**, **Serviços de Dados**, **Stream Analytics**, **Criação Rápida**. Forneça os valores conforme mostrado abaixo e, em seguida, clique em **Criar trabalho do Stream Analytics**.

    ![Criar um trabalho do Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Create a Stream Analytics job")

## <a name="create-a-blob-input-for-the-job"></a>Criar uma entrada de blob para o trabalho
1. Abra a página do trabalho do Stream Analytics, clique na guia **Entradas** e, em seguida, clique em **Adicionar uma entrada** para iniciar um assistente.
2. Na página **Adicionar uma entrada ao seu trabalho**, selecione **Transmissão de dados** e, em seguida, clique na seta para frente.

    ![Adicionar uma entrada ao seu trabalho](./media/data-lake-store-stream-analytics/create.input.1.png "Add an input to your job")
3. Na página **Adicionar um fluxo de dados ao seu trabalho**, selecione **Armazenamento de Blobs** e, em seguida, clique na seta para frente.

    ![Adicionar uma transmissão de dados ao trabalho](./media/data-lake-store-stream-analytics/create.input.2.png "Add a data stream to the job")
4. Na página **Configurações de Armazenamento de Blobs** , forneça os detalhes do armazenamento de blobs que será usado como a fonte de dados de entrada.

    ![Fornecer configurações de Armazenamento de Blobs](./media/data-lake-store-stream-analytics/create.input.3.png "Provide the blob storage settings")

   * **Digite um alias de entrada**. Este é um nome exclusivo que você fornece para a entrada do trabalho.
   * **Selecione uma conta de armazenamento**. Verifique se a conta de armazenamento está na mesma região que o trabalho do Stream Analytics ou você estará sujeito a pagar pelos custos adicionais de mover dados entre regiões.
   * **Forneça um contêiner de armazenamento**. Você pode optar por criar um novo contêiner ou selecionar um contêiner existente.

     Clique na seta para frente.
5. Na página **Configurações de serialização**, configure o formato de serialização como **CSV**, o delimitador como **guia**, a codificação como **UTF8** e, em seguida, clique na marca de seleção.

    ![Fornecer as configurações de serialização](./media/data-lake-store-stream-analytics/create.input.4.png "Provide the serialization settings")
6. Depois de concluir com o assistente, a entrada de blob será adicionada sob a guia **Entradas** e a coluna **Diagnóstico** deverá exibir **OK**. Você também pode testar a conexão com a entrada de maneira explícita usando o botão **Testar conexão** na parte inferior.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Criar uma saída do Repositório Data Lake para o trabalho
1. Abra a página do trabalho do Stream Analytics, clique na guia **Saídas** e, em seguida, clique em **Adicionar uma Saída** para inicializar um assistente.
2. Na página **Adicionar uma saída ao seu trabalho**, selecione **Data Lake Store** e, em seguida, clique na seta para frente.

    ![Adicionar uma saída ao seu trabalho](./media/data-lake-store-stream-analytics/create.output.1.png "Add an output to your job")
3. Na página **Autorizar conexão**, se você já tiver criado uma conta do Data Lake Store, clique em **Autorizar Agora**. Caso contrário, clique em **Inscreva-se agora** para criar uma nova conta. Para este tutorial, vamos supor que você já tem uma conta do Repositório Data Lake criada (conforme mencionado nos pré-requisitos). Você será automaticamente autorizado ao usar as credenciais com as quais você se conectou no Portal Clássico do Azure.

    ![Autorizar o Repositório Data Lake](./media/data-lake-store-stream-analytics/create.output.2.png "Authorize Data Lake Store")
4. Na página **Configurações do Repositório Data Lake** , insira as informações conforme mostrado na captura de tela abaixo.

    ![Especificar as configurações do Repositório Data Lake](./media/data-lake-store-stream-analytics/create.output.3.png "Specify Data Lake Store settings")

   * **Digite um alias de saída**. Este é um nome exclusivo que você fornece para a saída do trabalho.
   * **Especificar uma conta do Repositório Data Lake**. Você já deve tê-la criado, conforme mencionado nos pré-requisitos.
   * **Especificar um padrão de prefixo do caminho**. Isso é necessário para identificar os arquivos de saída que são gravados no Repositório Data Lake pelo trabalho do Stream Analytics. Visto que os títulos das saídas gravados pelo trabalho estão em um formato GUID, a inclusão de um prefixo ajudará a identificar a saída gravada. Se você quiser incluir um carimbo de data e hora como parte do prefixo, certifique-se de incluir `{date}/{time}` no padrão do prefixo. Se você incluí-lo, os campos **Formato de Data **e **Formato de Hora** serão habilitados e você poderá selecionar o formato escolhido.

     Clique na seta para frente.
5. Na página **Configurações de serialização**, configure o formato de serialização como **CSV**, o delimitador como **guia**, a codificação como **UTF8** e, em seguida, clique na marca de seleção.

    ![Especificar o formato de saída](./media/data-lake-store-stream-analytics/create.output.4.png "Specify the output format")
6. Depois de concluir o assistente, a saída do Data Lake Store será adicionada sob a guia **Saídas** e a coluna **Diagnóstico** deverá exibir **OK**. Você também pode testar a conexão com a saída de maneira explícita usando o botão **Testar Conexão** na parte inferior.

## <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics
Para executar um trabalho do Stream Analytics, você deve executar uma consulta da guia Consulta. Para este tutorial, você pode executar a consulta de exemplo, substituindo os espaços reservados pelos aliases de entrada e saída de trabalho, conforme mostrado na captura de tela abaixo.

![Executar consulta](./media/data-lake-store-stream-analytics/run.query.png "Run query")

Clique em **Salvar** na parte inferior da tela e, em seguida, clique em **Iniciar**. Na caixa de diálogo, selecione **Hora Personalizada** e, em seguida, selecione uma data no passado, como **1/1/2016**. Clique na marca de seleção para iniciar o trabalho. Pode levar até dois minutos para o trabalho ser iniciado.

![Definir o tempo de trabalho](./media/data-lake-store-stream-analytics/run.query.2.png "Set job time")

Depois que o trabalho for iniciado, clique na guia **Monitorar** para ver como os dados foram processados.

![Monitorar o trabalho](./media/data-lake-store-stream-analytics/run.query.3.png "Monitor job")

Finalmente, você pode usar o [Portal do Azure](https://portal.azure.com) para abrir sua conta do Data Lake Store e verificar se os dados foram gravados com êxito na conta.

![Verificar a saída](./media/data-lake-store-stream-analytics/run.query.4.png "Verify output")

No painel Gerenciador de Dados, observe que a saída é gravada em uma pasta, conforme especificado nas configurações de saída do Data Lake Store (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Confira também
* [Criar um cluster do HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Nov16_HO3-->


