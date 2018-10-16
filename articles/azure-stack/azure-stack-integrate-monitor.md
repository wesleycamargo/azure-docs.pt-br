---
title: Integrar a solução de monitoramento externa com o Azure Stack | Microsoft Docs
description: Saiba como integrar o Azure Stack com uma solução de monitoramento externa em seu datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: 66cd20eaa401261bcb18bedbbc16f5bcf40ee192
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342976"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrar a solução de monitoramento externa com o Azure Stack

Para o monitoramento externo da infraestrutura do Azure Stack, você precisará monitorar o software do Azure Stack, os computadores físicos e os comutadores de rede física. Cada uma dessas áreas oferece um método para recuperar informações de integridade e o alerta:

- Software de pilha do Azure oferece uma API baseada em REST para recuperar a integridade e alertas. O uso de tecnologias definida pelo software, como espaços de armazenamento diretos, integridade do armazenamento e alertas são parte do software de monitoramento.
- Computadores físicos podem disponibilizar integridade e informações de alerta por meio de controladores de gerenciamento de placa-base (BMC).
- Dispositivos de rede física podem disponibilizar integridade e informações de alerta por meio do protocolo SNMP.

Cada solução do Azure Stack é fornecido com um host de ciclo de vida do hardware. Esse host executa o software de monitoramento do fornecedor de hardware Original Equipment Manufacturer (OEM) para os servidores físicos e os dispositivos de rede. Se desejar, você pode ignorar essas soluções de monitoramento e integrar diretamente com as soluções de monitoramento existentes em seu datacenter.

> [!IMPORTANT]
> A solução de monitoramento externa que você usar deve ser sem agente. Você não pode instalar agentes de terceiros dentro de componentes do Azure Stack.

O diagrama a seguir mostra o fluxo do tráfego entre um sistema integrado do Azure Stack, o host de ciclo de vida do hardware, uma solução de monitoramento externa e um sistema de coleta de emissão de tíquetes/dados externos.

![Diagrama mostrando o tráfego entre o Azure Stack, monitoramento e solução de emissão de tíquetes.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Este artigo explica como integrar o Azure Stack com soluções de monitoramento externas como o System Center Operations Manager e Nagios. Ele também inclui como trabalhar com alertas programaticamente usando o PowerShell ou por meio de chamadas à API REST.

## <a name="integrate-with-operations-manager"></a>Integrar com o Operations Manager

Você pode usar o Operations Manager para o monitoramento externo do Azure Stack. O pacote de gerenciamento do System Center para Microsoft Azure Stack permite que você monitorar várias implantações do Azure Stack com uma única instância do Operations Manager. O pacote de gerenciamento usa o provedor de recursos de integridade e o provedor de recursos de atualização APIs REST para se comunicar com o Azure Stack. Se você planeja ignorar OEM monitorar um software que está em execução no host de ciclo de vida do hardware, você pode instalar pacotes de gerenciamento do fornecedor para monitorar os servidores físicos. Você também pode usar a descoberta de dispositivo de rede do Operations Manager para monitorar os comutadores de rede.

O pacote de gerenciamento para o Azure Stack fornece os seguintes recursos:

- Você pode gerenciar várias implantações do Azure Stack
- Há suporte para o Azure Active Directory (Azure AD) e os serviços de Federação do Active Directory (AD FS)
- Você pode recuperar e fechar alertas
- Há uma integridade e um painel de capacidade
- Inclui a detecção de modo de manutenção automática para quando o patch e atualização (P & U) está em andamento
- Inclui tarefas de atualização forçada para implantação e região
- Você pode adicionar informações personalizadas a uma região
- Dá suporte à notificação e o relatório

Você pode baixar o pacote de gerenciamento do System Center para Microsoft Azure Stack e os respectivos [guia do usuário](https://www.microsoft.com/en-us/download/details.aspx?id=55184), ou diretamente do Operations Manager.

Para uma solução de emissão de tíquetes, você pode integrar o Operations Manager com o System Center Service Manager. O conector de produto integrado que permite a comunicação bidirecional que permite que você fechar um alerta no Azure Stack e o Operations Manager depois de resolver uma solicitação de serviço no Service Manager.

O diagrama a seguir mostra a integração do Azure Stack com uma implantação existente do System Center. Você pode automatizar o Service Manager ainda mais com o System Center Orchestrator ou o Service Management Automation (SMA) para executar operações no Azure Stack.

![Diagrama mostrando a integração com OM, o Service Manager e o SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrar o Nagios

Um plug-in de monitoramento de Nagios foi desenvolvido em conjunto com as soluções do parceiro Cloudbase, que está disponível sob a licença de software livre permissivo – MIT (Massachusetts Institute of Technology).

O plug-in é escrito em Python e aproveita a API REST do provedor de recursos de integridade. Ele oferece a funcionalidade básica para recuperar e fechar alertas no Azure Stack. Como o pacote de gerenciamento do System Center, ele permite que você adicionar várias implantações do Azure Stack e enviar notificações.

O plug-in funciona com o Enterprise do Nagios e Nagios Core. Você pode baixá-lo [aqui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). O site de download também inclui detalhes de instalação e configuração.

### <a name="plugin-parameters"></a>Parâmetros de plug-in

Configure o arquivo de plug-in "Azurestack_plugin.py" com os seguintes parâmetros:

| Parâmetro | DESCRIÇÃO | Exemplo |
|---------|---------|---------|
| *arm_endpoint* | Ponto de extremidade do Azure Resource Manager (administrador) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Ponto de extremidade do Azure Resource Manager (administrador)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID de assinatura do administrador | Recuperar por meio do portal do administrador ou o PowerShell |
| *User_name* | Nome de usuário de assinatura de operador | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Senha de assinatura do operador | MyPassWord |
| *Client_id* | Cliente | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *região* |  Nome da região do Azure Stack | local |
|  |  |

* O GUID do PowerShell que é fornecido é universal. Você pode usá-lo para cada implantação.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Usar o PowerShell para monitorar a integridade e alertas

Se você não estiver usando uma solução baseada em Nagios, Nagios ou do Operations Manager, você pode usar o PowerShell para habilitar uma ampla gama de soluções para integrar com o Azure Stack de monitoramento.

1. Para usar o PowerShell, certifique-se de que você tenha [PowerShell instalado e configurado](azure-stack-powershell-configure-quickstart.md) para um ambiente de operador do Azure Stack. Instalar o PowerShell em um computador local que pode acessar o ponto de extremidade do Gerenciador de recursos (administrador) (https://adminmanagement. [ região]. [External_FQDN]).

2. Execute os comandos a seguir para conectar-se ao ambiente do Azure Stack como um operador do Azure Stack:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Use os comandos, como os exemplos a seguir para trabalhar com alertas:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>Saiba mais

Para obter informações sobre o monitoramento de integridade interna, consulte [monitorar a integridade e alertas no Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Próximas etapas

[Integração de segurança](azure-stack-integrate-security.md)
