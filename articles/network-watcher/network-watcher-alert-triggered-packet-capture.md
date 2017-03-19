---
title: Usar a captura de pacotes para fazer um monitoramento de rede proativo com o Azure Functions | Microsoft Docs
description: Este artigo descreve como criar uma captura de pacotes disparada por alertas com o Observador de Rede do Azure
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Usar a captura de pacotes para fazer um monitoramento de rede proativo com o Azure Functions

A captura de pacotes do Observador de Rede permite criar sessões de captura para controlar o tráfego dentro e fora de uma máquina virtual. O arquivo de captura pode ter um filtro que é definido para acompanhar apenas o tráfego que você deseja monitorar. Esses dados são armazenados em um blob de armazenamento ou localmente na máquina convidada. Esse recurso pode ser iniciado remotamente a partir de outros cenários de automação como o Azure Functions. A captura de pacotes fornece a capacidade de executar capturas proativas com base nos problemas de rede definidos. Outros usos incluem a coleta das estatísticas de rede, obter informações sobre as invasões de rede, depurar as comunicações entre cliente e servidor, e muito mais.

Os recursos implantados no Azure estão em execução 24/7. Você ou sua equipe não pode monitorar ativamente o status de todos os recursos 24/7. O que acontecerá se ocorrer um problema às 2:00 da manhã?

Usando o Observador de Rede, Alertas e Funções de dentro do ecossistema do Azure, você pode responder proativamente aos problemas em sua rede com dados e ferramentas para resolver o problema.

## <a name="before-you-begin"></a>Antes de começar

Neste exemplo, sua VM está enviando mais segmentos TCP que o usual e você gostaria de ser alertado. Os segmentos TCP são usados como um exemplo e você pode utilizar qualquer condição de alerta. Quando você for alertado, desejará ter os dados no nível do pacote para entender por que a comunicação aumentou para que possa tomar medidas para retornar a máquina à comunicação normal.
Este cenário pressupõe que você tem uma instância existente do Observador de Rede e um grupo de recursos com uma máquina virtual válida a ser usada.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Cenário

Para automatizar esse processo, criamos e conectamos um Alerta em nossa VM para disparar quando ocorre o incidente, e uma Função do Azure para chamar no Observador de Rede.

Este cenário:

* Crie uma função do Azure que inicia uma captura de pacotes.
* Criar uma regra de alerta em uma máquina virtual
* Configurar a regra de alerta para chamar a função do Azure

## <a name="creating-an-azure-function-and-overview"></a>Criar uma Função do Azure e visão geral

A primeira etapa é criar uma função do Azure para processar o alerta e criar uma captura de pacotes. 

A lista a seguir é uma visão geral do fluxo de trabalho que ocorre.

1. Um alerta é disparado em sua VM.
1. O alerta chama sua Função do Azure por meio de um webhook.
1. A Função do Azure processa o alerta e inicia uma sessão de captura de pacotes do Observador de Rede.
1. A captura de pacotes é executada na VM e coleta o tráfego. 
1. O arquivo de captura é carregado em uma conta de armazenamento para um diagnóstico e análise 

Criar uma Função do Azure pode ser feito no portal seguindo [Criar sua primeira Função do Azure](../azure-functions/functions-create-first-azure-function.md). Para este exemplo, escolhemos uma função do tipo HttpTrigger-CSharp. 

> [!NOTE]
> Outras linguagens são fornecidas com as funções do Azure, mas podem ser experimentais e não ter suporte completo, como o PowerShell e o Python.

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Processar o alerta e iniciar uma sessão de captura de pacotes

Agora é hora de fazer chamadas no Observador de Rede de dentro da Função do Azure. Dependendo dos requisitos, a implementação dessa função é diferente. No entanto, o fluxo geral do código é assim:

1. processar os parâmetros de entrada
2. consultar as capturas de pacotes existentes para verificar os limites e resolver os conflitos de nomenclatura
3. criar uma captura de pacotes com os devidos parâmetros
4. pesquisar a captura de pacotes periodicamente até concluir
5. notificar o usuário que a sessão de captura de pacotes foi concluída

O exemplo a seguir é C# e pode ser usado na Função do Azure. Há valores que precisam ser substituídos para a assinatura, id do cliente, id do locatário e segredo do cliente.

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

Depois de criar sua função, você precisa configurar o alerta para chamar a URL associada à função. Para obter esse valor, copie a URL da Função a partir de seu aplicativo de funções.

![localizar a url da função][2]

Se você precisar de propriedades personalizadas no conteúdo da solicitação POST do webhook, consulte [Configurar um webhook em um alerta de métrica do Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="configure-an-alert-on-a-vm"></a>Configurar um alerta em uma VM

Os alertas podem ser configurados para notificar as pessoas quando uma métrica específica cruza um limite atribuído a ela. Neste exemplo, o alerta está nos segmentos TCP enviados, mas o alerta pode ser disparado para muitas outras métricas. No exemplo, um alerta é configurado para chamar um webhook para chamar a função.

### <a name="create-the-alert-rule"></a>Criar a regra de alerta

Navegue até uma máquina virtual existente e adicione uma regra de alerta. Mais documentação detalhada sobre como configurar alertas pode ser encontrada no [Portal do Azure do usuário para criar alertas para os serviços do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![adicionar regra de alerta da vm a uma máquina virtual][1]

> [!NOTE]
> Algumas métricas não são habilitadas por padrão. Saiba mais sobre como habilitar as métricas de adição visitando [Habilitar o monitoramento e diagnóstico](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)

Por fim, cole a URL da etapa anterior na caixa de texto webhook em seu alerta. Clique em **OK** para salvar a regra de alerta.

![colar a url para a regra de alerta][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Baixar e exibir o arquivo de captura

Se você salvar a captura de uma conta de armazenamento, então, o arquivo de captura poderá ser baixado via portal ou por meio da programação. Se o arquivo de captura for armazenado localmente, ele será recuperado fazendo logon na máquina virtual. 

Para obter instruções sobre como baixar os arquivos das contas de armazenamento do Azure, consulte [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Gerenciador de Armazenamento. Mais informações sobre o Gerenciador de Armazenamento podem ser encontradas aqui no link a seguir: [Gerenciador de Armazenamento](http://storageexplorer.com/)

Depois que a captura for baixada, você poderá exibi-la usando qualquer ferramenta que possa ler um arquivo **.cap**. Os links para duas dessas ferramentas são:

[Analisador de Mensagens da Microsoft](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>Próximas etapas

Saiba como exibir as capturas de pacotes visitando [Análise da captura de pacotes com o Wireshark](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

