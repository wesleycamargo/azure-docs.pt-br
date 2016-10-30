<properties
    pageTitle="Tutorial do Power BI para o conector do Banco de Dados de Documentos | Microsoft Azure"
    description="Use este tutorial do Power BI para importar o JSON, criar relatórios interessantes e visualizar dados usando o conector do Banco de Dados de Documentos e do Power BI."
    keywords="tutorial do power bi, visualizar dados, conector do power bi"
    services="documentdb"
    authors="h0n"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="hawong"/>


# <a name="power-bi-tutorial-for-documentdb:-visualize-data-using-the-power-bi-connector"></a>Tutorial do Power BI para o Banco de Dados de Documentos: visualizar dados usando o conector do Power BI

[PowerBI.com](https://powerbi.microsoft.com/) é um serviço online no qual você pode criar e compartilhar painéis e relatórios com dados que são importantes para você e sua organização.  O Power BI Desktop é uma ferramenta dedicada para a criação de relatórios que permite recuperar dados de várias fontes de dados, mesclar e transformar os dados, criar relatórios e visualizações avançados e publicar os relatórios no Power BI.  Com a versão mais recente do Power BI Desktop, agora você pode se conectar à sua conta do Banco de Dados de Documentos por meio do conector do Banco de Dados de Documentos para o Power BI.   

Neste tutorial do Power BI, explicaremos as etapas para conectar a uma conta do DocumentDB no Power BI Desktop, navegaremos até uma coleção em que desejamos extrair os dados usando o Navegador, transformaremos os dados JSON em formato tabular usando o Editor de Consultas do Power BI Desktop, além de criarmos e publicarmos um relatório no PowerBI.com.

Depois de concluir este tutorial do Power BI, você estará apto a responder às seguintes perguntas:  

-   Como posso criar relatórios com dados do Banco de Dados de Documentos usando o Power BI Desktop?
-   Como posso me conectar a uma conta do Banco de Dados de Documentos no Power BI Desktop?
-   Como posso recuperar dados de uma coleção no Power BI Desktop?
-   Como posso transformar dados JSON aninhados no Power BI Desktop?
-   Como posso publicar e compartilhar meus relatórios no PowerBI.com?

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções neste tutorial do Power BI, certifique-se de ter o seguinte:

- [A versão mais recente do Power BI Desktop](https://powerbi.microsoft.com/desktop).
- Acesse nossa conta ou dados de demonstração em sua conta do Banco de Dados de Documentos do Azure.
    - A conta de demonstração é populada com os dados do vulcão mostrados neste tutorial. Essa conta de demonstração não está vinculada por nenhum SLA e se destina apenas a fins de demonstração.  Reservamos o direito de fazer modificações a essa conta de demonstração, incluindo, entre outros, encerramento da conta, alteração da chave, restrição do acesso, alteração e exclusão dos dados, a qualquer momento, sem aviso prévio ou justificativa.
        - URL: https://analytics.documents.azure.com
        - Chave somente leitura: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
    - Ou então, para criar sua própria conta, veja [Criar uma conta de banco de dados do DocumentDB usando o portal do Azure](https://azure.microsoft.com/documentation/articles/documentdb-create-account/). Em seguida, para obter dados de vulcão de exemplo semelhantes aos que são usados neste tutorial (mas que não contêm os blocos GeoJSON), confira o [site da NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) e importe os dados usando a [ferramenta de migração de dados do DocumentDB](https://azure.microsoft.com/documentation/articles/documentdb-import-data/).


Para compartilhar seus relatórios no PowerBI.com, você deve ter uma conta no PowerBI.com.  Para saber mais sobre o Power BI Gratuito e o Power BI Pro, visite [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="let's-get-started"></a>Vamos começar
Neste tutorial, vamos imaginar que você é um geólogo que estuda os vulcões em todo o mundo.  Os dados de vulcão são armazenados em uma conta do Banco de Dados de Documentos e os documentos JSON são semelhantes ao exemplo mostrado abaixo.

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

Você deseja recuperar os dados de vulcão da conta do Banco de Dados de Documentos e visualizar dados em um relatório interativo do Power BI como o mostrado abaixo.

![Ao concluir este tutorial do Power BI com o conector do Power BI, você poderá visualizar dados com o relatório sobre vulcões do Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

Pronto para experimentar? Vamos começar.


1. Execute o Power BI Desktop em sua estação de trabalho.
2. Depois de iniciar o Power BI Desktop, uma tela de *Boas-Vindas* é exibida.

    ![Tela de boas-vindas do Power BI Desktop — conector do Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_welcome.png)

3. Você pode **Obter Dados**, ver as **Fontes Recentes** ou **Abrir Outros Relatórios** diretamente na tela de *Boas-vindas*.  Clique no X no canto superior direito para fechar a tela. A exibição de **Relatório** do Power BI Desktop é exibida.

    ![Exibição de relatório do Power BI Desktop — conector do Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview.png)

4. Selecione a faixa de opções **Página Inicial** e clique em **Obter Dados**.  A janela **Obter Dados** deve ser exibida.

5. Clique em **Azure**, selecione **Microsoft Azure DocumentDB (Beta)** e clique em **Conectar**.  A janela **Conexão do Banco de Dados de Documentos do Microsoft Azure** deve ser exibida.

    ![Obtenção de dados do Power BI Desktop — conector do Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbigetdata.png)

6. Especifique a URL do ponto de extremidade da conta do Banco de Dados de Documentos da qual você gostaria de recuperar os dados, como mostrado abaixo, e clique em **OK**. Você pode recuperar a URL na caixa de URI na folha **[Chaves](documentdb-manage-account.md#keys)** do portal do Azure ou usar a conta de demonstração; nesse caso, a URL é `https://analytics.documents.azure.com`. 

    Deixe o nome do banco de dados, o nome da coleção e a instrução SQL em branco, já que esses campos são opcionais.  Em vez disso, usaremos o Navegador para selecionar o Banco de Dados e a Coleção para identificar a origem dos dados.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — janela Conexão da Área de Trabalho](./media/documentdb-powerbi-visualize/power_bi_connector_pbiconnectwindow.png)

7. Se estiver se conectando a este ponto de extremidade pela primeira vez, você será solicitado a fornecer a chave de conta.  Você pode recuperar a chave na caixa **Chave Primária** na folha **[Chaves Somente Leitura](documentdb-manage-account.md#keys)** do portal do Azure ou usar a conta de demonstração; nesse caso, a chave é `RcEBrRI2xVnlWheejXncHId6QRcKdCGQSW6uSUEgroYBWVnujW3YWvgiG2ePZ0P0TppsrMgscoxsO7cf6mOpcA==`. Insira a chave de conta e clique em **Conectar**.

    Recomendamos que você use a chave somente leitura ao criar relatórios.  Isso impedirá a exposição desnecessária da chave mestra a possíveis riscos de segurança. A chave somente leitura está disponível na folha [Chaves](documentdb-manage-account.md#keys) do portal do Azure, ou você pode usar as informações da conta de demonstração fornecidas acima.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — chave da conta](./media/documentdb-powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)

8. Quando a conta for conectada com êxito, o **Navegador** será exibido.  O **Navegador** mostrará uma lista de bancos de dados na conta.
9. Clique e expanda o banco de dados de onde os dados para o relatório virão. Se você estiver usando a conta de demonstração, selecione **volcanodb**.   

10. Agora, selecione uma coleção da qual você recuperará os dados. Se você estiver usando a conta de demonstração, selecione **volcano1**.

    O painel Visualização mostra uma lista dos itens de **Registro** .  Um Documento é representado como um tipo **Registro** no Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento é também um **Registro**.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — janela Navegador](./media/documentdb-powerbi-visualize/power_bi_connector_pbinavigator.png)

11. Clique em **Editar** para iniciar o Editor de Consultas para que possamos transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Nivelando e transformando documentos JSON
1. No Editor de Consultas do Power BI, você deve ver uma coluna **Documento** no painel central.
![Editor de Consultas do Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditor.png)

2. Clique no expansor no lado direito do cabeçalho da coluna **Documento** .  O menu de contexto com uma lista de campos será exibido.  Selecione os campos necessários para o relatório, por exemplo, Nome do Vulcão, País, Região, Local, Elevação, Tipo, Status e Última Erupção Conhecida e clique em **OK**.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — expandir documentos](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)

3. O painel central exibirá uma visualização do resultado com os campos selecionados.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — nivelar resultados](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflatten.png)

4. Em nosso exemplo, a propriedade Location é um bloco GeoJSON em um documento.  Como você pode ver, o Local é representado como um tipo **Registro** no Power BI Desktop.  
5. Clique no expansor no lado direito do cabeçalho da coluna Local.  O menu de contexto com os campos de tipo e coordenadas será exibido.  Vamos selecionar o campo de coordenadas e clicar em **OK**.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — registro de local](./media/documentdb-powerbi-visualize/power_bi_connector_pbilocationrecord.png)

6. Agora, o painel central mostra uma coluna de coordenadas do tipo **Lista** .  Como mostrado no início do tutorial, os dados GeoJSON neste tutorial são do tipo Ponto com valores de Latitude e Longitude gravados na matriz de coordenadas.

    O elemento coordinates[0] representa a Longitude, enquanto que coordinates[1] representa a Latitude.
    ![Tutorial do Power BI para conector do Power BI do DocumentDB — lista de coordenadas](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)

7. Para nivelar a matriz de coordenadas, criaremos uma **Coluna Personalizada** chamada LatLong.  Selecione a faixa de opções **Adicionar Coluna** e clique em **Adicionar Coluna Personalizada**.  A janela **Adicionar Coluna Personalizada** deve ser exibida.

8. Forneça um nome para a nova coluna, por exemplo, LatLong.

9. Em seguida, especifique a fórmula personalizada para a nova coluna.  Para nosso exemplo, os valores de Latitude e Longitude, separados por uma vírgula, serão concatenados com a seguinte fórmula, como mostrado abaixo: `Text.From([Document.Location.coordinates]{1})&","&Text.From([Document.Location.coordinates]{0})`. Clique em **OK**.

    Para obter mais informações sobre o DAX (Data Analysis Expressions), incluindo as funções DAX, visite [Noções básicas do DAX no Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — adicionar coluna personalizada](./media/documentdb-powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Agora, o painel central mostrará a nova coluna LatLong populada com os valores de Latitude e Longitude, separados por uma vírgula.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — coluna LatLong personalizada](./media/documentdb-powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)

    Se você receber um erro na nova coluna, verifique as etapas aplicadas nas Configurações de Consulta correspondem à figura abaixo:

    ![As etapas aplicadas devem ser Origem, Navegação, Document.Location Expandido, Personalizado Adicionado](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-applied-steps.png)

    Se as etapas são diferentes, exclua as etapas adicionais e tente adicionar a coluna personalizada novamente. 

11. Agora concluímos o nivelamento dos dados em formato tabular.  Você pode aproveitar todos os recursos disponíveis no Editor de Consultas para formatar e transformar dados no Banco de Dados de Documentos.  Se você está usando o exemplo, altere o tipo de dados de Elevação para **Número inteiro** mudando o **Tipo de Dados** na faixa de opções **Página Inicial**.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — alterar tipo de coluna](./media/documentdb-powerbi-visualize/power_bi_connector_pbichangetype.png)

12. Clique em **Fechar e Aplicar** para salvar o modelo de dados.

    ![Tutorial do Power BI para conector do Power BI do Banco de Dados de Documentos — fechar e aplicar](./media/documentdb-powerbi-visualize/power_bi_connector_pbicloseapply.png)

## <a name="build-the-reports"></a>Compilar os relatórios
A exibição de Relatório do Power BI Desktop é aquela em que você pode começar a criar relatórios para visualizar dados.  Você pode criar relatórios arrastando e soltando campos na tela **Relatório** .

![Exibição de relatório do Power BI Desktop — conector do Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview2.png)

Na exibição de Relatório, você deve encontrar:

 1. O painel **Campos** é onde você verá uma lista de modelos de dados com campos que podem ser usados para seus relatórios.

 2. O painel **Visualizações** . Um relatório pode conter uma ou várias visualizações.  Selecione os tipos de elementos visuais que atendem às suas necessidades no painel **Visualizações** .

 3. A tela **Relatório** é onde você criará os elementos visuais para seu relatório.

 4. A página **Relatório** . Você pode adicionar várias páginas do relatório no Power BI Desktop.

O exemplo a seguir mostra as etapas básicas da criação de um relatório interativo simples da exibição de Mapa.

1. Para nosso exemplo, criaremos uma exibição de mapa mostrando o local de cada vulcão.  No painel **Visualizações** , clique no tipo de elemento visual Mapa, como destacado na captura de tela acima.  Você deverá ver o tipo de elemento visual Mapa pintado na tela **Relatório** .  O painel **Visualização** também deve exibir um conjunto de propriedades relacionadas ao tipo de elemento visual Mapa.

2. Agora, arraste e solte o campo LatLong do painel **Campos** para a propriedade **Location** no painel **Visualizações**.
3. Em seguida, arraste e solte o campo Nome do Vulcão para a propriedade **Legend** .  

4. Em seguida, arraste e solte o campo Elevação para a propriedade **Size** .  

5. Agora você deverá ver o elemento visual Mapa mostrando um conjunto de bolhas que indica o local de cada vulcão com o tamanho da bolha correlacionando à elevação do vulcão.

6. Agora você criou um relatório básico.  É possível personalizar ainda mais o relatório adicionando mais visualizações.  Em nosso caso, adicionamos uma segmentação de dados do Tipo de Vulcão para tornar o relatório interativo.  

    ![Captura de tela do relatório final do Power BI Desktop mediante conclusão do tutorial do Power BI para Banco de Dados de Documentos](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Publicar e compartilhar seu relatório
Para compartilhar seu relatório, você deve ter uma conta no PowerBI.com.

1. No Power BI Desktop, clique na faixa de opções **Página Inicial** .
2. Clique em **Publicar**.  Você será solicitado a inserir o nome de usuário e a senha da sua conta do PowerBI.com.
3. Depois que a credencial é autenticada, o relatório é publicado no destino selecionado.
4. Clique em **Abrir 'PowerBITutorial.pbix' no Power BI** para ver e compartilhar seu relatório no PowerBI.com.

    ![Publicando no Power BI Sucesso! Abrir tutorial no Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbi.com"></a>Criar um painel no PowerBI.com

Agora que você tem um relatório, vamos compartilhá-lo no PowerBI.com

Quando você publica o relatório do Power BI Desktop no PowerBI.com, ele gera um **Relatório** e um **Conjunto de Dados** em seu locatário PowerBI.com. Por exemplo, após publicar um relatório chamado **PowerBITutorial** no PoweBIi.com, você verá PowerBITutorial nas seções **Relatórios** e **Conjuntos de Dados** no PowerBI.com.

   ![Captura de tela dos novos Relatório e Conjunto de Dados no PowerBI.com](./media/documentdb-powerbi-visualize/documentdb-powerbi-reports-datasets.png)

Para criar um painel compartilhável, clique no botão **Pin Live página** no seu relatório em PowerBI.com.

   ![Captura de tela dos novos Relatório e Conjunto de Dados no PowerBI.com](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-pin-live-tile.png)

Siga as instruções em [Fixar um bloco de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo painel. 

Você também pode fazer modificações ad hoc ao relatório antes de criar um painel. No entanto, é recomendável que você use o Power BI Desktop para executar as modificações e republicar o relatório no PowerBI.com.

## <a name="refresh-data-in-powerbi.com"></a>Atualizar dados no Power BI.com

Há duas maneiras de atualizar os dados: agendamento e ad hoc.

Para uma atualização ad hoc, basta clicar nas reticências (...) ao lado do **Conjunto de Dados**, por exemplo, PowerBITutorial. Você deve ver uma lista de ações, incluindo **Atualizar Agora**. Clique em **Atualizar Agora** para atualizar os dados.

![Captura de tela de Atualizar Agora no PowerBI.com](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-refresh-now.png)

Para uma atualização agendada, faça o seguinte:

1. Clique em **Agendar Atualização** na lista de ações. 
    ![Captura de tela da Atualização de Agenda no PowerBI.com](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-schedule-refresh.png)

2. Na página **Configurações**, expanda **Credenciais da fonte de dados**. 

3. Clique em **Editar credenciais**. 

    A opção Configurar pop-up é exibida. 

4. Insira a chave para se conectar à conta de documentos do conjunto de dados e clique em **Entrar**. 

5. Expanda **Agendar Atualização** e defina a agenda para atualizar o conjunto de dados. 
  
6. Clique em **Aplicar** para concluir a configuração da atualização agendada.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o Power BI, confira [Introdução ao Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
- Para saber mais sobre o DocumentDB, confira a [página de documentação do DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).



<!--HONumber=Oct16_HO2-->


