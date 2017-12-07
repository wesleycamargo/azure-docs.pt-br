---
title: Eventos agendados para VMs do Linux no Azure | Microsoft Docs
description: "Agendado eventos usando o serviço de metadados do Azure para nas máquinas virtuais Linux."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 2df39c64470e28bdf664d388041ae1b17d80db69
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Serviço de Metadados do Azure: Eventos Agendados (versão prévia) para VMs do Linux

> [!NOTE] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Os eventos agendados são um serviço de metadados do Azure que dá ao aplicativo tempo para se preparar para a manutenção de máquinas virtuais. Ele fornece informações sobre eventos de manutenção futuros (por exemplo, reinicializar) para que seu aplicativo possa se preparar e limitar as interrupções. Ele está disponível para todos os tipos de Máquina Virtual do Azure, incluindo PaaS e IaaS no Windows e no Linux. 

Para saber mais sobre os eventos agendados no Windows, veja [Eventos Agendados para VMs do Windows](../windows/scheduled-events.md).

## <a name="why-scheduled-events"></a>Por que Eventos Agendados?

Muitos aplicativos podem se beneficiar do tempo para se preparar para a manutenção de máquinas virtuais. O tempo pode ser usado para executar tarefas específicas de aplicativo que melhoram a disponibilidade, a confiabilidade e a capacidade de manutenção, incluindo: 

- Ponto de verificação e restauração
- Descarregamento de conexão
- Failover de réplica primária 
- Remoção do pool do balanceador de carga
- Log de eventos
- Desligamento normal 

Usando eventos agendados, seu aplicativo pode descobrir quando a manutenção ocorrerá e acionará tarefas para limitar seu impacto.  

Os eventos agendados fornecem eventos nos seguintes casos de uso:
- Manutenção iniciada pela plataforma (por exemplo, Atualização do Sistema Operacional do Host)
- Manutenção iniciada pelo usuário (por exemplo, o usuário reinicia ou reimplanta uma VM)

## <a name="the-basics"></a>Noções básicas  

O Serviço de Metadados do Azure expõe informações sobre a execução de Máquinas Virtuais usando um Ponto de Extremidade REST acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que ele não seja exposto fora da VM.

### <a name="scope"></a>Escopo
Os eventos agendados são entregues a:
- Todas as máquinas virtuais em um serviço de nuvem
- Todas as máquinas virtuais em um conjunto de disponibilidade
- Todas as máquinas virtuais em um Grupo de Posicionamento do Conjunto de Escala. 

Como resultado, você deve verificar o campo `Resources` no evento para identificar quais VMs serão afetadas.

### <a name="discovering-the-endpoint"></a>Descoberta do ponto de extremidade
Para VMs habilitadas a VNET, o ponto de extremidade completo para a versão mais recente dos eventos agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

