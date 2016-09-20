<properties
   pageTitle="Comece com uma amostra"
   description="Power BI Embedded, use o SDK para adicionar relatórios interativos do Power BI a seu aplicativo de business intelligence"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/14/2016"
   ms.author="mblythe"/>

# Introdução ao exemplo do Power BI Embedded

Com o **Microsoft Power BI Embedded**, você pode integrar relatórios do Power BI diretamente a seus aplicativos móveis ou Web. Neste artigo, apresesntaremos a você o exemplo de introdução do **Power BI Embedded**.

Antes de continuarmos, convém salvar os recursos a seguir. Eles o ajudarão ao integrar relatórios do Power BI ao aplicativo de exemplo e a seus próprios aplicativos também.

 -	[Aplicativo Web do painel de exemplo](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Referência da API do Power BI Embedded](https://msdn.microsoft.com/library/mt711493.aspx)
 -	[SDK do .NET do Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=746472) (disponível via NuGet)



> [AZURE.NOTE] Antes que você possa configurar e executar o exemplo de introdução do Power BI Embedded, você precisa criar pelo menos uma **Coleção de Espaço de Trabalho** em sua assinatura do Azure. Para aprender a criar um **Coleção de Espaço de Trabalho** no Portal do Azure, consulte [Introdução ao Power BI Embedded](power-bi-embedded-get-started.md).

## Configurar o aplicativo de exemplo

Vamos orientar você sobre como configurar seu ambiente de desenvolvimento do Visual Studio para acessar os componentes necessários para executar o aplicativo de exemplo.

1. Baixe e descompacte a amostra [Power BI Embedded - Integrar um relatório em um aplicativo Web](http://go.microsoft.com/fwlink/?LinkId=761493) no GitHub.

2. Abra **PowerBI-embedded.sln** no Visual Studio. Talvez seja necessário executar o comando **Update-Package** no Console do Gerenciador de Pacotes do NuGET para atualizar os pacotes usados nesta solução.

3. Compilar a solução.

4. Execute o aplicativo de console **ProvisionSample**. No aplicativo de console de exemplo, você provisiona um espaço de trabalho e importa um arquivo PBIX.

5. Para provisionar um novo **Espaço de trabalho**, selecione a opção 5, **Provisionar um novo espaço de trabalho em uma coleção de espaços de trabalho existente**.

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. Insira o nome da sua **Coleção de Espaços de Trabalho** e a sua **Tecla de Acesso**. Você pode obtê-los no **Portal do Azure**. Para saber mais sobre como obter sua **Tecla de Acesso**, consulte [Exibir Chaves de Acesso de API do Power BI](power-bi-embedded-get-started-sample.md#view-access-keys) na Introdução ao Microsoft Power BI Embedded.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. Copie e salve a **ID do Espaço de Trabalho** recém-criada a usar posteriormente neste artigo. Após a **ID do Espaço de Trabalho** ser criada, você poderá encontrá-la no **Portal do Azure**.

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. Para importar um arquivo PBIX para o **Espaço de Trabalho**, selecione a opção **6. Importe o arquivo da Área de Trabalho PBIX em um espaço de trabalho existente**. Se você não tiver um arquivo PBIX prático, poderá baixar o [PBIX de Exemplo de Análise de Vendas](http://go.microsoft.com/fwlink/?LinkID=780547).

9. Se solicitado, insira um nome amigável para o **Conjunto de Dados**.

Você verá uma resposta semelhante a essa:

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] Se o arquivo PBIX contiver quaisquer conexões de consulta direta, execute a opção 7 para atualizar as cadeias de conexão.

Neste ponto, um relatório PBIX do Power BI é importado para o seu **Espaço de Trabalho**. Agora, vejamos como executar aplicativo Web de exemplo de introdução do **Power BI Embedded**.

## Executar o aplicativo Web de exemplo

O aplicativo Web de exemplo é um painel de exemplo que renderiza relatórios importados para o seu **Espaço de Trabalho**. Aqui está como configurar o aplicativo Web de exemplo.

1. Na solução **PowerBI-embedded** do Visual Studio, clique com o botão direito do mouse no aplicativo Web **EmbedSample** e escolha **Definir como projeto de Inicialização**.
2. Em **web.config**, no aplicativo Web **EmbedSample**, edite o nome de **appSettings**: **AccessKey**, **WorkspaceCollection**, e a **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Execute o aplicativo Web **EmbedSample**.

Depois que você executar o aplicativo Web **EmbedSample**, o painel de navegação esquerdo deve conter um menu **Relatórios**. Para exibir o relatório que você importou, expanda **Relatórios** e clique em um relatório. Se você tivesse importado o [PBIX de Exemplo de Análise de Vendas](http://go.microsoft.com/fwlink/?LinkID=780547), o aplicativo Web de exemplo teria esta aparência:

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

Depois de clicar em um relatório, o aplicativo Web **EmbedSample** deve ter essa aparência:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)


## Explorar o código de exemplo
A amostra do **Microsoft Power BI Embedded** é um aplicativo Web de painel de exemplo que mostra como integrar relatórios do **Power BI** em seu aplicativo. Ele usa um MVC (Model-View-Controller) para demonstrar as práticas recomendadas. Esta seção destaca as partes do código de exemplo que você pode explorar dentro da solução de aplicativo Web **PowerBI-embedded**. O padrão Model-View-Controller (MVC) separa a modelagem de domínio, a apresentação e as ações com base na entrada do usuário em três classes separadas: Modelo, Exibição e Controle. Para saber mais sobre o MVC, consulte [Aprenda sobre o ASP.NET](http://www.asp.net/mvc).

O código de exemplo do **Microsoft Power BI Embedded** é separado conforme demonstrado a seguir. Cada seção inclui o nome do arquivo na solução PowerBI-embedded.sln para que você possa localizar facilmente o código no exemplo.

> [AZURE.NOTE] Esta seção é um resumo do código de exemplo que mostra como o código foi escrito. Para ver o exemplo completo, carregue a solução PowerBI-embedded.sln no Visual Studio.

### Modelo
O exemplo conta com um **ReportsViewModel** e um **ReportViewModel**.

**ReportsViewModel.cs**: representa relatórios do Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: representa um relatório do Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Cadeia de conexão
A cadeia de conexão deve estar no seguinte formato:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Usar atributos comuns de servidor e de banco de dados falhará. Por exemplo: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### Visualizar
A **Exibição** gerencia a exibição de **Relatórios** do Power BI e de um **Relatório** do Power BI.

**Reports.cshtml**: faça a iteração de **Model.Reports** para criar um **ActionLink**. O **ActionLink** é composto da seguinte maneira:

|Parte|Descrição
|---|---
|Title| Nome do Relatório.
|QueryString| Um link para a ID de Relatório.

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: defina o **Model.AccessToken** e a expressão Lambda para **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs**: cria um PowerBIClient que passa um **token de aplicativo**. Um JWT (Token Web JSON) é gerado da **Chave de Assinatura** para obter as **Credenciais**. As **Credenciais** são usadas para criar uma instância de **PowerBIClient**. Quando tiver uma instância de **PowerBIClient**, você poderá chamar GetReports() e GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrar um relatório em seu aplicativo

Quando tiver um **Relatório**, você deverá usar um **IFrame** para inserir o **Relatório** do Power BI. Veja um trecho de código do powerbi.js na amostra do **Microsoft Power BI Embedded**.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)


## Filtrar relatórios inseridos no seu aplicativo

Você pode filtrar um relatório inserido usando uma sintaxe de URL. Para fazer isso, adicione um parâmetro de cadeia de caracteres de consulta **$filter** com um operador **eq** à sua URL de origem do iFrame com o filtro especificado. Aqui está a sintaxe de consulta de filtro:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} não pode incluir espaços ou caracteres especiais. O {fieldValue} aceita um único valor categórico.


## Consulte também

- [Cenários comuns do Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Autenticando e autorizando com o Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0907_2016-->