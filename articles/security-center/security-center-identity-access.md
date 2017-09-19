---
title: "Monitoramento de identidade e acesso na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda você a usar identidades e acesso problemas relacionados ao recurso na Central de Segurança do Azure para monitorar a atividade de acesso do usuário e identidade."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Monitoramento de identidade e acesso na Central de Segurança do Azure
Este documento ajuda você a usar a Central de Segurança do Azure para monitorar a identidade do usuário e atividade de acesso.

## <a name="why-monitor-identity-and-access"></a>Por que monitorar a identidade e o acesso?
A identidade deve ser o plano de controle de sua empresa e a proteção de sua identidade deve ser sua prioridade. Embora, no passado, existiam perímetros em torno das organizações e embora eles fossem um dos principais limites de defesa, hoje em dia, com um número maior de dados e aplicativos sendo movidos para a nuvem, a identidade tornou-se o novo perímetro.

Ao monitorar suas atividades de identidade, você poderá agir de forma proativa antes que um incidente local ocorra ou agir de forma reativa para interromper uma tentativa de ataque. O painel Identidade e Acesso fornece uma visão geral do estado de identidade, incluindo o número de tentativas de logon com falha, a conta do usuário que foi usada durante as tentativas, as contas que foram bloqueadas, as contas com alteração ou redefinição de senha e o número atual de contas que estão registradas.

## <a name="how-to-monitor-identity-and-access-activities"></a>Como monitorar a identidade e as atividades de acesso?
Siga as etapas abaixo para visualizar as atividades relacionadas a identidade atual do e acesso, você precisa acessar o painel **Identidade e acesso**:

1.  Abra o painel **Central de Segurança**.
2.  No painel esquerdo, em **prevenção**, clique EM **Identidade e Acesso**. Se você tiver vários espaços de trabalho, o seletor de espaço de trabalho será exibido.

    ![seleção de espaço de trabalho](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Se a última coluna mostra **PLANO DE ATUALIZAÇÃO**, esse espaço de trabalho está usando a assinatura gratuita e você precisa atualizar para Standard a fim de usar esse recurso. Se ela mostra REQUIRES UPDATE, você precisa atualizar o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esse recurso. Para saber mais sobre o plano de preço, leia Preços da Central de Segurança do Azure. 
    > 
3. Se você tiver mais de um espaço de trabalho para investigar, você pode priorizar a investigação de acordo com o **LOGONS COM FALHA** coluna que mostra o número atual sem êxito logons tentativas nesse espaço de trabalho. Selecione o espaço de trabalho que você deseja usar, e, em seguida, o painel **Identidade e acesso** é exibido.

    ![identidade e acesso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. As informações disponíveis neste painel podem ajudá-lo imediatamente a identificar uma atividade suspeita potencial. Este painel é dividido em três áreas principais:
    * **Postura de identidade**: resume a identidade relacionada atividades que estão tomando colocar neste espaço de trabalho.
    * **Falhas de logon**: ajuda a identificar rapidamente a principal causa da tentativa de logon com falha e mostra uma lista das contas de dez que mais com falha tentando fazer logon.
    * **Logons extras**: ajuda a identificar rapidamente a quantidade de horas extras de logon e mostra uma lista das principais tentativas de logon em contas de computador.
    
Independentemente do bloco selecionado, o painel será exibido se baseia o [consulta de Pesquisa de Log,](https://docs.microsoft.com/azure/security-center/security-center-search) a única diferença é o tipo de consulta e o resultado. Você ainda pode selecionar um item, um computador por exemplo, clicar nele e ver dados relevantes. 

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como monitorar a identidade e o acesso na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança. 
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.


