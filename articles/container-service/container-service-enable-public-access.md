---
title: "Habilitar o acesso público a um aplicativo ACS | Microsoft Docs"
description: "Como habilitar o acesso público a um Serviço de Contêiner do Azure."
services: container-service
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure"
ms.assetid: 5dea3c4d-a687-4024-93ea-f7a9a7243ab4
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9491ffb2683063169ab25c38b3db5de06282d654


---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Habilitar acesso público a um aplicativo do Serviço de Contêiner do Azure
Todos os contêineres DC/OS no [pool de agentes públicos](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) do ACS são automaticamente expostos à Internet. Por padrão, as portas **80**, **443**, **8080** estão abertas e qualquer contêiner (público) que esteja ouvindo nessas portas estará acessível. Este artigo mostra como abrir mais portas para seus aplicativos no Serviço de Contêiner do Azure.

## <a name="open-a-port-portal"></a>Abrir uma porta (portal)
Primeiro, é necessário abrir a porta que queremos.

1. Faça logon no portal.
2. Localize o grupo de recursos em que você implantou o Serviço de Contêiner do Azure.
3. Selecione o balanceador de carga do agente (que tem um nome semelhante a **XXXX-agent-lb-XXXX**).
   
    ![Balanceador de carga do serviço de contêiner do Azure](media/container-service-dcos-agents/agent-load-balancer.png)
4. Clique em **Investigações** e então em **Adicionar**.
   
    ![Investigações do balanceador de carga do serviço de contêiner do Azure](media/container-service-dcos-agents/add-probe.png)
5. Preencha o formulário de investigação e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo da investigação. |
   | Port |A porta do contêiner a ser testado. |
   | Caminho |(Quando em modo HTTP) O caminho relativo do site para investigação. Não há suporte para HTTPS. |
   | Intervalo |O tempo entre as tentativas de investigação, em segundos. |
   | Limite não íntegro |Número de tentativas consecutivas de investigação antes de considerar o contêiner não íntegro. |
6. De volta às propriedades do balanceador de carga do agente, clique em **Regras de balanceamento de carga** e em **Adicionar**.
   
    ![Regras do balanceador de carga do serviço de contêiner do Azure](media/container-service-dcos-agents/add-balancer-rule.png)
7. Preencha o formulário do balanceador de carga e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo do balanceador de carga. |
   | Port |A porta de entrada pública. |
   | Porta de back-end |A porta pública interna do contêiner para o qual o tráfego será roteado. |
   | Pool de back-end |Os contêineres nesse pool serão o destino para este balanceador de carga. |
   | Investigação |A investigação usada para determinar se um destino no **Pool de back-end** está íntegro. |
   | Persistência de sessão |Determina como o tráfego de um cliente deve ser tratado durante a sessão.<br><br>**Nenhum**: solicitações sucessivas do mesmo cliente podem ser tratadas por qualquer contêiner.<br>**IP do cliente**: solicitações sucessivas do mesmo IP do cliente são manipuladas pelo mesmo contêiner.<br>**IP do cliente e o protocolo**: as solicitações sucessivas da mesma combinação de IP e o protocolo de cliente são manipuladas pelo mesmo contêiner. |
   | Tempo limite de ociosidade |(Somente TCP) Em minutos, o tempo para manter um cliente TCP/HTTP aberto sem depender de mensagens *keep-alive* . |

## <a name="add-a-security-rule-portal"></a>Adicionar uma regra de segurança (portal)
Em seguida, precisamos adicionar uma regra de segurança que encaminhe o tráfego da nossa porta aberta por meio do firewall.

1. Faça logon no portal.
2. Localize o grupo de recursos em que você implantou o Serviço de Contêiner do Azure.
3. Selecione o grupo de segurança de rede de agente **público** (que tem um nome semelhante a **XXXX-agent-public-nsg-XXXX**).
   
    ![Grupo de segurança de rede do serviço de contêiner do Azure](media/container-service-dcos-agents/agent-nsg.png)
4. Selecione **Regras de segurança de entrada** e **Adicionar**.
   
    ![Regras do grupo de segurança de rede do serviço de contêiner do Azure](media/container-service-dcos-agents/add-firewall-rule.png)
5. Preencha a regra de firewall para permitir sua porta pública e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo da regra do firewall. |
   | Prioridade |Classificação de prioridade para a regra. Quanto menor o número, maior a prioridade. |
   | Fonte |Restrinja o intervalo de endereços IP de entrada a ser permitido ou negado por essa regra. Use **Qualquer** para não especificar uma restrição. |
   | O Barramento de |Selecione um conjunto de serviços predefinidos para os quais foi definida essa regra de segurança. Caso contrário, use **Personalizada** para criar suas próprias. |
   | Protocolo |Restrinja o tráfego baseado em **TCP** ou **UDP**. Use **Qualquer** para não especificar uma restrição. |
   | Intervalo de portas |Quando **Serviço** for **Personalizado**, especifica o intervalo de portas afetadas por essa regra. Você pode usar uma única porta, como **80** ou um intervalo, como **1024–1500**. |
   | Ação |Permita ou negue o tráfego que atenda aos critérios. |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a diferença entre [agentes DC/OS públicos e privados](container-service-dcos-agents.md).

Leia mais informações sobre [como gerenciar seus contêineres de DC/OS](container-service-mesos-marathon-ui.md).




<!--HONumber=Nov16_HO3-->


