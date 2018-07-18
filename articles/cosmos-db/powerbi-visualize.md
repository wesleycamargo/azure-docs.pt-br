---
title: Tutorial do Power BI para o conector do Azure Cosmos DB | Microsoft Docs
description: Use este tutorial do Power BI para importar o JSON, criar relatórios interessantes e visualizar dados usando o conector do Azure Cosmos DB e do Power BI.
keywords: tutorial do power bi, visualizar dados, conector do power bi
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: sngun
ms.openlocfilehash: 67ea7a9ea1a1be4fd0780f8b8ce22f1a133615e0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615862"
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Tutorial do Power BI para o Azure Cosmos DB: Visualizar dados usando o conector do Power BI
[PowerBI.com](https://powerbi.microsoft.com/) é um serviço online no qual você pode criar e compartilhar painéis e relatórios com dados que são importantes para você e sua organização.  O Power BI Desktop é uma ferramenta dedicada para a criação de relatórios que permite recuperar dados de várias fontes de dados, mesclar e transformar os dados, criar relatórios e visualizações avançados e publicar os relatórios no Power BI.  Com a última versão do Power BI Desktop, é possível conectar-se à conta do Microsoft Azure Cosmos DB por meio do conector do Azure Cosmos DB para o Power BI.   

Neste tutorial do Power BI, explicaremos as etapas para conectar uma conta do Azure Cosmos DB no Power BI Desktop, navegar até uma coleção onde queremos extrair os dados usando o Navegador, transformar dados JSON em formato tabular usando o Editor de Consultas do Power BI Desktop e compilar e publicar um relatório para o PowerBI.com.

Depois de concluir este tutorial do Power BI, você estará apto a responder às seguintes perguntas:  

* Como posso criar relatórios com dados do Azure Cosmos DB usando o Power BI Desktop?
* Como posso conectar a uma conta do Azure Cosmos DB no Power BI Desktop?
* Como posso recuperar dados de uma coleção no Power BI Desktop?
* Como posso transformar dados JSON aninhados no Power BI Desktop?
* Como posso publicar e compartilhar meus relatórios no PowerBI.com?

> [!NOTE]
> O conector do Power BI para o Azure Cosmos DB conecta-se ao Power BI Desktop para extração e transformação de dados. Os relatórios criados no Power BI Desktop podem ser publicados no PowerBI.com. Extração direta e transformação de dados do Azure Cosmos DB não podem ser executadas no PowerBI.com. 

> [!NOTE]
> Para conectar o Azure Cosmos DB ao Power BI usando a API do MongoDB, você deve usar o [Driver ODBC do Simba MongoDB](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>pré-requisitos
Antes de seguir as instruções neste tutorial do Power BI, certifique-se de ter acesso aos recursos seguintes:

* [A versão mais recente do Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Acesse nossa conta de demonstração ou os dados na conta do Azure Cosmos DB.
  * A conta de demonstração é populada com os dados do vulcão mostrados neste tutorial. Essa conta de demonstração não está vinculada por nenhum SLA e se destina apenas a fins de demonstração.  Reservamos o direito de fazer modificações a essa conta de demonstração, incluindo, entre outros, encerramento da conta, alteração da chave, restrição do acesso, alteração e exclusão dos dados, a qualquer momento, sem aviso prévio ou justificativa.
    * URL: https://analytics.documents.azure.com
    * Chave somente leitura: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * Ou então, para criar sua própria conta, consulte [Criar uma conta de banco de dados do Azure Cosmos DB usando o portal do Azure](https://azure.microsoft.com/documentation/articles/create-account/). Em seguida, para obter dados de vulcão de exemplo semelhantes aos que são usados neste tutorial (mas que não contêm os blocos GeoJSON), visite o [site da NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) e, depois, importe os dados usando a [ferramenta de migração de dados do Azure Cosmos DB](import-data.md).

Para compartilhar seus relatórios no PowerBI.com, você deve ter uma conta no PowerBI.com.  Para saber mais sobre Power BI Gratuito e Power BI Pro, visite [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Vamos começar
Neste tutorial, vamos imaginar que você é um geólogo que estuda os vulcões em todo o mundo.  Os dados de vulcão são armazenados em uma conta do Azure Cosmos DB e os documentos JSON são semelhantes ao exemplo mostrado no documento de exemplo a seguir.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Você deseja recuperar os dados de vulcão da conta do Azure Cosmos DB e visualizar os dados em um relatório interativo do Power BI como o mostrado no relatório a seguir.

![Ao concluir este tutorial do Power BI com o conector do Power BI, você poderá visualizar dados com o relatório sobre vulcões do Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Pronto para experimentar? Vamos começar.

1. Execute o Power BI Desktop em sua estação de trabalho.
2. Depois de iniciar o Power BI Desktop, uma tela de *Boas-Vindas* é exibida.
   
    ![Tela de boas-vindas do Power BI Desktop — conector do Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Você pode **Obter Dados**, ver as **Fontes Recentes** ou **Abrir Outros Relatórios** diretamente na tela de *Boas-vindas*.  Clique no X no canto superior direito para fechar a tela. A exibição de **Relatório** do Power BI Desktop é exibida.
   
    ![Exibição de relatório do Power BI Desktop — conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Selecione a faixa de opções **Página Inicial** e clique em **Obter Dados**.  A janela **Obter Dados** deve ser exibida.
5. Clique em **Azure**, selecione **Azure Cosmos DB (Beta)** e clique em **Conectar**. 

    ![Obtenção de dados do Power BI Desktop — conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Na página de **Visualizar Conector**, clique em **Continuar**. A janela do **Azure Cosmos DB** é exibida.
7. Especifique a URL do ponto de extremidade da conta do Azure Cosmos DB da qual você deseja recuperar os dados, conforme mostrado abaixo e clique em **OK**. Para usar sua própria conta, você pode recuperar a URL na caixa de URI na folha **[Chaves](manage-account.md#keys)** do portal do Azure. Para usar a conta de demonstração, digite `https://analytics.documents.azure.com` para a URL. 
   
    Deixe o nome do banco de dados, o nome da coleção e a instrução SQL em branco, já que esses campos são opcionais.  Em vez disso, usaremos o Navegador para selecionar o Banco de Dados e a Coleção para identificar a origem dos dados.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Janela Conexão da Área de Trabalho](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Se estiver se conectando a este ponto de extremidade pela primeira vez, você será solicitado a fornecer a chave de conta. Para sua própria conta, recupere a chave na caixa **Chave Primária** na folha **[Chaves Somente Leitura](manage-account.md#keys)** do portal do Azure. Para a conta de demonstração, a chave é `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Insira a chave apropriada e, em seguida, clique em **Conectar**.
   
    Recomendamos que você use a chave somente leitura ao criar relatórios.  Isso evita a exposição desnecessária da chave mestra a potenciais riscos de segurança. A chave somente leitura está disponível na folha [Chaves](manage-account.md#keys) do portal do Azure, ou você pode usar as informações da conta de demonstração fornecidas acima.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Chave de Conta](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Se você receber um erro dizendo "O banco de dados especificado não foi encontrado." consulte as etapas da solução alternativa neste [Problema do Power BI](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Quando a conta é conectada com êxito, o painel **Navegador** é exibido.  O **Navegador** mostra uma lista de bancos de dados na conta.
10. Clique e expanda no banco de dados de onde originam os dados do relatório e, se estiver usando a conta de demonstração, selecione **volcanodb**.   
11. Agora, selecione uma coleção que contenha os dados a serem recuperados. Se você estiver usando a conta de demonstração, selecione **volcano1**.
    
    O painel Visualização mostra uma lista dos itens de **Registro** .  Um Documento é representado como um tipo **Registro** no Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento é também um **Registro**.
    
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Janela Navegador](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Clique em **Editar** para iniciar o Editor de Consultas em uma nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Nivelando e transformando documentos JSON
1. Altere para a janela do Editor de Consultas do Power BI, onde a coluna **Documento** no painel central.
   ![Editor de Consultas do Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Clique no expansor no lado direito do cabeçalho da coluna **Documento** .  O menu de contexto com uma lista de campos será exibido.  Selecione os campos que você precisa para o relatório, por exemplo, Nome do Vulcão, País, Região, Localização, Elevação, Tipo, Status e Última Erupção. Desmarque a caixa **Usar nome original da coluna como prefixo** e, em seguida, clique em **OK**.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Expandir documentos](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. O painel central exibe uma visualização do resultado com os campos selecionados.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Nivelar resultados](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Em nosso exemplo, a propriedade Location é um bloco GeoJSON em um documento.  Como você pode ver, o Local é representado como um tipo **Registro** no Power BI Desktop.  
5. Clique no expansor no lado direito do cabeçalho da coluna Document.Location.  O menu de contexto com os campos tipo e coordenadas é exibido.  Vamos selecionar o campo de coordenadas, assegurar de que **Usar nome original da coluna como prefixo** não está selecionado e clicar em **OK**.
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Registro de localização](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Agora, o painel central mostra uma coluna de coordenadas do tipo **Lista** .  Como mostrado no início do tutorial, os dados GeoJSON neste tutorial são do tipo Ponto com valores de Latitude e Longitude gravados na matriz de coordenadas.
   
    O elemento coordinates[0] representa a Longitude, enquanto que coordinates[1] representa a Latitude.
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Lista de coordenadas](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Para nivelar a matriz de coordenadas, crie uma **Coluna Personalizada** chamada LatLong.  Selecione a faixa de opções **Adicionar Coluna** e clique em **Coluna Personalizada**.  A janela **Coluna Personalizada** é exibida.
8. Forneça um nome para a nova coluna, por exemplo, LatLong.
9. Em seguida, especifique a fórmula personalizada para a nova coluna.  Para nosso exemplo, os valores de Latitude e Longitude, separados por uma vírgula, serão concatenados com a seguinte fórmula, como mostrado abaixo: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Clique em **OK**.
   
    Para obter mais informações sobre o DAX (Data Analysis Expressions), incluindo as funções DAX, visite [Noções básicas do DAX no Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Adicionar Coluna Personalizada](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Agora, o painel central mostra as novas colunas LatLong preenchidas com os valores.
    
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Coluna LatLong personalizada](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se você receber um erro na nova coluna, verifique as etapas aplicadas nas Configurações de Consulta correspondem à figura abaixo:
    
    ![As etapas aplicadas devem ser Origem, Navegação, Document.Location Expandido, Personalizado Adicionado](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se as etapas são diferentes, exclua as etapas adicionais e tente adicionar a coluna personalizada novamente. 

11. Clique em **Fechar e Aplicar** para salvar o modelo de dados.
    
    ![Tutorial do Power BI para o conector do Azure Cosmos DB para Power BI – Fechar e Aplicar](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Compilar os relatórios
A exibição de Relatório do Power BI Desktop é aquela em que você pode começar a criar relatórios para visualizar dados.  Você pode criar relatórios arrastando e soltando campos na tela **Relatório** .

![Exibição de relatório do Power BI Desktop — conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Na exibição de Relatório, você deve encontrar:

1. O painel **Campos** é onde você pode ver uma lista de modelos de dados com campos que podem ser utilizados para os relatórios.
2. O painel **Visualizações** . Um relatório pode conter uma ou várias visualizações.  Selecione os tipos de elementos visuais que atendem às suas necessidades no painel **Visualizações** .
3. A tela **Relatório** é onde você cria os elementos visuais do relatório.
4. A página **Relatório** . Você pode adicionar várias páginas do relatório no Power BI Desktop.

O exemplo a seguir mostra as etapas básicas da criação de um relatório interativo simples da exibição de Mapa.

1. Para nosso exemplo, criaremos uma exibição de mapa mostrando o local de cada vulcão.  No painel **Visualizações** , clique no tipo de elemento visual Mapa, como destacado na captura de tela acima.  Você deverá ver o tipo de elemento visual Mapa pintado na tela **Relatório** .  O painel **Visualização** também deve exibir um conjunto de propriedades relacionadas ao tipo de elemento visual Mapa.
2. Agora, arraste e solte o campo LatLong do painel **Campos** para a propriedade **Location** no painel **Visualizações**.
3. Em seguida, arraste e solte o campo Nome do Vulcão para a propriedade **Legend** .  
4. Em seguida, arraste e solte o campo Elevação para a propriedade **Size** .  
5. Agora você deverá ver o elemento visual Mapa mostrando um conjunto de bolhas que indica o local de cada vulcão com o tamanho da bolha correlacionando à elevação do vulcão.
6. Agora você criou um relatório básico.  É possível personalizar ainda mais o relatório adicionando mais visualizações.  Em nosso caso, adicionamos uma segmentação de dados do Tipo de Vulcão para tornar o relatório interativo.  
   
    ![Captura de tela do relatório final do Power BI Desktop após a conclusão do tutorial do Power BI para o Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. No menu Arquivo, clique em **Salvar** e salve o arquivo como PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publicar e compartilhar seu relatório
Para compartilhar seu relatório, você deve ter uma conta no PowerBI.com.

1. No Power BI Desktop, clique na faixa de opções **Página Inicial** .
2. Clique em **Publicar**.  Você será solicitado a inserir o nome de usuário e senha da conta do PowerBI.com.
3. Depois que a credencial é autenticada, o relatório é publicado no destino selecionado.
4. Clique em **Abrir 'PowerBITutorial.pbix' no Power BI** para ver e compartilhar seu relatório no PowerBI.com.
   
    ![Publicando no Power BI Sucesso! Abrir tutorial no Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Criar um painel no PowerBI.com
Agora que você tem um relatório, vamos compartilhá-lo no PowerBI.com

Quando você publica o relatório do Power BI Desktop no PowerBI.com, ele gera um **Relatório** e um **Conjunto de Dados** em seu locatário PowerBI.com. Por exemplo, após publicar um relatório chamado **PowerBITutorial** no PoweBI.com, você verá PowerBITutorial nas seções **Relatórios** e **Conjuntos de Dados** no PowerBI.com.

   ![Captura de tela dos novos Relatório e Conjunto de Dados no PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Para criar um painel compartilhável, clique no botão **Pin Live página** no seu relatório em PowerBI.com.

   ![Captura de tela dos novos Relatório e Conjunto de Dados no PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Siga as instruções em [Fixar um bloco de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo painel. 

Você também pode fazer modificações ad hoc ao relatório antes de criar um painel. No entanto, é recomendável que você use o Power BI Desktop para executar as modificações e republicar o relatório no PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o Power BI, confira [Introdução ao Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para saber mais sobre o Azure Cosmos DB, consulte a [página de aterrissagem de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

