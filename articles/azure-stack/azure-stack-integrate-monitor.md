---
title: Integrar a solução de monitoramento externa a pilha do Azure | Microsoft Docs
description: Aprenda a integrar o Azure pilha com uma solução de monitoramento externa em seu data center.
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
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: d7c8520602132722fd0c7138de4a276b9ac2208a
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807332"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrar a solução de monitoramento externa a pilha do Azure

Para o monitoramento externo da infraestrutura de pilha do Azure, você precisa monitorar o software de pilha do Azure, os computadores físicos e os comutadores de rede física. Cada uma dessas áreas oferece um método para recuperar as informações de integridade e o alerta:

- O software de pilha do Azure oferece uma API baseada em REST para recuperar a integridade e os alertas. O uso de tecnologias definidas pelo software, como espaços de armazenamento diretos, integridade do armazenamento e alertas fazem parte do software de monitoramento.
- Computadores físicos podem disponibilizar integridade e informações de alerta por meio de controladores de gerenciamento da placa-base (BMC).
- Dispositivos de rede física podem disponibilizar integridade e informações de alerta por meio do protocolo SNMP.

Cada solução de pilha do Azure é fornecido com um host de ciclo de vida de hardware. Esse host executa o software de monitoramento do fornecedor de hardware do fabricante de equipamento Original (OEM) para os servidores físicos e os dispositivos de rede. Se desejar, você pode ignorar essas soluções de monitoramento e integra diretamente com soluções de monitoramento existentes em seu data center.

> [!IMPORTANT]
> Solução de monitoramento externa que você usar deve ser sem agente. Você não pode instalar agentes de terceiros dentro de componentes da pilha do Azure.

O diagrama a seguir mostra o fluxo do tráfego entre um sistema de pilha do Azure integradas, o host de ciclo de vida de hardware, uma solução de monitoramento e um sistema de coleta de tíquetes/dados externos.

![Diagrama mostrando o tráfego entre a pilha do Azure, monitoramento e solução de registro.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Este artigo explica como integrar a pilha do Azure com soluções de monitoramento externas, como o System Center Operations Manager e Nagios. Ele também inclui como trabalhar com alertas programaticamente usando o PowerShell ou por meio de chamadas da API REST.

## <a name="integrate-with-operations-manager"></a>Integrar com o Operations Manager

Você pode usar o Operations Manager para monitorar externo da pilha do Azure. O pacote de gerenciamento do System Center para a pilha do Microsoft Azure permite monitorar várias implantações de pilha do Azure com uma única instância do Operations Manager. O pacote de gerenciamento usa o provedor de recursos de integridade e o provedor de recursos de atualização APIs REST para se comunicar com a pilha do Azure. Se você planeja ignorar OEM software que está em execução no host de ciclo de vida do hardware de monitoramento, você pode instalar pacotes de gerenciamento do fornecedor para monitorar servidores físicos. Você também pode usar a descoberta de dispositivo de rede do Operations Manager para monitorar os comutadores de rede.

O pacote de gerenciamento para a pilha do Azure fornece os seguintes recursos:

- Você pode gerenciar várias implantações de pilha do Azure
- Não há suporte para o Azure Active Directory (AD do Azure) e os serviços de Federação do Active Directory (AD FS)
- Você pode recuperar e fechar alertas
- Há uma integridade e um painel de capacidade
- Inclui a detecção de modo de manutenção automática para quando o patch e atualização (P & U) está em andamento
- Inclui tarefas de atualização forçada para implantação e região
- Você pode adicionar informações personalizadas a uma região
- Oferece suporte à notificação e o relatório

Você pode baixar o pacote de gerenciamento do System Center para a pilha do Microsoft Azure e os respectivos [guia do usuário](https://www.microsoft.com/en-us/download/details.aspx?id=55184), ou diretamente do Operations Manager.

Para uma solução de emissão de tíquetes, você pode integrar o Operations Manager com o System Center Service Manager. O conector de produto integrado permite a comunicação bidirecional que permite que você fechar um alerta na pilha do Azure e Operations Manager depois de resolver uma solicitação de serviço no Service Manager.

O diagrama a seguir mostra a integração da pilha do Azure com uma implantação existente do System Center. Você pode automatizar o Service Manager ainda mais com o System Center Orchestrator ou Service Management Automation (SMA) para executar operações na pilha do Azure.

![Diagrama mostrando a integração com o OM, o Service Manager e o SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrar com Nagios

Um plug-in de monitoramento de Nagios foi desenvolvido junto com as soluções de Cloudbase de parceiro, que está disponível sob a licença de software livre permissivo – MIT (Massachusetts Institute of Technology).

O plug-in é gravado em Python e utiliza o provedor de recursos de integridade API REST. Ele oferece a funcionalidade básica para recuperar e fechar alertas na pilha do Azure. Como o pacote de gerenciamento do System Center, ele permite que você adicionar várias implantações de pilha do Azure e enviar notificações.

O plug-in funciona com Nagios Enterprise e Nagios Core. Você pode baixá-lo [aqui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). O site de download também inclui detalhes de instalação e configuração.

### <a name="plugin-parameters"></a>Parâmetros de plug-in

Configure o arquivo de plug-in "Azurestack_plugin.py" com os seguintes parâmetros:

| Parâmetro | DESCRIÇÃO | Exemplo |
|---------|---------|---------|
| *arm_endpoint* | Ponto de extremidade do Azure Resource Manager (administrador) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Ponto de extremidade do Azure Resource Manager (administrador)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID de assinatura de Admin | Recuperar por meio do portal do administrador ou o PowerShell |
| *user_name* | O nome de usuário do operador assinatura | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Senha de assinatura do operador | MyPassWord |
| *Client_id* | Cliente | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *Região* |  Nome da região de pilha do Azure | local |
|  |  |

* O GUID do PowerShell que é fornecido é universal. Você pode usá-lo para cada implantação.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Use o PowerShell para monitorar a integridade e alertas

Se você não estiver usando o Operations Manager, Nagios ou uma solução baseada em Nagios, você pode usar o PowerShell para habilitar uma ampla gama de soluções para integrar com o Azure pilha de monitoramento.

1. Para usar o PowerShell, certifique-se de que você tenha [PowerShell instalado e configurado](azure-stack-powershell-configure-quickstart.md) para um ambiente de operador de pilha do Azure. Instale o PowerShell em um computador local que pode acessar o ponto de extremidade do Gerenciador de recursos (administrador) (https://adminmanagement. [ região]. [External_FQDN]).

2. Execute os comandos a seguir para se conectar ao ambiente de pilha do Azure como um operador de pilha do Azure:

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

Para obter informações sobre monitoramento de integridade interna, consulte [monitorar a integridade e alertas na pilha do Azure](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Próximas etapas

[Integração de segurança](azure-stack-integrate-security.md)
