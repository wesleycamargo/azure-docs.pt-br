---
title: Como usar o Power BI Embedded com REST | Microsoft Docs
description: 'Saiba como usar o Power BI Embedded com o REST  '
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="how-to-use-power-bi-embedded-with-rest"></a>Como usar o Power BI Embedded com REST
## <a name="power-bi-embedded:-what-it-is-and-what-it's-for"></a>Power BI Embedded: o que é e para que serve
Uma visão geral do Power BI Embedded é descrita no [site oficial do Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/), mas vamos dar uma rápida olhada antes de adentrarmos nos detalhes sobre como usá-lo com o REST.

Ele é realmente bem simples. Muitas vezes, um ISV quer usar as visualizações dinâmicas de dados do [Power BI](https://powerbi.microsoft.com) em seu próprio aplicativo como blocos de construção da interface de usuário.

Mas, você sabe, inserir relatórios ou blocos do Power BI em sua página da Web já é possível sem o serviço do Azure Power BI Embedded, usando a **API do Power BI**. Quando quiser compartilhar relatórios na mesma organização, você pode inserir os relatórios com a autenticação do Azure AD. O usuário que exibe os relatórios deve fazer logon usando sua própria conta do Azure AD. Quando quiser compartilhar relatórios com todos os usuários (incluindo os externos), você pode simplesmente inseri-los com acesso anônimo.

Porém, como você pode ver, essa simples solução de inserção não atende totalmente às necessidades de um aplicativo ISV.
A maioria dos aplicativos ISV precisa distribuir os dados para seus próprios clientes, não necessariamente usuários em sua própria organização. Por exemplo, se você estiver distribuindo algum serviço para a empresa A e a empresa B, os usuários na empresa A deverão ver apenas os dados de sua própria empresa. Isto é, a multilocação é necessária para a distribuição.

O aplicativo ISV também pode oferecer seus próprios métodos de autenticação, como autenticação de formulário, autenticação básica, etc. Assim, a solução de inserção deve colaborar com esses métodos existentes de autenticação de modo seguro. Também é necessário para os usuários poder usar esses aplicativos ISV sem a compra extra ou o licenciamento de uma assinatura do Power BI.

 **Power BI Embedded** foi desenvolvido precisamente para esses tipo de cenário ISV. Agora que fizemos essa rápida introdução, vamos entrar nos detalhes

Você pode usar o SDK para .NET \(C#) ou Node.js a fim de criar seu aplicativo facilmente com o Power BI Embedded. Mas, neste artigo, explicaremos sobre o fluxo do HTTP \(incluindo AuthN) do Power BI sem SDKs. Entendendo esse fluxo, você pode criar seu aplicativo **com qualquer linguagem de programação**, além de poder entender profundamente a essência do Power BI Embedded.

## <a name="create-power-bi-workspace-collection,-and-get-access-key-\(provisioning)"></a>Criar a coleção de espaços de trabalho do Power BI e obter tecla de acesso \(provisionamento)
O Power BI Embedded é um dos serviços do Azure. Apenas o ISV que usa o Portal do Azure é cobrado pelo uso \(sessão de usuário por hora). O usuário que exibe o relatório não é cobrado, e nem mesmo uma assinatura do Azure é exigida.
Antes de iniciar o desenvolvimento do nosso aplicativo, devemos criar a **coleção de espaços de trabalho do Power BI** usando o Portal do Azure.

Cada espaço de trabalho do Power BI Embedded é o espaço de trabalho de cada cliente (locatário), e podemos adicionar muitos espaços de trabalho em cada coleção de espaços de trabalho. A mesma tecla de acesso é usada em cada coleção de espaços de trabalho. Em vigor, a coleção de espaços de trabalho é o limite de segurança do Power BI Embedded.

![](media\\power-bi-embedded-iframe\\create-workspace.png)

Quando concluirmos a criação da coleção de espaços de trabalho, copie a tecla de acesso do Portal do Azure.

![](media\\power-bi-embedded-iframe\\copy-access-key.png)

> [!NOTE]
> Também podemos provisionar a coleção de espaços de trabalho e obter a tecla de acesso por meio da API REST. Para saber mais, confira [Power BI Resource Provider APIs](https://msdn.microsoft.com/library/azure/mt712306.aspx)(APIs do provedor de recursos do Power BI).
> 
> 

## <a name="create-.pbix-file-with-power-bi-desktop"></a>Criar o arquivo .pbix com o Power BI Desktop
Em seguida, devemos criar a conexão de dados e os relatórios a serem inseridos.
Para essa tarefa, não há programação nem código. Vamos usar apenas o Power BI Desktop.
Neste artigo, não entraremos em detalhes sobre como usar o Power BI Desktop. Se precisar de ajuda aqui, confira [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). Em nosso exemplo, usaremos apenas o [Exemplo de Análise de Varejo](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\\power-bi-embedded-iframe\\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Criar um espaço de trabalho do Power BI
Agora que o provisionamento foi feito, vamos começar a criar o espaço de trabalho de um cliente na coleção de espaços de trabalho por meio das APIs REST. A seguinte Solicitação HTTP POST (REST) está criando o novo espaço de trabalho em nossa coleção de espaços de trabalho existente. Em nosso exemplo, o nome da coleção de espaços de trabalho é **mypbiapp**.
Definimos apenas a tecla de acesso, que copiamos anteriormente, como **AppKey**. A autenticação é muito simples!

**Solicitação HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Resposta HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

A **workspaceId** retornada é usada para as chamadas à API subsequentes a seguir. Nosso aplicativo deve manter esse valor.

## <a name="import-.pbix-file-into-the-workspace"></a>Importar o arquivo .pbix no espaço de trabalho
Cada espaço de trabalho pode hospedar um único arquivo do Power BI Desktop com um conjunto de dados \(incluindo configurações de fonte de dados) e relatórios. Podemos importar nosso arquivo .pbix no espaço de trabalho, conforme mostrado no código abaixo. Como você pode ver, podemos carregar o binário do arquivo .pbix usando diversas partes de MIME em http.

O fragmento de uri **32960a09-6366-4208-a8bb-9e0678cdbb9d** é a workspaceId e o parâmetro de consulta **datasetDisplayName** é o nome do conjunto de dados a ser criado. O conjunto de dados criado contém todos os artefatos relacionados aos dados no arquivo .pbix, como dados importados, o ponteiro para a fonte de dados etc.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

A execução dessa tarefa de importação pode demorar um pouco. Quando concluída, nosso aplicativo poderá solicitar o status da tarefa usando a id de importação. Em nosso exemplo, a id de importação é **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Veja a seguir a solicitação de status usando essa id de importação:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Se a tarefa não for concluída, a resposta HTTP poderá ser parecida com esta:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Se a tarefa for concluída, a resposta HTTP poderá se parecer mais com isto:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-\(and-multi-tenancy-of-data)"></a>Conectividade da fonte de dados \(e multilocação de dados)
Embora quase todos os artefatos no arquivo .pbix sejam importados para nosso espaço de trabalho, as credenciais das fontes de dados não são. Como resultado, ao usar o **modo DirectQuery**, o relatório inserido não poderá ser exibido corretamente. Mas, ao usar o **Modo de importação**, poderemos exibir o relatório usando os dados importados existentes. Nesse caso, devemos definir a credencial usando as etapas a seguir por meio das chamadas à REST.

Em primeiro lugar, devemos obter a fonte de dados do gateway. Sabemos que a **id** do conjunto de dados é a id retornada anteriormente.

**Solicitação HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Resposta HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Usando a id de gateway retornada e a id da fonte de dados \(veja a **gatewayId** anterior e a **id** no resultado retornado), podemos mudar a credencial dessa fonte de dados como se segue:

**Solicitação HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Resposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

Em produção, também podemos definir a cadeia de conexão diferente para cada espaço de trabalho usando a API REST. \(ou seja, podemos separar o banco de dados para cada cliente.)

Veja a seguir a mudança da cadeia de conexão da fonte de dados por meio de REST.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Ou, podemos usar a Segurança em Nível de Linha no Power BI Embedded e separar os dados para cada usuário em um relatório. Consequentemente, podemos provisionar cada relatório de cliente com o mesmo .pbix \(interface de usuário etc.) e diferentes fontes de dados.

> [!NOTE]
> Se você estiver usando o **Modo de importação** em vez do **Modo DirectQuery**, não há como atualizar os modelos por meio da API. As fontes de dados locais por meio do gateway do Power BI ainda não têm suporte no Power BI Embedded. No entanto, você vai querer realmente ficar de olho no [blog do Power BI](https://powerbi.microsoft.com/blog/) para saber das novidades e o que virá nas versões futuras.
> 
> 

## <a name="authentication-and-hosting-(embedding)-reports-in-our-web-page"></a>Autenticação e hospedagem (inserção) de relatórios em nossa página da Web
Na API REST anterior, podemos usar a tecla de acesso **AppKey** em si como o cabeçalho da autorização. Como essas chamadas podem ser tratadas no servidor back-end, ela é segura.

Porém, quando inserimos o relatório em nossa página da Web, esse tipo de informação de segurança pode ser tratado usando JavaScript \(front-end). Sendo assim, o valor do cabeçalho da autorização deve ser protegido. Se nossa tecla de acesso for descoberta por um usuário ou código mal-intencionado, eles poderão chamar qualquer operação usando essa chave.

Quando inserimos o relatório em nossa página da Web, devemos usar o token calculado no lugar da tecla de acesso **AppKey**. Nosso aplicativo deve criar o Token Web JSON \(JWT) OAuth, que consiste em declarações e na assinatura digital calculada. Conforme ilustrado abaixo, este JWT OAuth são tokens de cadeia de caracteres codificados delimitados por ponto.

![](media\\power-bi-embedded-iframe\\oauth-jwt.png)

Primeiramente, devemos preparar o valor de entrada, que é assinado posteriormente. Esse valor é a cadeia de caracteres (rfc4648) codificada da url em base64 do seguinte json, limitada pelo caractere de ponto \(.). Mais adiante, explicaremos como obter a identificação de relatório.

> [!NOTE]
> Se quisermos usar RLS (Segurança em Nível de Linha) com o Power BI Embedded, também deveremos especificar **nome de usuário** e **funções** nas declarações.
> 
> 

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Em seguida, devemos criar a cadeia de caracteres codificada em base64 de HMAC \(a assinatura) com o algoritmo SHA256. Esse valor de entrada assinado é a cadeia de caracteres anterior.

Por fim, devemos combinar o valor de entrada e a cadeia de caracteres de assinatura usando o caractere de ponto \(.). A cadeia de caracteres completa é o token de aplicativo para a inserção de relatório. Mesmo se o token de aplicativo for descoberto por um usuário mal-intencionado, ele não poderá obter a tecla de acesso original. Esse token de aplicativo vai expirar rapidamente.

Veja um exemplo de PHP para essas etapas:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally,-embed-the-report-into-the-web-page"></a>Por fim, inserir o relatório na página da Web
Para inserir nosso relatório, devemos obter a url inserida e a **id** do relatório usando a API REST a seguir.

**Solicitação HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Resposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Podemos inserir o relatório em nosso aplicativo Web usando o token de aplicativo anterior.
Se observarmos o próximo código de exemplo, a primeira parte é a mesma do exemplo anterior. Na última parte, este exemplo mostra a **embedUrl** \(veja o resultado anterior) no iframe, que está postando o token de aplicativo no iframe.

> [!NOTE]
> Você precisará alterar o valor da id de relatório para um dos seus valores. Além disso, devido a um bug em nosso sistema de gerenciamento de conteúdo, a marcação de iframe no exemplo de código é lida literalmente. Remova o texto limitado da marcação caso copie e cole esse código de exemplo.
> 
> 

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Aqui está nosso resultado:

![](media\\power-bi-embedded-iframe\\view-report.png)

Neste momento, o Power BI Embedded mostra o relatório somente no iframe. Mas, fique atento ao [Blog do Power BI](). Melhorias futuras poderão usar novas APIs do lado do cliente que nos permitirão enviar informações ao iframe, bem como removê-las. Algo bem interessante!

## <a name="see-also"></a>Confira também
* [Autenticando e autorizando com o Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!--HONumber=Oct16_HO2-->


