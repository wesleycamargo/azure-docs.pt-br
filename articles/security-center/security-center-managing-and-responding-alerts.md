---
title: "Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda você a usar recursos da Central de segurança do Azure para gerenciar e responder a alertas de segurança."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c0a1cf550c46810624d9eee7722409ff19b75369


---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure
Este documento ajuda você a usar a Central de Segurança do Azure para gerenciar e responder a alertas de segurança.

> [!NOTE]
> Para habilitar as detecções avançadas, atualize para o Padrão da Central de Segurança do Azure. Há uma avaliação gratuita de 90 dias disponível. Para atualizar, selecione a Camada de Preços na [Política de Segurança](security-center-policies.md). Consulte [Preços da Central de Segurança do Azure](security-center-pricing.md) para saber mais.
> 
> 

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque. A Central de Segurança do Azure também agrega alertas que se alinham para eliminar os padrões em cadeia como [Incidentes](security-center-incident.md). 

> [!NOTE]
> Para saber mais sobre como funciona os recursos de detecção da Central de Segurança, leia [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md).
> 
> 

## <a name="managing-security-alerts"></a>Configurando alertas de segurança
Você pode examinar os alertas atuais observando o bloco **Alertas de segurança** . Abra o Portal do Azure e siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No painel Central de Segurança, você verá o bloco **Alertas de segurança** .
   
    ![Bloco Alertas de segurança na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)
2. Clique no bloco para abrir a folha **Alertas de segurança** , que contém mais detalhes sobre os alertas, conforme mostrado abaixo.
   
   ![A folha Alertas de segurança na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Na parte inferior dessa folha estão os detalhes de cada alerta. Para classificar, clique na coluna com base na qual você deseja classificar. A definição de cada coluna é mostrada abaixo:

* **Alerta**: uma breve explicação sobre o alerta.
* **Contagem**: uma lista de todos os alertas desse tipo específico que foram detectados em um dia específico.
* **Detectado por**: o serviço responsável por disparar o alerta.
* **Data**: a data na qual o evento ocorreu.
* **Estado**: o estado atual desse alerta. Há dois tipos de estado:
  
  * **Ativo**: o alerta de segurança foi detectado.
  * **Descartado**: o alerta de segurança foi fechado pelo usuário. Esse status costuma ser usado para alertas que foram investigados, mas que foram atenuados ou considerados como não sendo ataques reais
* **Gravidade**: o nível de gravidade, que pode ser alta, média ou baixa.

### <a name="filtering-alerts"></a>Filtragem de alertas
Você pode filtrar com base na data, no estado e na gravidade dos alertas. A filtragem de alertas pode ser útil para cenários em que é necessário restringir o escopo da exibição de alertas de segurança. Por exemplo, convém lidar com os alertas de segurança que ocorreram nas últimas 24 horas, pois você está investigando uma possível falha no sistema.

1. Clique em **Filtrar** na folha **Alertas de Segurança**. A folha **Filtrar** é aberta e você seleciona os valores de data, estado e gravidade que deseja ver.
   
    ![Filtragem de alertas na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)
2. Após investigar um alerta de segurança, você pode achar que ele é um falso positivo para seu ambiente ou indica um comportamento esperado para determinado recurso. Seja qual for o caso, se determinar que um alerta de segurança não é aplicável, você poderá ignorá-lo e, em seguida, filtrá-lo da exibição. Há duas maneiras de ignorar um alerta de segurança. Clique com o botão direito do mouse em um alerta e selecione **Ignorar** ou passe o mouse sobre um item, clique nos três pontos que aparecem à direita e selecione **Ignorar**. Você pode exibir os alertas de segurança ignorados clicando em **Filtrar** e selecionando **Ignorados**.
   
   ![Como ignorar alertas na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### <a name="respond-to-security-alerts"></a>Responder a alertas de segurança
Selecione um alerta de segurança para saber mais sobre o evento que disparou o alerta e, se houver, as etapas necessárias para corrigir um ataque. Os alertas de segurança são agrupados por tipo e data. Um alerta de segurança permite abrir uma folha que contém uma lista dos alertas agrupados.

![Responder aos alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Nesse caso, os alertas disparados referem-se à atividade suspeita do protocolo RDP (Protocolo de Área de Trabalho Remota). A primeira coluna mostra quais recursos foram atacados; a segunda mostra quantas vezes o recurso foi atacado; a terceira mostra o horário do ataque; a quarta mostra o estado do alerta e a quarta mostra gravidade do ataque. Depois de revisar essas informações, clique no recurso que foi atacado e uma nova folha será aberta.

![Sugestões sobre o que fazer com relação aos alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

No campo **Descrição** dessa folha, você encontrará mais detalhes sobre esse evento. Esses detalhes adicionais oferecem informações sobre o que disparou o alerta de segurança, o recurso de destino e, quando aplicável, o endereço IP de origem e as recomendações sobre como corrigir.  Em alguns casos, o endereço IP de origem ficará vazio (não disponível) porque nem todos os logs de eventos de segurança do Windows incluem o endereço IP.

A correção sugerida pela Central de Segurança varia de acordo com o alerta de segurança. Em alguns casos, talvez seja necessário usar outros recursos do Azure para implementar a correção recomendada. Por exemplo, a correção para esse ataque é colocar o endereço IP que está gerando esse ataque em uma lista de contatos bloqueados usando uma [ACL de rede](../virtual-network/virtual-networks-acl.md) ou uma regra de [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> Para saber mais sobre os tipos diferentes de alertas, leia [Alertas de segurança por tipo na Central de segurança do Azure](security-center-alerts-type.md).
> 
> 

## <a name="see-also"></a>Confira também
Neste documento, você aprendeu a configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Manipulação de incidente de segurança na Central de Segurança do Azure](security-center-incident.md)
* [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço de localização.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.




<!--HONumber=Dec16_HO2-->


