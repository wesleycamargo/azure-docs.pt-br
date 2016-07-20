<properties
   pageTitle="Microsoft Power BI Embedded - inserir um relatório do Power BI com um IFrame"
   description="Microsoft Power BI Embedded - código essencial para integrar um relatório em seu aplicativo, como autenticar com o token do aplicativo Power BI Embedded, como obter relatórios"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# Inserir um relatório do Power BI com um IFrame
Este artigo mostra o código essencial para usar a API REST do **Power BI Embedded**, tokens do aplicativo, um IFrame e JavaScript para integrar, ou inserir um relatório em seu aplicativo.

Em [Introdução ao Microsoft Power BI Embedded](power-bi-embedded-get-started.md), você aprenderá a configurar uma **Coleção de Espaços de Trabalho** para manter um ou mais **Espaços de Trabalho** para o conteúdo do relatório. Em seguida, em [Introdução ao exemplo do Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md), você importa um relatório para um **Espaço de Trabalho**.

Neste artigo, explicaremos as etapas para inserir um relatório em seu aplicativo. Para acompanhar este artigo, você deve baixar o exemplo [Integrar um relatório com um IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) no GitHub. Este exemplo é um aplicativo de formulário da Web ASP.NET simples com o objetivo de ilustrar os códigos C# e JavaScript essenciais necessários para integrar um relatório. Para obter um exemplo mais avançado que utiliza o padrão de design MVC (Model-View-Controller) para integrar um relatório, confira o [Aplicativo Web do painel de amostra](http://go.microsoft.com/fwlink/?LinkId=761493) no GitHub.

Aqui estão as etapas para integrar um relatório:

- Etapa 1: [obter um relatório em um espaço de trabalho](#GetReport). Nesta etapa, você usa um fluxo de token do aplicativo para obter um token de acesso para chamar a operação REST [Obter Relatórios](https://msdn.microsoft.com/library/mt711510.aspx). Depois de obter um relatório da lista **Obter Relatórios**, você irá inseri-lo em um aplicativo com um elemento **IFrame**.
- Etapa 2: [inserir um relatório em um aplicativo](#EmbedReport). Nesta etapa, você usa um token de inserção para um relatório, um JavaScript e um IFrame para integrar ou inserir um relatório em um aplicativo Web.

Se você quiser executar o exemplo para ver como integrar um relatório, baixe o exemplo [Integrar um relatório com um IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) no GitHub e defina três configurações Web.config:

- **AccessKey**: uma **AccessKey** é usada para gerar um JWT (Token Web JSON) usado para obter relatórios e inserir um relatório. Para saber como obter uma **AccessKey**, consulte [Introdução ao Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceName**: para saber como obter um **WorkspaceName**, consulte [Introdução ao Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceId**: para saber como obter uma **WorkspaceId**, consulte [Introdução ao Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

As próximas seções mostram o código necessário para integrar um relatório.

<a name="GetReport"/>
## Obter um relatório em um espaço de trabalho

Para integrar um relatório em um aplicativo, você precisa de uma **ID** e **embedUrl** do relatório. Para obter uma **ID** e **embedUrl** do relatório, você chama a operação REST [Obter Relatórios](https://msdn.microsoft.com/library/mt711510.aspx) e escolhe um relatório na lista JSON. Em [Inserir relatório em um aplicativo](#EmbedReport), você usa uma **ID** e **embedUrl** do relatório para inserir o relatório em seu aplicativo.

### Resposta JSON para obter relatórios
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Para chamar a operação REST [Obter Relatórios](https://msdn.microsoft.com/library/mt711510.aspx), você usa um token do aplicativo. Para saber mais sobre o fluxo do token do aplicativo, confira [Sobre o fluxo do token do aplicativo no Power BI Embedded](power-bi-embedded-app-token-flow.md). O código a seguir descreve como obter uma lista JSON de relatórios. Para inserir um relatório, confira [Inserir relatório em um aplicativo](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## Inserir um relatório em um aplicativo

Antes de poder inserir um relatório em seu aplicativo, é necessário um token de inserção para um relatório. Esse token é semelhante a um token do aplicativo usado para chamar as operações REST **Power BI Embedded**, mas é gerado para um recurso do relatório, em vez de um recurso REST. A seguir está o código para obter um token do aplicativo para um relatório. Para usar o token do aplicativo de relatório, confira [Inserir um relatório em seu aplicativo](#EmbedReportJS).

<a name="EmbedReportToken"/>
### Obter um token do aplicativo para um relatório

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Inserir um relatório em seu aplicativo

Para inserir um relatório do **Power BI** no aplicativo, use um IFrame e um código JavaScript. A seguir está um IFrame de exemplo e o código do JavaScript para inserir um relatório. Para ver todo o código de exemplo para inserir um relatório, confira o exemplo [Integrar um relatório com um IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) no GitHub.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Depois de ter um relatório inserido em seu aplicativo, você poderá filtrar o relatório. A próxima seção mostra como filtrar um relatório usando uma sintaxe de URL.

## Filtrar um relatório

Você pode filtrar um relatório inserido usando uma sintaxe de URL. Para fazer isso, adicione um parâmetro de cadeia de caracteres de consulta à sua URL src iFrame com o filtro especificado. Você pode **Filtrar por um valor** e **Ocultar o Painel de Filtro**.


**Filtrar por um valor**

Para filtrar por um valor, você deverá usar uma sintaxe de consulta **$filter** com um operador **eq** da seguinte maneira:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Por exemplo, você pode filtrar onde está 'Fernandes' na cadeia de armazenamento. A parte de filtro da URL teria esta aparência:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} não pode incluir espaços ou caracteres especiais. O {fieldValue} aceita um único valor categórico.

**Ocultar o Painel de Filtro**

Para ocultar o **Painel de Filtro**, adicione **filterPaneEnabled** à cadeia de consulta de relatório da seguinte maneira:

```
&filterPaneEnabled=false
```

## Recursos adicionais

Neste artigo, você foi apresentado ao código para integrar um relatório **Power BI** em seu aplicativo. Não deixe de conferir estes exemplos adicionais no GitHub:

- [Integrar um relatório com um exemplo de IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Aplicativo Web do painel de exemplo](http://go.microsoft.com/fwlink/?LinkId=761493)

## Veja também
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)

<!---HONumber=AcomDC_0713_2016-->