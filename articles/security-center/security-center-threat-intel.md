---
title: "Inteligência contra Ameaças da Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda você a usar o recurso de inteligência contra ameaças na Central de Segurança do Azure para identificar possíveis ameaças nas VMs e nos computadores."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Inteligência contra Ameaças da Central de Segurança do Azure
Este documento ensina a usar a Inteligência contra Ameaças da Central de Segurança do Azure para tratar de problemas de segurança.

## <a name="what-is-threat-intelligence"></a>O que é inteligência contra ameaças?
Ao usar a opção Inteligência contra Ameaças disponível na Central de Segurança, os administradores de TI podem identificar as ameaças à segurança no ambiente, por exemplo, se determinado computador faz parte de um botnet. Os computadores podem se tornar nós em um botnet quando os invasores instalam de forma ilícita malware que se conecta secretamente esse computador ao comando e controle. Ela também pode identificar ameaças potenciais recebidas de canais de comunicação underground, como darknet.

A fim de criar essa inteligência contra ameaças, a Central de Segurança usa dados recebidos de várias fontes da Microsoft. A Central de Segurança aproveita esses dados para identificar possíveis ameaças ao seu ambiente. O painel Inteligência contra Ameaças é formado por três opções principais:

- Tipos de ameaças detectados
- Origem da ameaça
- Mapa de inteligência contra ameaças


## <a name="when-should-i-use-threat-intelligence"></a>Quando devo usar inteligência contra ameaças?
Uma das etapas de um processo de [resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade do comprometimento dos sistemas. Nesta fase, você deverá realizar as seguintes tarefas:

- Determinar a natureza do ataque
- Determinar o ponto de ataque de origem
- Determinar o objetivo do ataque. O ataque foi direcionado especificamente em sua organização para adquirir informações específicas ou foi aleatório?
- Identificar os sistemas que foram comprometidos
- Identificar os arquivos que foram acessados e determinar a confidencialidade desses arquivos. Você pode aproveitar as informações de inteligência contra ameaças na Central de Segurança para ajudar nessas tarefas. 

## <a name="how-to-access-the-threat-intelligence"></a>Como acessar a inteligência contra ameaças?
Para visualizar a inteligência contra ameaças atual do seu ambiente, você deve primeiro selecionar o espaço de trabalho onde estão suas informações. Se você não tiver vários espaços de trabalho, não verá o seletor de espaço de trabalho e será levado para o painel **Inteligência contra Ameaças** diretamente. Siga as etapas abaixo para acessar o painel de Inteligência contra Ameaças:

1.  Abra o painel **Central de Segurança**.
2.  No painel esquerdo, em **Detecção** clique **Inteligência contra ameaças**. O painel **Inteligência contra ameaças** é exibido.

    ![Threat Intel](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Se a última coluna mostra **PLANO DE ATUALIZAÇÃO**, esse espaço de trabalho está usando a assinatura gratuita e você precisa atualizar para Standard a fim de usar esse recurso. Se ela mostra REQUIRES UPDATE, você precisa atualizar o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esse recurso. Para saber mais sobre o plano de preço, leia Preços da Central de Segurança do Azure. 
    > 
3. Se você tiver mais de um espaço de trabalho para investigar, priorize a investigação de acordo com a coluna **IP MAL-INTENCIONADO**, que mostra o número atual de IPs mal-intencionadas nesse espaço de trabalho. Selecione o espaço de trabalho que você deseja usar e o painel **Inteligência contra ameaças** será exibido.

    ![Threat Intel](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Este painel é dividido em quatro blocos:
    * **Tipos de ameaças**: resume o tipo de ameaça que foi detectadas no espaço de trabalho selecionado.
    * **País de origem**: agrega a quantidade de tráfego de acordo com seu local de origem.
    * **Local da ameaça**: ajuda a identificar os locais atuais em todo o mundo que se comunicam com o seu ambiente. Neste mapa, há setas em laranja (entrada) e vermelho (saídas) que identificam a direção do tráfego; se você clicar em um dessas setas, ela mostrará o tipo de ameaça e a direção do tráfego.
    * **Detalhes de ameaças**: mostra mais detalhes sobre a ameaça que você selecionou no mapa.

Independentemente do bloco de opção selecionado, o painel exibido se baseia na consulta da [Pesquisa de Logs](https://docs.microsoft.com/azure/security-center/security-center-search); a única diferença é o tipo de consulta e o resultado.

### <a name="threat-types"></a>Tipos de ameaça
Depois de clicar no bloco **Tipos de ameaça**, o painel **Pesquisa de Logs** é exibido e lá você tem as opções de filtro à esquerda e os resultados da consulta à direita.

![Pesquisa da inteligência contra ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

O resultado da consulta mostra as ameaças agregadas por nome. Você pode usar o painel esquerdo para selecionar o atributo que deseja filtrar, por exemplo, se quiser ver somente as ameaças atualmente conectadas às máquinas, selecione **Conectado**em **SESSIONSTATE**e clique no botão **Aplicar**.

![estado de sessão](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Para VMs do Azure, somente os dados de rede que fluem pelo agente serão exibidos no painel de inteligência contra ameaças. Os seguintes tipos de dados também são usados pela inteligência contra ameaças:

- Dados CEF (Type=CommonSecurityLog)
- WireData (Type= WireData)
- Logs do IIS (Type=W3CIISLog)
- Firewall do Windows (Type=WindowsFirewall)
- Eventos do DNS (Type=DnsEvents)


## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a usar a inteligência contra ameaças na Central de Segurança para ajudar a identificar atividades suspeitas. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança. 
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.


