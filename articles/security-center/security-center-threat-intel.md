---
title: Mapa de alerta de segurança na Central de Segurança do Azure e de inteligência de ameaças | Microsoft Docs
description: Saiba como usar a funcionalidade de mapa de alerta de segurança e inteligência contra ameaças na Central de Segurança do Azure para identificar possíveis ameaças em VMs e computadores.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: f1e238890b777e2816648ec407e2581f636d1c12
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57239820"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Inteligência contra ameaças e mapa de alertas de segurança
Este artigo ajuda você a usar o mapa de alertas da Central de Segurança do Azure e o mapa de inteligência de ameaças baseada em eventos de segurança para solucionar problemas relacionados à segurança.

## <a name="how-the-security-alerts-map-works"></a>Como mapear os trabalhos do mapa de alertas
A Central de Segurança fornece a você com um mapa que ajuda você a identifica ameaças de segurança no ambiente. Por exemplo, você pode identificar se determinado computador faz parte de um botnet, e onde a ameaça é proveniente. Computadores podem se tornar nós em um botnet quando os invasores instalam de forma ilícita malware que secretamente interage com o comando e controle que gerencia o botnet. 

Para criar esse mapa, a Central de Segurança usa dados que vêm de várias fontes da Microsoft. Central de Segurança do Azure usa esses dados para mapear as ameaças potenciais ao seu ambiente. 

Uma das etapas de um processo de [resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, você deverá realizar as seguintes tarefas:

- Determinar a natureza do ataque.
- Determine o ponto de origem do ataque.
- Determinar o objetivo do ataque. O ataque foi direcionado à sua organização para adquirir informações específicas ou foi aleatório?
- Identificar os sistemas que foram comprometidos.
- Identificar os arquivos que foram acessados e determinar a confidencialidade desses arquivos.

Você pode usar o mapa de alertas de Segurança na Central de Segurança do Azure para ajudar com essas tarefas.

## <a name="access-the-security-alerts-map"></a>Mapa de alertas de segurança de acesso
Para visualizar as ameaças atuais em seu ambiente, abra o mapa de alertas de segurança:

1. Abra o painel **Central de Segurança**.
2. No painel esquerdo, em **proteção contra ameaças** selecionar **Mapa de alertas de segurança**. O mapa abre.
3. Para obter mais informações sobre o alerta e receber as etapas de correção, clique no ponto do Alerta no mapa e siga as instruções. 
 
O mapa de alertas de segurança é baseado em alertas. Esses alertas são baseados nas atividades de para qual rede de comunicação foi associada a um endereço IP que foi resolvido com êxito, se o endereço IP é um endereço IP arriscado conhecido (por exemplo, um cryptominer conhecido) ou um endereço IP que não é reconhecido anteriormente como arriscado. O mapa fornece alertas em todas as assinaturas que você selecionou anteriormente no Azure. 

Os alertas no mapa são exibidos de acordo com a localização geográfica onde elas forem detectadas como sendo proveniente e são codificadas por severidade. 
    ![Informações de inteligência contra ameaças](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Exibindo o painel de inteligência contra ameaças baseado em evento
Para exibir o mapa de inteligência contra ameaças com base em eventos de segurança bruto, você pode seguir este procedimento. Este mapa exibe somente os eventos que envolvem um endereço IP que é considerado um risco, por exemplo, um endereço IP de um botnet conhecido.

1. Abra o painel **Central de Segurança**.

1. No painel esquerdo, em **proteção contra ameaças** selecionar **Mapa de alertas de segurança**. O mapa abre.
2. No canto superior direito, clique em **Ir para o mapa de eventos de segurança**.
3. Selecione o espaço de trabalho para o qual você deseja exibir o painel.
4. Na parte superior do mapa, selecione **Exibir a inteligência contra ameaças clássica**. O painel **Inteligência contra ameaças** é aberto.

   > [!NOTE]
   > Se a coluna à direita mostra **ATUALIZAR PLANO**, esse workspace está usando a assinatura gratuita. Atualize para Standard a fim de usar esse recurso. Se a coluna direita mostra **requer atualização**, atualize [registra em log do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esse recurso. Para saber mais sobre o plano de preço, leia Preços da Central de Segurança do Azure.
   >
5. Se você tem mais de um workspace para investigar, priorize a investigação de acordo com a coluna **IP Mal-intencionado**. Ela mostra o número atual de IPs mal-intencionadas no workspace. Selecione o workspace que você deseja usar e o painel **Inteligência contra ameaças** será exibido.

    ![Informações de inteligência contra ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. O painel é dividido em quatro blocos:

     a.  **Tipos de ameaça**. Resume os tipos de ameaça que foram detectados no workspace selecionado.

    b.  **País de origem**. Agrega a quantidade de tráfego de acordo com seu local de origem.

    c.  **Local da ameaça**. Ajuda a identificar os locais atuais em todo o mundo que se comunicam com o seu ambiente. No mapa mostrado, setas em laranja (entrada) e vermelho (saída) identificam o sentido do tráfego. Se você seleciona uma essas setas, o tipo de ameaça e o sentido do tráfego são exibidos.

    d.  **Detalhes das ameaças**. Mostra mais detalhes sobre a ameaça que você selecionou no mapa.

Independentemente do bloco de opções selecionado, o painel exibido se baseia na consulta da Pesquisa de Logs. A única diferença é o tipo de consulta e o resultado.

### <a name="threat-types"></a>Tipos de ameaça
Selecione o bloco **Tipos de ameaça** para abrir o painel **Pesquisa de Logs**. As opções de filtro aparecem à esquerda e os resultados da consulta aparecem à direita.

![Pesquisa de log](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

O resultado da consulta mostra as ameaças por nome. Você pode usar o painel esquerdo para selecionar o atributo que deseja filtrar. Por exemplo, para ver apenas as ameaças que estão conectadas às máquinas no momento, em **ESTADO DE SESSÃO**, selecione **Conectado** > **Aplicar**.

![Estado de sessão](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Para VMs do Azure, somente os dados de rede que fluem pelo agente aparecem no painel **Inteligência contra ameaças**. Os seguintes tipos de dados também são usados pela inteligência contra ameaças:

- Dados CEF (Type=CommonSecurityLog)
- WireData (Type= WireData)
- Logs do IIS (Type=W3CIISLog)
- Firewall do Windows (Type=WindowsFirewall)
- Eventos do DNS (Type=DnsEvents)


## <a name="see-also"></a>Consulte também
Neste artigo, você aprendeu a usar a inteligência contra ameaças na Central de Segurança para ajudar a identificar atividades suspeitas. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
