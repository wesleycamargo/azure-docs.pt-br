---
title: Coletar dados do seu ambiente com o Azure Log Analytics | Microsoft Docs
description: "Este tópico ajuda você a entender como coletar dados e monitorar computadores hospedados em suas instalações ou em outro ambiente de nuvem com o Log Analytics."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: magoedte
ms.openlocfilehash: 513855084c8b89d97b049f1df2ec24d0f9789afe
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Coletar dados de computadores em seu ambiente com o Log Analytics

O Log Analytics do Azure pode coletar e agir sobre dados de computadores Windows ou Linux que residem em:

* [Máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md) usando a extensão de Log Analytics VM 
* Seu data center como servidores físicos ou máquinas virtuais
* Máquinas virtuais em um serviço hospedado em nuvem como os serviços do Amazon Web Services (AWS)

Computadores hospedados no seu ambiente podem ser conectados diretamente ao Log Analytics, ou se você já estiver monitorando esses computadores com o System Center Operations Manager 2012 R2 ou 2016, você pode integrar seu grupo de gerenciamento Operations Manage com o Logs Analytics e continue mantendo sua estratégia e processos de operações de serviço.  

## <a name="overview"></a>Visão geral

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Antes de analisar e atuar em dados coletados, você precisa instalar e conectar agentes para todos os computadores que deseja enviar dados ao serviço do Log Analytics. Você pode instalar agentes nos seus computadores locais usando a Instalação, a linha de comando ou com o DSC (Configuração de Estado Desejado) na Automação do Azure. 

O agente para Linux e Windows se comunica na saída com o serviço Log Analytics na porta TCP 443 e, caso o computador se conecte a um firewall ou servidor proxy para se comunicar pela Internet, examine [Configurando o agente para uso com um servidor proxy ou Gateway do OMS](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) para entender quais alterações de configuração precisarão ser aplicadas. Se você estiver monitorando o computador com o System Center 2016 – Operations Manager ou Operations Manager 2012 R2, ele poderá ser multihomed com o serviço Log Analytics para coletar dados e encaminhe para o serviço e ainda ser monitorado pelo [Operations Manager](log-analytics-om-agents.md). Os computadores Linux monitorados por um grupo de gerenciamento do Operations Manager integrado ao Log Analytics não recebem a configuração de fontes de dados e encaminham os dados coletados pelo grupo de gerenciamento. O agente para Windows pode relatar até quatro espaços de trabalho, enquanto o agente do Linux oferece suporte a relatórios somente para um único espaço de trabalho.  

O agente para Linux e Windows não é apenas para a conexão com o Log Analytics, também oferece suporte a conexão com a automação do Azure para hospedar a função Hybrid Runbook Worker e soluções de gerenciamento, como o controle de alterações e gerenciamento de atualizações.  Para obter mais informações sobre a função Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Automação do Azure](../automation/automation-offering-get-started.md#automation-architecture-overview).  

Se suas políticas de segurança não permitem que computadores em sua rede se conectem à Internet, o agente pode ser configurado para se conectar ao Gateway do OMS para receber informações de configuração e enviar os dados coletados dependendo da solução habilitada. Para obter mais informações e etapas sobre como configurar o agente para Linux ou Windows para se comunicar através de um Gateway do OMS ao serviço Log Analytics, consulte [Conectar computadores ao OMS usando o Gateway do OMS](log-analytics-oms-gateway.md). 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, examine os detalhes a seguir para verificar se você atende aos requisitos mínimos do sistema.

### <a name="windows-operating-system"></a>Sistema operacional Windows
Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2008 Service Pack 1 (SP1) ou posterior
* Windows 7 SP1 e posterior.

#### <a name="network-configuration"></a>Configuração de rede
As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agente do Windows se comunique com o Log Analytics. O tráfego é de saída da rede para o serviço Log Analytics. 

| Recurso de agente | Portas | Ignorar a inspeção de HTTPS|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Sim |
|*.oms.opinsights.azure.com | 443 | Sim | 
|*.blob.core.windows.net | 443 | Sim | 
|*.azure-automation.net | 443 | Sim | 

### <a name="linux-operating-systems"></a>Sistemas operacionais Linux
As seguintes distribuições Linux têm suporte oficialmente.  No entanto, o agente para Linux também pode ser executado em outras distribuições não listadas.

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

#### <a name="network-configuration"></a>Configuração de rede
As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agente do Linux se comunique com o Log Analytics. O tráfego é de saída da rede para o serviço Log Analytics. 

|Recurso de agente| Portas |  
|------|---------|  
|*.ods.opinsights.azure.com | Porta 443|   
|*.oms.opinsights.azure.com | Porta 443|   
|*.blob.core.windows.net | Porta 443|   
|*.azure-automation.net | Porta 443|  

O agente para Linux dá suporte à comunicação por meio de um servidor proxy ou de um gateway do OMS para o serviço Log Analytics usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  O servidor proxy pode ser especificado durante a instalação ou modificando o arquivo de configuração proxy.conf após a instalação.  

O valor de configuração de proxy tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se seu servidor proxy não exigir autenticação, o agente para Linux exigirá mesmo assim fornecendo um pseudo usuário/senha. Isso pode ser qualquer nome de usuário ou senha.

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | HTTPS |
|usuário | Nome de usuário opcional para autenticação de proxy |
|Senha | Senha opcional para autenticação de proxy |
|proxyhost | Endereço ou FQDN do servidor proxy/Gateway do OMS |
|porta | Número da porta opcional para o servidor proxy/OMS do Gateway |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se você usar caracteres especiais, como "@" em sua senha, você receberá um erro de conexão de proxy porque o valor é analisado incorretamente.  Para contornar esse problema, codifique a senha na URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalar e configurar o agente 
Conectar seus computadores locais diretamente com o Log Analytics pode ser feito usando métodos diferentes, dependendo dos seus requisitos. A tabela a seguir realça cada método para determinar o que funciona melhor em sua organização.

|Fonte | Método | Descrição|
|-------|-------------|-------------|
| Computador com Windows|- [Instalação manual](log-analytics-agent-windows.md)<br>- [DSC de Automação do Azure](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Instalar o agente Microsoft Monitoring da linha de comando ou usando um método automatizado como DSC de automação do Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), ou com um modelo do Azure Resource Manager, se você implantou o Microsoft Azure Stack no seu datacenter.| 
|Computador Linux| [Instalação manual](log-analytics-quick-collect-linux-computer.md)|Instale o agente para Linux chamando um script de wrapper hospedado no GitHub. | 
| System Center Operations Manager|[Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md) | Configure a integração entre o Operations Manager e o Log Analytics para encaminhar dados coletados de computadores Linux e Windows subordinados a um grupo de gerenciamento.|  

## <a name="next-steps"></a>Próximas etapas

* Consultar as [fontes de dados](log-analytics-data-sources.md) para entender as fontes de dados disponíveis para coletar dados do sistema Windows ou Linux. 

* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções. 

* Saiba mais sobre [soluções](log-analytics-add-solutions.md) que adicionam funcionalidades ao Log Analytics e também coletam dados para o repositório do OMS.