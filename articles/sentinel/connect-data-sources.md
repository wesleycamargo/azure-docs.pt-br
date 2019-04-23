---
title: Conectar fontes de dados à versão prévia do Azure Sentinel? | Microsoft Docs
description: Saiba como conectar fontes de dados ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: ba0f584e8026fe3828ec79c4b6c0ff5a0bb89f5a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492355"
---
# <a name="connect-data-sources"></a>Conectar fontes de dados

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Para a integração do Azure Sentinel, você precisa primeiro se conectar às suas fontes de dados. O Azure Sentinel vem com uma série de conectores para soluções da Microsoft, disponíveis prontas para o uso e fornecendo integração em tempo real, incluindo soluções de Proteção contra Ameaças da Microsoft 365, e fontes do Microsoft 365, tais como Office 365, Azure AD, Azure ATP, Microsoft Cloud App Security e muito mais. Além disso, existem conectores internos no ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar formato comum de eventos, Syslog ou API REST para conectar suas fontes de dados ao Azure Sentinel.  

![Coletores de dados](./media/collect-data/collect-data-page.png)

## <a name="data-connection-methods"></a>Métodos de conexão de dados

O Azure Sentinel dá suporte aos métodos de conexão de dados a seguir:

- **Serviços Microsoft**:<br> os serviços da Microsoft são conectados nativamente, utilizando a base do Azure para integração pronta para o uso. É possível conectar as soluções a seguir em poucos cliques:
    - [Office 365](connect-office-365.md)
    - [Entradas e logs de auditoria do Azure AD](connect-azure-active-directory.md)
    - [Atividades do Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Central de Segurança do Azure](connect-azure-security-center.md)
    - [Proteção de Informações do Azure](connect-azure-information-protection.md)
    - [Proteção Avançada contra Ameaças do Azure](connect-azure-atp.md)
    - [Segurança de Aplicativo de Nuvem](connect-cloud-app-security.md)
    - [Eventos de segurança do Windows](connect-windows-security-events.md) 
    - [Firewall do Windows](connect-windows-firewall.md)

- **Soluções externas por meio de API**: algumas fontes de dados são conectados por meio de APIs fornecidas pela fonte de dados conectada. Normalmente, a maioria das tecnologias de segurança fornece um conjunto de APIs por meio das quais os logs de eventos podem ser recuperados. As APIs se conectam ao Azure Sentinel e reúnem tipos de dados específicos e os enviam à Análise de Logs do Azure. Os dispositivos conectados por meio de API incluem:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Soluções externas por meio de agente**: o Azure Sentinel pode ser conectado a todas as outras fontes de dados capazes de executar streaming de log em tempo real usando o protocolo Syslog, por meio de um agente. <br>A maioria dos dispositivos usa o protocolo Syslog para enviar mensagens de eventos que incluem o próprio log e dados sobre o log. O formato dos logs varia. No entanto, a maioria dos dispositivos dá suporte ao padrão CEF (Formato Comum de Evento). <br>O agente do Azure Sentinel, que se baseia no agente OMS, converte logs formatados como CEF em um formato que pode ser ingerido pela Análise de Logs. Dependendo do tipo de dispositivo, o agente é instalado diretamente no dispositivo ou em um servidor dedicado do Linux. O agente para Linux recebe eventos do daemon Syslog por UDP. Porém, em casos em que um computador Linux deve coletar um alto volume de eventos de Syslog, eles são enviados por meio de TCP do daemon Syslog para o agente e de lá para a Análise de Logs.
    - Firewalls, proxies e pontos de extremidade:
        - [F5](connect-f5.md)
        - [Ponto de Verificação](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Outros dispositivos CEF](connect-common-event-format.md)
        - [Outros dispositivos Syslog](connect-syslog.md)
    - Soluções de DLP
    - [Provedores de inteligência contra ameaças](connect-threat-intelligence.md)
    - [Computadores DNS](connect-dns.md) - agente instalado diretamente no computador DNS
    - Servidores Linux
    - Outras nuvens
    
## Opções de conexão de agente<a name="agent-options"></a>

Para conectar seu dispositivo externo ao Azure Sentinel, o agente precisa ser implantado em um computador dedicado (VM ou local) para dar suporte à comunicação entre o dispositivo e o Azure Sentinel. Você pode implantar o agente manualmente ou automaticamente. A implantação automática só estará disponível se o computador dedicado for uma nova VM que você está criando no Azure. 


![CEF no Azure](./media/connect-cef/cef-syslog-azure.png)

Como alternativa, você pode implantar o agente manualmente em uma VM do Azure existente em uma VM em outra nuvem ou em um computador local.

![CEF no local](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Azure Sentinel, você precisa ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [integrar seus dados ao Azure Sentinel](quickstart-onboard.md) e [obtenha visibilidade de seus dados, além de possíveis ameaças](quickstart-get-visibility.md).
