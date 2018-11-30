---
title: Colete dados de log com o agente do Azure Log Analytics | Microsoft Docs
description: Este tópico ajuda o reconhecimento de como coletar dados e monitorar computadores hospedados no Azure, no local ou em outro ambiente de nuvem com o Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 9729c7313f275201fc61bb5c7552066eecb13373
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637981"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Colete dados de log com o agente do Log Analytics do Azure

O agente do Azure Log Analytics (OMS), anteriormente conhecido como agente do Microsoft Monitoring Agent (MMA) ou OMS Linux, foi desenvolvido para gerenciamento abrangente em máquinas locais, computadores monitorados pelo [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/), e máquinas virtuais em qualquer nuvem. Os agentes do Windows e do Linux se conectam a um espaço de trabalho do Log Analytics para coletar dados de diferentes origens, bem como quaisquer logs ou métricas exclusivos, conforme definido em uma solução de monitoramento. 

Este artigo fornece uma visão geral detalhada do agente, dos requisitos do sistema e da rede e dos diferentes métodos de implantação.   

## <a name="overview"></a>Visão geral

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Antes de analisar e agir sobre os dados coletados, você primeiro precisa instalar e conectar os agentes de todas as máquinas para as quais deseja enviar dados ao serviço do Log Analytics. Você pode instalar agentes em suas VMs do Azure usando a extensão VM do Log do Azure para Windows e Linux e para computadores em um ambiente híbrido usando a instalação, a linha de comando ou o DSC (Configuração de Estado Desejado) na Automação do Azure. 

O agente para Linux e Windows comunica saída para o serviço Log Analytics pela porta TCP 443 e, se a máquina se conectar por meio de um firewall ou servidor proxy para se comunicar pela Internet, revise os requisitos abaixo para o reconhecimento da configuração de rede necessária. Se as políticas de segurança de TI não permitirem que os computadores na rede se conectem à Internet, você poderá configurar um [gateway do Log Analytics](gateway.md) e configurar o agente para se conectar por meio do gateway ao Log Analytics. O agente pode receber informações de configuração e enviar dados coletados, dependendo de quais regras de coleta de dados e soluções de monitoramento você ativou. 

Se você estiver monitorando o computador com o System Center 2012 R2 ou superior, ele poderá ser multihomed com o serviço Log Analytics para coletar dados e encaminhe para o serviço e ainda ser monitorado pelo [Operations Manager](../../log-analytics/log-analytics-om-agents.md). Os computadores Linux monitorados por um grupo de gerenciamento do Operations Manager integrado ao Log Analytics não recebem a configuração de fontes de dados e encaminham os dados coletados pelo grupo de gerenciamento. O agente do Windows pode relatar até quatro áreas de trabalho do Log Analytics, enquanto o agente do Linux suporta apenas o relatório para um único espaço de trabalho.  

O agente para Linux e Windows não serve apenas para conectar-se ao Log Analytics, também oferece suporte à Automação do Azure para hospedar a função de trabalho do Runbook Híbrido e outros serviços, como [Change Tracking](../../automation/automation-change-tracking.md) e [Gerenciamento de Atualizações](../../automation/automation-update-management.md). Para obter mais informações sobre a função Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Automação do Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis
Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2008 Service Pack 1 (SP1) ou posterior
* Windows 7 SP1 e posterior.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte
Esta seção fornece detalhes sobre as distribuições de Linux com suporte.    

Começando com versões lançadas depois de agosto de 2018, estamos fazendo as seguintes alterações ao nosso modelo de suporte:  

* Somente o servidor versões têm suporte, um não cliente.  
* Novas versões de [distros do Azure Linux Endorsed](../../virtual-machines/linux/endorsed-distros.md) são sempre suportadas.  
* Todas as versões secundárias têm suporte para cada versão principal listada.
* As versões que passaram a data de fim de suporte do fabricante não são suportadas.  
* Não há suporte para novas versões do AMI.  
* Apenas versões que executam o SSL 1.x por padrão são suportadas.

Se você estiver usando uma distribuição ou versão que não é suportada no momento e não se alinha ao nosso modelo de suporte, recomendamos que você distribua esse repositório, reconhecendo que o suporte da Microsoft não fornecerá assistência com as versões do agente bifurcado.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86 x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x86 x64) 
* Red Hat Enterprise Linux Server 6 (x86 x64) e 7 (x64)
* Debian GNU/Linux 8 e 9 (x86 x64)
* Ubuntu 14.04 18.04 LTS (x64), 16.04 LTS (x86 x64) e LTS (x86 x64)
* SUSE Linux Enterprise Server 12 (x64)