No caso em que uma máquina virtual é criada em uma VNet (Rede Virtual), o serviço de metadados está disponível a partir de um IP não roteável estático, `169.254.169.254`.
Se a Máquina Virtual não for criada em uma Rede Virtual, casos padrão para serviços de nuvem e VMs clássicas, uma lógica adicional será necessária para descobrir o endereço IP a ser utilizado. Consulte esse exemplo para saber como [descobrir o ponto de extremidade do host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Controle de versão 
O serviço de eventos agendados tem controle de versão. As versões são obrigatórias e a versão atual é `2017-08-01`.

| Versão | Notas de versão | 
| - | - | 
| 2017-08-01 | <li> Removido o sublinhado inicial dos nomes de recursos para as VMs de Iaas<br><li>Requisito de cabeçalho de metadados imposto para todas as solicitações | 
| 2017-03-01 | <li>Versão Prévia Pública


> [!NOTE] 
> Versões de visualização anteriores de eventos agendados compatíveis {mais recentes} como a api-version. Esse formato não é mais suportado e será substituído no futuro.

### <a name="using-headers"></a>Uso de cabeçalhos
Ao consultar o Serviço de Metadados você deverá fornecer o cabeçalho `Metadata:true` para garantir que a solicitação não foi redirecionada de forma involuntária. O cabeçalho `Metadata:true` é necessário para todas as solicitações de eventos programados. A não inclusão do cabeçalho na solicitação resultará em uma resposta de solicitação incorreta do Serviço de Metadados.

### <a name="enabling-scheduled-events"></a>Habilitar Eventos Agendados
Na primeira vez em que fizer uma solicitação de eventos programados, o Azure habilitará implicitamente o recurso em sua Máquina Virtual. Como resultado, você deve esperar um atraso na resposta em sua primeira chamada de até dois minutos.

> [!NOTE]
> Os eventos agendados serão desabilitados automaticamente para seu serviço se ele não chamar o ponto de extremidade por 1 dia. Quando os eventos agendados é desabilitado para o serviço, não haverá eventos criados para a manutenção iniciada pelo usuário.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo usuário
A manutenção de máquinas virtuais iniciada pelo usuário pelo portal do Azure, API, CLI ou PowerShell resulta em um evento agendado. Isso permite que você teste a lógica de preparação de manutenção em seu aplicativo e permite que seu aplicativo se prepare para manutenção iniciada pelo usuário.

Reiniciar uma máquina virtual agendará um evento com tipo `Reboot`. Reimplantar uma máquina virtual agendará um evento com tipo `Redeploy`.

> [!NOTE] 
> No momento, no máximo 100 operações de manutenção iniciadas pelo usuário podem ser agendadas simultaneamente.

> [!NOTE] 
> A manutenção iniciada pelo usuário que resulta em Eventos Agendados no momento não é configurável. A capacidade de configuração está prevista para uma versão futura.

## <a name="using-the-api"></a>Usando a API

### <a name="query-for-events"></a>Consulta de eventos
Você pode consultar Eventos agendados realizando a chamada a seguir:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.
No caso de haver eventos agendados, a resposta contém uma matriz de eventos: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador global exclusivo para esse evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto desse evento. <br><br> Valores: <br><ul><li> `Freeze`: a máquina virtual está agendada para pausar por alguns segundos. A CPU é suspensa, mas não há nenhum impacto na memória, em arquivos abertos ou em conexões de rede. <li>`Reboot`: a Máquina Virtual está agendada para reiniciar (a memória é apagada). <li>`Redeploy`: a Máquina Virtual está agendada para ser movida para outro nó (os discos efêmeros são perdidos). |
| ResourceType | Tipo de recurso que esse evento impacta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que esse evento impacta. Isso é garantido para conter máquinas de no máximo um [Domínio de Atualização](manage-availability.md), mas pode não conter todas as máquinas no UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Status do evento | Status desse evento. <br><br> Valores: <ul><li>`Scheduled`: esse evento está agendado para iniciar após o tempo especificado na propriedade `NotBefore`.<li>`Started`: esse evento foi iniciado.</ul> Não `Completed` status semelhante já foi fornecido; o evento não será mais retornado quando o evento for concluído.
| NotBefore| Tempo após o qual esse evento poderá começar. <br><br> Exemplo: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Agendamento do evento
Cada evento é agendado uma quantidade mínima de tempo no futuro com base no tipo de evento. Esse tempo é refletido na propriedades `NotBefore` de um evento. 

|EventType  | Aviso mínimo |
| - | - |
| Congelamento| 15 minutos |
| Reboot | 15 minutos |
| Reimplantar | 10 minutos |

### <a name="starting-an-event"></a>Como iniciar um evento 

Após a descoberta de um evento futuro e concluído sua lógica de desligamento normal, você poderá aprovar o evento pendente fazendo uma chamada `POST` para o serviço de metadados com `EventId`. Isso indica para o Azure que ele pode encurtar o tempo mínimo de notificação (quando possível). 

A seguir vemos o json esperado no corpo da solicitação `POST`. A solicitação deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que você deseja agilizar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Exemplo de bash
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Reconhecer um evento permite que o evento prossiga para todos `Resources` no evento, e não apenas a máquina virtual que reconhece o evento. Portanto, é possível eleger um líder para coordenar o reconhecimento, que pode ser tão simples como a primeira máquina no campo `Resources`.

## <a name="python-sample"></a>Exemplo de Python 

O exemplo a seguir consulta o serviço de metadados para eventos agendados e aprova cada evento pendente.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Próximas etapas 
- Confira os Exemplos de código de eventos agendados no [Repositório Github de eventos agendados de metadados de instância do Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Leia mais sobre as API disponíveis no [serviço Metadados da Instância](instance-metadata-service.md).
- Saiba mais sobre a [manutenção planejada para máquinas virtuais do Linux no Azure ](planned-maintenance.md).
