---
title: "Monitorando e respondendo a alertas de segurança na solução de Segurança e Auditoria do Operations Management Suite | Microsoft AzurDocs"
description: "Este documento o ajuda a usar a opção de inteligência contra ameaças disponível na Segurança e Auditoria do OMS para monitorar e responder a alertas de segurança."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7161cacfd371aa73974e635a343793bbec76d858


---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Monitorando e respondendo a alertas de segurança na solução de Segurança e Auditoria do Operations Management Suite
Este documento o ajuda a usar a opção de inteligência contra ameaças disponível na Segurança e Auditoria do OMS para monitorar e responder a alertas de segurança.

## <a name="what-is-oms"></a>O que é o OMS?
O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem. Para obter mais informações sobre o OMS, leia o artigo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Inteligência contra ameaças
Em um ambiente empresarial no qual os usuários têm amplo acesso à rede e usam uma variedade de dispositivos para se conectar a dados corporativos, é fundamental que você possa monitorar de forma ativa seus recursos e responder rapidamente a incidentes de segurança. Isso é especificamente importante da perspectiva do ciclo de vida de segurança, pois algumas ameaças de segurança cibernética podem não gerar alertas nem atividades suspeitas que possam ser identificadas pelos controles técnicos tradicionais de segurança. 

Ao usar a opção **Inteligência contra Ameaças** disponível na Segurança e Auditoria do OMS, os administradores de TI podem identificar as ameaças à segurança no ambiente, por exemplo, identificar se determinado computador faz parte de um [botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Os computadores podem se tornar nós em um botnet quando os invasores instalam de forma ilícita malware que se conecta secretamente esse computador ao comando e controle. Ela também pode identificar ameaças potenciais recebidas de canais de comunicação underground, como [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

A fim de criar essa inteligência contra ameaças, a Segurança e Auditoria do OMS usam dados recebidos de várias fontes da Microsoft. A Segurança e Auditoria do OMS aproveitará esses dados para identificar ameaças potenciais ao seu ambiente.

O painel Inteligência contra Ameaças é formado por três opções principais:

* Servidores com o tráfego de saída mal-intencionado
* Tipos de ameaças detectados
* Mapa de inteligência contra ameaças

> [!NOTE]
> para obter uma visão geral de todas essas opções, leia [Introdução à solução de Segurança e Auditoria do Operations Management Suite](oms-security-getting-started.md).
> 
> 

### <a name="responding-to-security-alerts"></a>Respondendo a alertas de segurança
Uma das etapas de um processo de [resposta a incidentes de segurança](https://technet.microsoft.com/library/cc512623.aspx) é identificar a severidade do comprometimento do(s) sistema(s). Nesta fase, você deverá realizar as seguintes tarefas:

* Determinar a natureza do ataque
* Determinar o ponto de ataque de origem
* Determinar o objetivo do ataque. O ataque foi direcionado especificamente em sua organização para adquirir informações específicas ou foi aleatório?
* Identificar os sistemas que foram comprometidos
* Identificar os arquivos que foram acessados e determinar a confidencialidade desses arquivos

Você pode aproveitar as informações da **Inteligência contra Ameaças** na solução de Segurança e Auditoria do OMS para ajudar com essas tarefas. Siga as etapas abaixo para acessar estas opções da **Inteligência contra Ameaças** :

1. No painel principal do **Microsoft Operations Management Suite**, clique no bloco **Segurança e Auditoria**.
   
    ![Segurança e Auditoria](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. No painel **Segurança e Auditoria**, você verá as opções **Inteligência de Ameaças** à direita, conforme mostrado abaixo:
   
    ![Threat Intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Estes três blocos fornecerão uma visão geral das ameaças atuais. No **Servidor com tráfego de saída mal-intencionado** , você poderá identificar se há qualquer computador monitorado (dentro ou fora de sua rede) que esteja enviando tráfego mal-intencionado para a Internet. 

O bloco **Tipos de ameaças detectados** mostra um resumo das ameaças que, atualmente estão “fora de controle”; se você clicar nesse bloco, verá mais detalhes sobre essas ameaças, conforme mostrado abaixo:

![Tipos de ameaças detectados](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

É possível extrair mais informações sobre cada ameaça clicando nela. O exemplo abaixo mostra mais detalhes sobre o Botnet:

![mais detalhes sobre uma ameaça](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Conforme descrito no início desta seção, essas informações podem ser muito úteis durante um caso de resposta a incidentes. Elas também podem ser importantes durante uma investigação forense, na qual você precisa encontrar a origem do ataque, qual sistema foi comprometido e a linha do tempo. Neste relatório, você pode identificar com facilidade alguns detalhes básicos sobre o ataque, tais como: a origem do ataque, o IP local comprometido e o estado da sessão atual da conexão. 

O **mapa de inteligência contra ameaças** o ajudará a identificar os locais atuais em todo o mundo com tráfego mal-intencionado. Neste mapa, há setas laranja (entrada) e vermelhas (saídas) que identificam a direção do tráfego; se você clicar em um dessas setas, ela mostrará o tipo de ameaça e a direção do tráfego, conforme mostrado abaixo:

![mapa do threat intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> Você pode ver uma demonstração sobre como usar essa funcionalidade durante um processo de resposta a incidentes assistindo à apresentação [Mitigar as ameaças de segurança de datacenter com investigação guiada usando o Operations Management Suite](https://myignite.microsoft.com/videos/5000) entregue no Microsoft Ignite.
> 
> 

## <a name="see-also"></a>Confira também
Neste documento, você aprendeu a usar a opção **Inteligência contra Ameaças** na solução de Segurança e Auditoria do OMS para responder a alertas de segurança. Para saber mais sobre a Segurança do OMS, veja os seguintes artigos:

* [Operations Management Suite (OMS) overview](operations-management-suite-overview.md)
* [Introdução à solução de Segurança e Auditoria do Operations Management Suite](oms-security-getting-started.md)
* [Monitorando recursos na solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)




<!--HONumber=Nov16_HO3-->


