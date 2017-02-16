---
title: "Visão geral do Azure Resource Health | Microsoft Docs"
description: "Visão geral do Azure Resource Health"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: resource-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 06/01/2016
ms.author: BernardoAMunoz
translationtype: Human Translation
ms.sourcegitcommit: d777bc6bd477c5b6645fc8bd7b6d57a5d2f89e22
ms.openlocfilehash: e465e2c1503add186a4b134e85bd9aab61d5c0ad


---
# <a name="azure-resource-health-overview"></a>Visão geral do Azure Resource Health
O Azure Resource Health é um serviço que expõe a integridade de recursos individuais do Azure e fornece diretrizes acionáveis para solucionar problemas. Em um ambiente de nuvem em que não é possível acessar diretamente os servidores ou elementos da infraestrutura, a meta do Resource Health é reduzir o tempo que os clientes gastam na solução de problemas, reduzindo especialmente o tempo gasto para determinar se a raiz do problema reside no aplicativo ou se o problema foi causado por algum evento na plataforma do Azure.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>O que é considerado um recurso e como o Resource Health decide se o recurso está íntegro ou não?
Um recurso é uma instância criada pelo usuário de um tipo de recurso fornecido por um serviço; por exemplo: uma máquina virtual, um aplicativo Web ou um banco de dados SQL. 

O Resource Health se baseia em sinais emitidos pelo recurso e/ou pelo serviço para determinar se um recurso está íntegro ou não. É importante observar que, no momento, o Resource Health só indica a integridade de um tipo específico de recurso e não leva em consideração outros elementos que podem contribuir para a integridade geral. Por exemplo, ao relatar o status de uma máquina virtual, somente a parte de computação da infraestrutura é considerada, ou seja, problemas na rede não são mostrados no Resource Health, a menos que haja uma interrupção de serviço declarada. Nesse caso, ela será mostrada por meio da faixa na parte superior da folha. Mais informações sobre a interrupção de serviço são fornecidas mais adiante neste artigo. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Como o Resource Health é diferente do Painel de Integridade de Serviço?
As informações fornecidas pelo Resource Health são mais granulares do que aquelas fornecidas pelo Painel de Integridade de Serviço. Embora o SHD comunique eventos que afetam a disponibilidade de um serviço em uma região, o Resource Health expõe informações relevantes a um recurso específico. Por exemplo, ela expõe eventos que afetam a disponibilidade de uma máquina virtual, um aplicativo Web ou um banco de dados SQL. Por exemplo, se um nó for reiniciado inesperadamente, os clientes cujas máquinas virtuais estavam em execução nesse nó poderão obter a razão pela qual suas VMs não estiveram disponíveis por um período de tempo.   

## <a name="how-to-access-resource-health"></a>Como acessar o Resource Health
Para os serviços disponíveis por meio do Resource Health, há duas maneiras de acessar o Resource Health.

### <a name="azure-portal"></a>Portal do Azure
A folha Integridade de recursos no Portal do Azure fornece informações detalhadas sobre a integridade do recurso, bem como ações recomendadas, que variam dependendo da integridade atual do recurso. Essa folha fornece a melhor experiência ao se consultar o Resource Health, pois facilita o acesso a outros recursos no portal. Como mencionado anteriormente, o conjunto de ações recomendadas na folha Integridade de recursos varia com base na integridade atual:

* Recursos íntegros: como não foi detectado nenhum problema que possa afetar a integridade do recurso, as ações são voltadas para ajudar no processo de solução de problemas. Por exemplo, fornece acesso direto à folha Solução de problemas, que oferece orientação sobre como resolver os mais problemas comuns enfrentados pelos clientes.
* Recurso não íntegro: para problemas causados pelo Azure, a folha exibirá ações que a Microsoft está executando (ou executou) para recuperar o recurso. Para problemas causados por ações iniciadas pelo usuário, a folha mostrará uma lista de clientes de ações que os clientes podem executar para solucionar o problema e recuperar o recurso.  

Após fazer logon no Portal do Azure, há duas maneiras de acessar a folha Integridade de recursos: 

### <a name="open-the-resource-blade"></a>Abrir a folha Recursos
Abra a folha Recursos de um recurso específico. Na folha à esquerda que é aberta próxima à folha Recurso, clique em Integridade do Recurso em Suporte + Solução de Problemas para abrir a folha Integridade do recurso. 