>[!NOTE]
>OpenSSL 1.1.0 só tem suporte em plataformas de x86_x64 (64 bits) e OpenSSL mais cedo do que 1. x não tem suporte em qualquer plataforma.
>

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2
Para garantir a segurança dos dados em trânsito para o Log Analytics, incentivamos você a configurar o agente para usar pelo menos o protocolo TLS 1.2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**.  Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](../../log-analytics/log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede
As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agentes do Windows e do Linux se comunique com o Log Analytics.  

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Entrada e saída|SIM |  
|*.oms.opinsights.azure.com |Porta 443 |Entrada e saída|SIM |  
|*.blob.core.windows.net |Porta 443 |Entrada e saída|SIM |  
|*.azure-automation.net |Porta 443 |Entrada e saída|SIM |  


Se você planeja usar o Hybrid Runbook Worker da Automação do Azure para conectar e se registrar no serviço de automação para usar runbooks em seu ambiente, é necessário ter acesso ao número da porta e as URLs descritas em [Configurar sua rede para o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

O agente do Windows e do Linux é compatível com a comunicação por meio de um servidor proxy ou de um gateway do Log Analytics para o serviço Log Analytics usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  Para o agente do Windows conectado diretamente ao serviço, a configuração do proxy é especificada durante a instalação ou [após a implementação](agent-manage.md#update-proxy-settings) no Painel de Controle ou no PowerShell.  

Para o agente Linux, o servidor proxy pode ser especificado durante a instalação ou modificando o arquivo de configuração proxy.conf [após a instalação](agent-manage.md#update-proxy-settings).  O valor de configuração de proxy do agente do Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se seu servidor proxy não exigir autenticação, o agente para Linux exigirá mesmo assim fornecendo um pseudo usuário/senha. Isso pode ser qualquer nome de usuário ou senha.

|Propriedade| DESCRIÇÃO |
|--------|-------------|
|Protocolo | HTTPS |
|usuário | Nome de usuário opcional para autenticação de proxy |
|Senha | Senha opcional para autenticação de proxy |
|proxyhost | Endereço ou FQDN do servidor proxy/gateway do Log Analytics |
|porta | Número da porta opcional para o servidor proxy/gateway do Log Analytics |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se você usar caracteres especiais, como “\@” em sua senha, você receberá um erro de conexão de proxy porque o valor é analisado incorretamente.  Para contornar esse problema, codifique a senha na URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalar e configurar o agente 
Conectar máquinas em sua assinatura do Azure ou ambiente híbrido diretamente com o Log Analytics pode ser realizado usando métodos diferentes, dependendo de seus requisitos. A tabela a seguir realça cada método para determinar o que funciona melhor em sua organização.

|Fonte | Método | DESCRIÇÃO|
|-------|-------------|-------------|
|VM do Azure| - Extensão de VM do Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) usando a CLI do Azure ou com um modelo do Azure Resource Manager<br>- [Manualmente no portal do Azure](../../log-analytics/log-analytics-quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | A extensão instala o agente do Log Analytics nas máquinas virtuais do Azure e as registra em uma área de trabalho do Azure Monitor existente.|
| Computador Windows híbrido|- [Instalação manual](agent-windows.md)<br>- [DSC de Automação do Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Instalar o agente Microsoft Monitoring da linha de comando ou usando um método automatizado como DSC de automação do Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), ou com um modelo do Azure Resource Manager, se você implantou o Microsoft Azure Stack no seu datacenter.| 
| Computador Linux híbrido| [Instalação manual](../../log-analytics/log-analytics-quick-collect-linux-computer.md)|Instale o agente para Linux chamando um script de wrapper hospedado no GitHub. | 
| System Center Operations Manager|[Conectar o Operations Manager ao Log Analytics](../../log-analytics/log-analytics-om-agents.md) | Configure a integração entre o Operations Manager e o Log Analytics para encaminhar dados coletados de computadores Linux e Windows subordinados a um grupo de gerenciamento.|  

## <a name="next-steps"></a>Próximas etapas

* Consultar as [fontes de dados](../../azure-monitor/platform/agent-data-sources.md) para entender as fontes de dados disponíveis para coletar dados do sistema Windows ou Linux. 

* Saiba mais sobre [registrar consultas](../../log-analytics/log-analytics-queries.md) para analisar os dados coletados de fontes de dados e soluções. 

* Conheça as [soluções de monitoramento](../../azure-monitor/insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também coletam dados no espaço de trabalho do Log Analytics.
