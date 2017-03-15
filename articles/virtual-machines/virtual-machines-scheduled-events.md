---
title: "Eventos agendados o Serviço de Metadados do Azure | Microsoft Docs"
description: "Reaja a Eventos de Impacto em sua Máquina Virtual antes que eles ocorram."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
translationtype: Human Translation
ms.sourcegitcommit: f67ff1cc18b23c3d4006121e266a5d96933e49bf
ms.openlocfilehash: d6c4d0b9f9cbf05341d30fe75e9828d0237a1962
ms.lasthandoff: 03/02/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Serviço de Metadados do Azure – Eventos Agendados (Visualização)

> [!NOTE] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para obter mais informações, consulte [Termos de Uso do Microsoft Azure para Visualizações do Microsoft Azure.] (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)
>

Os Eventos Agendados são um dos subserviços no Serviço de Metadados do Azure que revelam informações sobre eventos futuros (por exemplo, de reinicialização) para que seu aplicativo possa se preparar para eles e limitar interrupções. Estão disponíveis para todos os tipos de Máquina Virtual do Azure, incluindo PaaS e IaaS. Os Eventos Agendados fornecem seu tempo de Máquina Virtual para executar tarefas preventivas e minimizar o efeito de um evento. 


## <a name="introduction---why-scheduled-events"></a>Introdução – Por que usar Eventos Agendados?

Com Eventos Agendados, é possível tomar medidas para limitar o impacto em seu serviço. Cargas de trabalho de várias instâncias, que usam técnicas de replicação para manter o estado, podem estar vulneráveis a interrupções frequentes que ocorrem em diversas instâncias. Tais interrupções podem resultar em tarefas caras (por exemplo, recompilação de índices) ou até mesmo na perda de uma réplica. Em muitos outros casos, usar a sequência de desligamento normal melhora a disponibilidade do serviço geral. Por exemplo, concluindo (ou cancelar) transações em trânsito, reatribuindo outras tarefas para outras VMs no cluster (failover manual) e remover a Máquina Virtual de um pool do balanceador de carga. Há casos em que notificar um administrador sobre um evento futuro ou mesmo simplesmente registrar tal evento em log, pode ajudar a melhorar a capacidade de manutenção de aplicativos hospedados na nuvem.
O Serviço de Metadados do Azure revela Eventos Agendados nos seguintes casos de uso:
-    Manutenção iniciada na plataforma (por exemplo, distribuição do sistema operacional do Host)
-    Chamadas iniciadas pelo usuário (por exemplo, o usuário reinicializa ou reimplanta uma VM)


## <a name="scheduled-events---the-basics"></a>Eventos Agendados – Noções básicas  

O Serviço de Metadados do Azure expõe informações sobre a execução de máquinas virtuais usando um Ponto de Extremidade REST de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que ele não seja exposto fora da VM.

### <a name="scope"></a>Escopo
Eventos agendados são exibidos para todas as máquinas virtuais em um serviço de nuvem ou para todas as máquinas virtuais em um conjunto de disponibilidade. Como resultado, você deve verificar o campo **Recursos** no evento para identificar quais VMs serão afetadas. 

### <a name="discover-the-endpoint"></a>Descobrir o ponto de extremidade
Quando uma máquina Virtual é criada em uma Rede Virtual (VNet), o serviço de metadados está disponível no IP não roteável: 169.254.169.254. Caso contrário, os casos padrão para serviços de nuvem e VMs clássicas, uma lógica adicional é necessária para descobrir o ponto de extremidade para usar. Consulte este exemplo para aprender a [descobrir o ponto de extremidade do host] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="versioning"></a>Controle de versão 
O Serviço de Metadados usa uma API de controle de versão no seguinte formato: http://{ip}/metadata/{versão}/scheduledevents. É recomendável que o seu serviço consuma a versão mais recente disponível em: http://{ip}/metadata/latest/scheduledevents

### <a name="using-headers"></a>Usando cabeçalhos
Ao consultar o Serviço de Metadados, você deverá fornecer o seguinte cabeçalho *Metadata: true*. 

### <a name="enable-scheduled-events"></a>Habilitar eventos agendados
Na primeira vez que você chamar os eventos agendados, o Azure habilita implicitamente o recurso em sua máquina virtual. Como resultado, você deve esperar um atraso na resposta em sua primeira chamada de até dois minutos.

### <a name="testing-your-logic-with-user-initiated-operations"></a>Teste sua lógica com as operações iniciadas pelo usuário
Para testar sua lógica, você pode usar o Portal do Azure, a API, a CLI ou o PowerShell para iniciar as operações que resultam em eventos agendados. Reiniciar uma máquina virtual resulta em um evento agendado com um tipo de evento igual para Reinicializar. Reimplantar uma máquina virtual resulta em um evento agendado com um tipo de evento igual para Reimplantar.
Em ambos os casos, a operação iniciada pelo usuário leva mais tempo para ser concluída, já que os eventos agendados permitem que um aplicativo tenha mais tempo para desligar normalmente. 

## <a name="using-the-api"></a>Usando a API

### <a name="query-for-events"></a>Consulta de eventos
Você pode consultar Eventos Agendados realizando a chamada a seguir

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.
No caso de haver eventos agendados, a resposta contém uma matriz de eventos: 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

O EventType captura o impacto esperado na máquina virtual, em que:
- Congele: a máquina virtual está agendada para pausar por alguns segundos. Não há nenhum impacto na memória, arquivos abertos ou conexões de rede
- Reinicialização: a máquina virtual está agendada para reinicialização (a memória é apagada).
- Reimplantação: a máquina virtual está agendada para ser movida para outro nó (os discos efêmeros são perdidos). 

Quando um evento é agendado (Status = Agendado), o Azure compartilha o tempo após o qual o evento pode iniciar (especificado no campo NotBefore).

### <a name="starting-an-event-expedite"></a>Iniciando um evento (agilizar)

Depois de ter descoberto um evento futuro e concluído sua lógica de desligamento normal, você pode indicar que o Azure deve apressar-se (quando possível), fazendo uma chamada **POST** 
 

## <a name="powershell-sample"></a>Exemplo do PowerShell 

O exemplo a seguir lê o servidor de metadados para eventos agendados e aprova os eventos.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $uri)
{    
    # Create the Scheduled Events Approval Json
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/latest/scheduledevents' -f $localHostIP 


# Get the document
$scheduledEvents = GetScheduledEvents $scheduledEventURI


# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents


# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
    ApproveScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>Exemplo de C\# 
O exemplo a seguir é de um cliente que identifica APIs para se comunicar com o Serviço de Metadados
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
Eventos Agendados podem ser analisados usando as seguintes estruturas de dados 

```csharp
    public class ScheduledEventsDocument
    {
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

Um programa de exemplo que usa o cliente para recuperar, manipular e reconhecer eventos:   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Exemplo de Python 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>Próximas etapas 
[Manutenção planejada para máquinas virtuais no Azure](./virtual-machines-linux-planned-maintenance.md)

