---
title: Carregar dados do SAP Business Warehouse usando o Azure Data Factory | Microsoft Docs
description: Usar o Azure Data Factory para copiar dados do SAP BW (Business Warehouse)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200150"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Carregar dados do SAP BW (Business Warehouse) por meio do Azure Data Factory

Este artigo mostra um passo a passo sobre como usar o Data Factory _carregar dados do SAP BW (Business Warehouse) por meio do Open Hub no Azure Data Lake armazenamento Gen2_. Você pode seguir etapas semelhantes para copiar dados para outro [suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Consulte a [artigo de conector do SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md) sobre como copiar dados do SAP BW em geral, incluindo a introdução sobre o fluxo de extração de delta e de integração do SAP BW Open Hub.

## <a name="prerequisites"></a>Pré-requisitos

- **Azure Data Factory (ADF):** Se você não tiver um data factory, execute o "[criar um data factory](quickstart-create-data-factory-portal.md#create-a-data-factory)" seção para criar um. 

- **SAP BW Open Hub de destino (OHD) com o tipo de destino como "Tabela de banco de dados".** Siga [configurações de destino SAP BW do Open Hub](#sap-bw-open-hub-destination-configurations) seção criá-lo ou para confirmar se seu OHD existente está configurado corretamente para ser integrado com o ADF.

- **Usuário do SAP BW que está sendo usado precisa ter as seguintes permissões:**

  - Autorização para RFC e SAP BW.
  - Permissões para o "**Execute**"Atividade de objeto de autorização"**S_SDSAUTH**".

- **[Hospedar internamente o Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) com o SAP .NET connector 3.0 são necessários**. Abaixo estão as preparações detalhadas que precisam ser feitas:

  1. Instalar e registrar o IR auto-hospedado com a versão > = 3.13 (abordado no passo a passo seguinte). 

  2. Baixe o [64-bit SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) no site da SAP e instalá-lo no computador do IR auto-hospedado.  Quando a instalação, na janela "etapas de configuração opcionais", verifique se você selecionou o "**instalar Assemblies ao GAC**" opção conforme mostrado na imagem a seguir.

     ![Configurar o conector SAP do .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Cópia completa do SAP BW Open Hub

No portal do Azure, acesse sua data factory -> selecione **autor & Monitor** para iniciar o ADF UI em uma guia separada. 

1. Na página **Introdução**, selecione **Copy Data** para iniciar a ferramenta Copy Data. 

2. Sobre o **propriedades** , especifique um nome para o **nome da tarefa** campo e selecione **próxima**.

3. Sobre o **armazenamento de dados de origem** , clique em **+ criar nova conexão** -> selecione **SAP BW Open Hub** da Galeria do conector -> selecione **continuar**. Você pode digitar "SAP" na caixa de pesquisa para filtrar os conectores.

4. Sobre o **conexão especificar SAP BW Open Hub** página, 

   ![Criar serviço do SAP BW Open Hub vinculado](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Escolha o **conectar por meio do tempo de execução de integração**: clique na lista suspensa para selecionar um IR auto-hospedado existente ou crie um se você não tiver o IR auto-hospedado configurada ainda. 

      Para criar novas, clique em **+ novo** na lista suspensa-> selecione o tipo **auto-hospedado** -> especificar um **nome** e clique em **Avançar** -> escolha **Instalação expressa** para instalar no computador atual ou siga o **Configuração Manual** etapas lá.

      Conforme mencionado na [pré-requisitos](#prerequisites), verifique se você tem também a **conector do SAP .NET 3.0** instalado no mesmo computador onde o IR auto-hospedado está em execução.

   2. Especificar o SAP BW **nome do servidor**, **número do sistema**, **ID do cliente** **idioma** (se diferente de EN), **nomedeusuário**, e **senha**.

   3. Clique em **Testar conectividade** para validar as configurações e selecione **Concluir**.

   4. Você verá uma nova conexão ser criada. Selecione **Avançar**.

5. Sobre o **destinos selecione Open Hub** página, procure os destinos de Hub aberto disponível no seu SAP BW e selecione aquela que você deseja copiar dados de e, em seguida, clique em **próxima**.

   ![Selecione a tabela de SAP BW Open Hub](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique o filtro, se necessário. Se o destino do Open Hub contém apenas dados de execução única do processo de transferência de dados (DTP) com a ID de solicitação única ou certeza de que seu DTP terminou e deseja copiar todos os dados, desmarque a **excluir a última solicitação**. Você pode aprender mais sobre como essas configurações se relacionam à configuração no SAP BW [configurações de destino SAP BW do Open Hub](#sap-bw-open-hub-destination-configurations) seção. Clique em **Validate** double verificar os dados retornados, em seguida, selecione **próxima**.

   ![Configurar filtro do Open Hub do SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. No **armazenamento de dados de destino** , clique em **+ criar nova conexão**e, em seguida, selecione **Gen2 de armazenamento do Azure Data Lake**e selecione **continuar**.

8. No **conexão de especificar o Azure Data Lake Storage** página, 

   ![Criar serviço vinculado de Gen2 de ADLS](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecione sua conta do Data Lake armazenamento Gen2 capaz na lista suspensa "Nome de conta de armazenamento".
   2. Selecione **Concluir** para criar a conexão. Em seguida, selecione **Avançar**.

9. No **escolha o arquivo de saída ou a pasta** página, insira "copyfromopenhub" como o nome da pasta de saída e selecione **próxima**.

   ![Escolha a pasta de saída](media/load-sap-bw-data/choose-output-folder.png)

10. No **formato de arquivo de configuração** página, selecione **próxima** para usar as configurações padrão.

    ![Especifique o formato do coletor](media/load-sap-bw-data/specify-sink-format.png)

11. No **as configurações** página, expanda o **configurações de desempenho**e defina **grau de paralelismo de cópia** como 5 para a carga do SAP BW em paralelo. Clique em **Avançar**.

    ![Definir as configurações de cópia](media/load-sap-bw-data/configure-copy-settings.png)

12. No **resumo** página, examine as configurações e selecione **próxima**.

13. No **implantação** página, selecione **Monitor** para monitorar o pipeline.

    ![Página Implantação](media/load-sap-bw-data/deployment.png)

14. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente:

    ![Monitoramento de pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline, assim, você vê apenas uma entrada. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Monitorando de atividades](media/load-sap-bw-data/activity-monitoring.png)

16. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** (imagem de óculos) em **Ações** na exibição de monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas:

    ![Detalhes do monitoramento de atividade](media/load-sap-bw-data/activity-monitoring-details.png)

17. Examine os **ID da solicitação máxima** que é copiado. Volte para o modo de exibição de monitoramento de atividades, clique no **saída** sob **ações**.

    ![Saída de Atividade](media/load-sap-bw-data/activity-output.png)

    ![Detalhes da saída de atividade](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Cópia incremental de Hub aberto do SAP BW

> [!TIP]
>
> Consulte a [fluxo de extração de delta de conector SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para saber mais sobre o funcionamento do conector do AAD SAP BW Open Hub para copiar dados incrementais do SAP BW e leia este artigo desde o início para entender os conceitos básicos do conector relacionados configurações.

Agora, vamos continuar a configurar uma cópia incremental de Hub aberto do SAP BW. 

A cópia incremental é usando o mecanismo de marca d'água alta com base em **ID da solicitação** gerado automaticamente no destino SAP BW do Open Hub por DTP. O fluxo de trabalho para essa abordagem é ilustrado no diagrama a seguir:

![Fluxo de trabalho de cópia incremental](media/load-sap-bw-data/incremental-copy-workflow.png)

Na UI do ADF **vamos começar** página, selecione **criar o pipeline de modelo** para aproveitar o modelo interno. 

1. Pesquisar "SAP BW" para localizar e selecionar o modelo chamado **Incremental copiar do SAP BW para armazenamento do Azure Data Lake Gen2**. Este modelo copia dados em ADLS Gen2, mais tarde, você pode seguir o fluxo semelhante para copiar para outros tipos de coletor.

2. Na página principal do modelo, selecione ou crie as seguintes conexões de três e selecione **Use este modelo** na parte inferior direita.

   - **BLOBs do Azure**: neste passo a passo, podemos usar BLOBs do Azure para armazenar a marca d'água alta, que é a ID do máximo de solicitação copiado.
   - **SAP BW Open Hub**: copiar dados de sua fonte. Consulte o passo a passo completo de cópia anterior sobre as configurações detalhadas.
   - **ADLS Gen2**: para copiar dados para o coletor. Consulte o passo a passo completo de cópia anterior sobre as configurações detalhadas.

   ![Cópia incremental do modelo de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Esse modelo gera um pipeline com três atividades - **pesquisa, cópia de dados e Web** - e faz com que sejam encadeadas em caso de êxito. Vá para o pipeline **parâmetros** guia, você ver todas as configurações que você precisa fornecer.

   ![Cópia incremental de configuração do SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: especifique o nome da tabela para copiar dados de Hub aberto.

   - **ADLSGen2SinkPath**: especificar o caminho de destino Gen2 ADLS para copiar dados para. Se o caminho não existir, a atividade de cópia do ADF criará uma durante a execução.

   - **HighWatermarkBlobPath**: especifique o caminho para armazenar o valor de marca d'água alta, por exemplo, `container/path`. 

   - **HighWatermarkBlobName**: especifique o nome do blob para armazenar o valor de marca d'água alta, por exemplo, `requestIdCache.txt`. No armazenamento de BLOBs, no caminho correspondente do HighWatermarkBlobPath + HighWatermarkBlobName, por exemplo, "*container/path/requestIdCache.txt*", crie um blob com conteúdo 0. 

      ![Conteúdo do blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: nesse modelo, usamos atividade da Web para chamar aplicativos lógicos para definir o valor de marca d'água alta no armazenamento de BLOBs. Como alternativa, você também pode usar o banco de dados SQL para armazená-lo e usar a atividade de procedimento armazenado para atualizar o valor. 

      Aqui, você precisa em primeiro lugar, crie um aplicativo lógico como o seguinte, em seguida, copie o **URL de HTTP POST** a esse campo. 

      ![Configuração do aplicativo lógico](media/load-sap-bw-data/logic-app-config.png)

      1. Ir para o portal do Azure -> Novo uma **aplicativos lógicos** serviço -> clique **+ aplicativo lógico em branco** até **Designer de aplicativos lógicos**.

      2. Criar um gatilho de **quando uma solicitação HTTP é recebida**. Especifique o corpo da solicitação HTTP da seguinte maneira:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. Adicionar uma ação de **criar blob**. Para "Caminho da pasta" e "Nome do Blob", use o mesmo valor configurado no HighWatermarkBlobPath e HighWatermarkBlobName acima.

      4. Clique em **salve**e, em seguida, copie o valor de **URL de HTTP POST** usar no pipeline do ADF.

4. Depois de fornecer todos os valores para parâmetros de pipeline do ADF, você pode clicar em **Debug** -> **concluir** para invocar uma execução para validar a configuração. Ou, você pode selecionar **publicar tudo** para publicar todas as alterações, em seguida, clique em **gatilho** executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurações de destino SAP BW do Open Hub

Esta seção apresenta a configuração necessária no lado do SAP BW para usar o conector do SAP BW Open Hub no ADF para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurar a extração de delta no SAP BW

Se você precisar copiar histórico e cópia incremental ou somente cópia incremental, configure a extração de delta no SAP BW.

1. Criar o destino do Open Hub (OHD)

   Você pode criar o OHD no SAP transação RSA1, que cria automaticamente a transformação necessária e o processo de transferência de dados (DTP). Use as configurações a seguir:

   - Tipo de objeto pode ser qualquer. Aqui usamos InfoCube como um exemplo.
   - **Tipo de destino:** *Tabela de banco de dados*
   - **Chave da tabela:** *Chave técnico*
   - **Extração:** *Manter os dados e inserir registros na tabela*

   ![Criar a extração do SAP BW OHD delta](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Você pode aumentar o número de paralelo executando os processos de trabalho do SAP para o DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Agendar o DTP em cadeias de processo

   DTP um Delta para um cubo só funcionará quando a necessário linhas não foram compactadas ainda. Portanto, certifique-se de que a compactação de cubo BW não está em execução antes do DTP na tabela do Open Hub. A maneira mais fácil para isso é integrar esse DTP em suas cadeias de processo existente. No exemplo a seguir, o DTP (para OHD) é inserido na cadeia de processo entre a etapa de ajuste (acumulação agregada) e recolher (compactação de cubo).

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurar a extração completa no SAP BW

Além da extração de delta, convém ter uma extração completa de InfoProvider o mesmo. Ele geralmente se aplica se você quiser cópia sem necessidade de incremental completa ou você deseja [sincronizar novamente a extração de delta](#re-sync-delta-extraction).

Você não deve ter o DTP mais de um para o mesmo OHD. Portanto, você precisará criar uma extração OHD então delta adicional.

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para uma carga completa OHD, escolha Opções de extração de delta diferentes:

- Em OHD: defina a opção de "Extração" como "*registros de inserir e excluir dados*". Caso contrário, os dados seriam extraídos muitas vezes de repetição quando o DTP em uma cadeia de processo do BW.

- Em DTP: definir o "Modo de extração" como "*completo*". Você deve alterar o DTP criado automaticamente de Delta como Full, logo após o OHD foi criado:

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- No conector do AAD SAP BW Open Hub: desativar a opção "*última solicitação de exclusão*". Caso contrário, nada seria extraído. 

Você normalmente executa o DTP completa manualmente. Ou você também pode criar uma cadeia de processo para o DTP completo – normalmente seria uma cadeia de processo separado independente de suas cadeias de processo existente. Em ambos os casos, você deve **Verifique se o DTP terminou antes de iniciar a extração usando a cópia do ADF**, caso contrário, os dados parciais serão copiados.

### <a name="run-delta-extraction-the-first-time"></a>Executar a extração de delta na primeira vez

Tecnicamente, a extração de Delta a primeira é uma **extração completo**. Observação pelo conector do AAD SAP BW Open Hub padrão exclui a última solicitação ao copiar os dados. No caso de extração de delta pela primeira vez, na atividade de cópia do ADF, nenhum dado será extraído até que haja subsequentes DTP gera dados de delta na tabela com a ID de solicitação separados. Embora existam duas maneiras possíveis de evitar esse cenário:

1. Desative a opção "Excluir última solicitação" para o primeiro Delta extração In neste caso, que você precisará certificar-se de que a primeira DTP Delta foi concluída antes de iniciar a extração de Delta na primeira vez
2. Use o procedimento para sincronizar novamente a extração de Delta, conforme descrito abaixo.

### <a name="re-sync-delta-extraction"></a>Sincronizar novamente a extração de delta

Há alguns cenários que alteram os dados em cubos do SAP BW, mas não são considerados pelo DTP Delta:

- Exclusão seletiva do SAP BW (das linhas com qualquer condição de filtro)
- Exclusão de solicitação do SAP BW (de solicitação com falha)

Um destino do Open Hub SAP não é um destino de dados controlados de armazém de dados (em todos os SAP BW pacotes de suporte desde o ano de 2015). Portanto, é possível excluir dados de um cubo sem alterar os dados em que o OHD. Nesse caso, você deve sincronizar novamente os dados do cubo com os dados no ADF, executando as seguintes etapas:

1. Executar uma extração completa no ADF (por meio de um DTP completa no SAP)
2. Excluir todas as linhas da tabela do Open Hub para o DTP Delta
3. Definir o status do DTP Delta para buscadas

Depois disso, extrações de Delta do ADF e todos os próximos DTPs de Delta funcionam conforme o esperado.

Você pode definir o status do DTP Delta para buscadas, executando o DTP Delta manualmente usando a opção a seguir: "*Nenhuma transferência de dados; Status de delta na fonte: Buscadas*".

## <a name="next-steps"></a>Próximas etapas

Avance para o seguinte artigo para saber mais sobre o suporte do conector SAP BW Open Hub: 

> [!div class="nextstepaction"]
>[Conector do SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