![Folha Integridade de recursos](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Folha de Ajuda e Suporte
Abra a folha Ajuda e Suporte clicando no ponto de interrogação no canto superior direito e selecionando Ajuda + Suporte. 

**Na barra de navegação superior**

![Ajuda + suporte](./media/resource-health-overview/HelpAndSupport.png)

Na folha à esquerda que é aberta próxima à folha Ajuda + Suporte, clique em Integridade de Recurso em Suporte + Solução de Problemas. Clicar nessa opção abre a folha de assinatura de Integridade do recurso, que lista todos os recursos em sua assinatura. Ao lado de cada recurso, há um ícone que indica sua integridade. Clicar em cada recurso abrirá a folha Integridade de recursos.

**Bloco de integridade de recursos**

![Bloco de integridade de recursos](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>O que significa o status de integridade do recurso?
Há quatro status de integridade diferentes que você pode ver para o recurso.

### <a name="available"></a>Disponível
O serviço não detectou problemas na plataforma que possam afetar a disponibilidade do recurso. Isso é indicado por um ícone de marca de seleção verde. 

![O recurso está disponível](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível
Nesse caso, o serviço detectou um problema contínuo na plataforma que está afetando a disponibilidade deste recurso; por exemplo, o nó em que a VM está em execução foi reiniciado inesperadamente. Isso é indicado por um ícone de aviso vermelho. Informações adicionais sobre o problema são fornecidas na seção no meio da folha, incluindo: 

1. As ações que a Microsoft está executando para recuperar o recurso 
2. Uma linha do tempo detalhada do problema, incluindo o tempo de resolução esperado
3. Uma lista de ações recomendadas para usuários 

![O recurso não está disponível](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>Não disponível - iniciado pelo cliente
O recurso não está disponível devido a uma solicitação de cliente, como a interrupção de um recurso ou a solicitação de uma reinicialização. Isso é indicado por um ícone azul informativo. 

![O recurso não está disponível devido a uma ação iniciada pelo usuário](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Desconhecido
O serviço não recebeu informações sobre esse recurso por mais de cinco minutos. Isso é indicado por um ícone de ponto de interrogação cinza. 

É importante observar que isso não é uma indicação definitiva de que há algo errado com um recurso. Portanto, os clientes devem seguir estas recomendações:

* Se o recurso está sendo executado conforme o esperado, mas sua integridade está definida como Desconhecida no Resource Health, não há problemas e você pode esperar que o status do recurso seja atualizado para Íntegro após alguns minutos.
* Se houver problemas ao acessar o recurso e sua integridade estiver definida como Desconhecida no Resource Health, isso poderá ser uma indicação inicial de que pode haver um problema, e investigações adicionais deverão ser realizadas até que a integridade seja atualizada para Íntegro ou Não íntegro

![A integridade do recurso é desconhecida](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Eventos que afetam o serviço
Se o recurso puder ser afetado por um Evento que Afeta o Serviço em andamento, será exibida uma faixa na parte superior da folha Integridade de recursos. Clicar na faixa abrirá a folha Eventos de Auditoria, que exibirá informações adicionais sobre a interrupção.

![A integridade de recursos pode ser afetada por um SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>O que mais preciso saber sobre o Resource Health?
### <a name="signal-latency"></a>Latência de sinal
Os sinais que são usados pelo Resource Health podem ter um atraso de até 15 minutos, o que pode causar discrepâncias entre o status de integridade atual do recurso e sua disponibilidade real. É importante ter isso em mente, pois ajudará a eliminar o tempo desnecessário gasto na investigação de possíveis problemas. 

### <a name="special-case-for-sql"></a>Caso especial para o SQL
O Resource Health relata o status do banco de dados SQL, não do SQL Server. Embora essa opção forneça uma imagem mais realista da integridade, requer que vários componentes e serviços sejam levados em consideração para determinar a integridade do banco de dados. O sinal atual se baseia em logons no banco de dados, o que significa que, para bancos de dados que recebem logons regulares (o que inclui, entre outras coisas, receber solicitações de execução de consulta), o status de integridade será exibido regularmente. Se o banco de dados não tiver sido acessado por um período de 10 minutos ou mais, será movido para o estado desconhecido. Isso não significa que o banco de dados esteja indisponível, apenas que nenhum sinal foi emitido porque nenhum logon foi executado. Conectar-se ao banco de dados e executar uma consulta emitirá os sinais necessários para determinar e atualizar o status de integridade do banco de dados.

## <a name="feedback"></a>Comentários
Estamos sempre abertos a comentários e sugestões! Envie-nos suas [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, você pode interagir conosco pelo [Twitter](https://twitter.com/azuresupport) ou nos [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).




<!--HONumber=Jan17_HO2-->


