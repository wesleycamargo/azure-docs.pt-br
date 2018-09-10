---
title: Monitoramento de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este artigo o ajuda a se familiarizar com o monitoramento de recursos na Central de segurança do Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: 434b73d4625f86fab195dbda1fed9c841791f5b6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099452"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Monitoramento de integridade de segurança na Central de segurança do Azure
Este artigo o ajuda a usar os recursos de monitoramento na Central de segurança do Azure para monitorar a conformidade com as políticas.

## <a name="what-is-security-health-monitoring"></a>O que é o monitoramento de integridade de segurança?
Costumamos pensar em monitoramento como assistir e esperar até que um evento ocorra para poder reagir à situação. Monitoramento de segurança refere-se a ter uma estratégia proativa que audita seus recursos para identificar sistemas que não atendem aos padrões organizacionais ou práticas recomendadas.

## <a name="monitoring-security-health"></a>Monitoramento de integridade da segurança
Depois de habilitar as [políticas de segurança](security-center-policies.md) para os recursos de uma assinatura, a Central de Segurança analisa a segurança de seus recursos para identificar as possíveis vulnerabilidades. Informações sobre a configuração de rede estão disponíveis imediatamente. Dependendo do número deVMs e computadores que tenham o agente instalado, pode levar uma hora ou mais para que a coleta de informações sobre VMs e configuração do computador, como o status das atualizações de segurança e a configuração do sistema operacional, se torne disponível. Você pode exibir o estado de segurança de seus recursos e quaisquer problemas na seção **Prevenção**. Você também pode exibir uma lista desses problemas no bloco **Recomendações** .

Para obter mais informações sobre como aplicar recomendações, leia [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md).

Em **Monitoramento da integridade do recurso**, você pode monitorar o estado de segurança de seus recursos. No exemplo a seguir, você pode ver que no bloco de cada recurso (Computação e aplicativos, Rede, Segurança de dados e Identidade e acesso) há o número total de problemas que foram identificados.

![Bloco de integridade de segurança de recursos](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>Monitorar computação e aplicativos
Veja [Como proteger seus computadores e aplicativos na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md) para saber mais.

### <a name="monitor-virtual-networks"></a>Monitorar redes virtuais
Quando você clica no bloco **Rede** a folha **Rede** é aberta com mais detalhes, conforme mostrado na seguinte captura de tela:

![Blog da rede](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Recomendações de rede
Assim como as informações de integridade de recursos de máquinas virtuais, aqui você pode ver uma lista resumida dos problemas e uma lista de redes monitoradas na parte inferior.

A seção de divisão de status de rede lista os problemas de segurança potenciais e oferece [recomendações](security-center-network-recommendations.md). Os possíveis problemas podem incluir:

* Firewall da Próxima Geração (NGFW) não instalado
* Grupos de segurança da rede em sub-redes não habilitadas
* Grupos de segurança de rede em máquinas virtuais não habilitadas
* Restringir o acesso externo por meio do ponto de extremidade externo público
* Pontos de extremidade voltados para a Internet íntegra

Quando você clica em uma recomendação, pode ver mais detalhes sobre a recomendação, conforme mostrado no exemplo abaixo:

![Detalhes de uma recomendação em Rede](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Neste exemplo, **Configurar grupos de segurança de rede ausentes para sub-redes** tem uma lista de sub-redes e máquinas virtuais que estão sem proteção do grupo de segurança de rede. Se você clicar na sub-rede à qual deseja aplicar o grupo de segurança de rede, verá **Escolher grupo de segurança de rede**. Aqui, você pode selecionar o grupo de segurança de rede mais apropriado para a sub-rede ou criar um novo grupo de segurança de rede.

#### <a name="internet-facing-endpoints-section"></a>Seção dos pontos de extremidade voltados para a Internet
Na seção **Pontos de extremidade voltados para a Internet**, você pode ver as máquinas virtuais configuradas no momento com um ponto de extremidade voltado para a Internet e seu status atual.

![Máquinas virtuais configuradas para o ponto de extremidade e o status da Internet](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Esta tabela tem o nome de ponto de extremidade que representa a máquina virtual, o endereço IP público da Internet e o status de severidade atual do grupo de segurança de rede e o NGFW. A tabela é classificada por severidade:

* Vermelho (no topo): alta prioridade e deve ser endereçado imediatamente
* Laranja: prioridade média e deve ser endereçado assim que possível
* Verde (último): estado íntegro

#### <a name="networking-topology-section"></a>Seção de Topologia da rede
A seção **Topologia de rede** tem uma exibição hierárquica dos recursos, como mostrado na seguinte captura de tela:

![Exibição hierárquica de recursos na seção de topologia de rede](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Essa tabela é classificada (máquinas virtuais e sub-redes) por severidade:

* Vermelho (no topo): alta prioridade e deve ser endereçado imediatamente
* Laranja: prioridade média e deve ser endereçado assim que possível
* Verde (último): estado íntegro

Nessa exibição da topologia, o primeiro nível tem as [redes virtuai](../virtual-network/virtual-networks-overview.md), [gateways da rede virtual](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) e [rede virtual (clássica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). O segundo nível tem sub-redes, e o terceiro nível tem as máquinas virtuais que pertencem a essas sub-redes. A coluna da direita tem o status atual do grupo de segurança da rede para esses recursos, como mostrado neste exemplo:

![O status do grupo de segurança de rede na seção de Topologia de rede](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

A parte inferior da folha tem as recomendações para essa máquina virtual, que é semelhante ao que foi descrito anteriormente. Você pode clicar em uma recomendação para obter mais informações ou aplicar a configuração ou controle de segurança necessário.

### <a name="monitor-data-security"></a>Monitorar segurança de dados

Quando você clica em **Segurança de dados** na seção **Prevenção**, a opção **Recursos de Dados** abre com recomendações para SQL e Armazenamento. Ela também contém [recomendações](security-center-sql-service-recommendations.md) para o estado de integridade geral do banco de dados. Para saber mais sobre criptografia de armazenamento, leia [Habilitar a criptografia para a conta de armazenamento do Azure na Central de Segurança do Azure](security-center-enable-encryption-for-storage-account.md).

![Recursos de dados](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Em **Recomendações de SQL**, você pode clicar em qualquer recomendação e obter mais detalhes sobre uma ação adicional para resolver um problema. O exemplo a seguir mostra a expansão da recomendação **Auditoria e Detecção de ameaças do banco de dados em bancos de dados SQL**.

![Detalhes sobre uma recomendação de SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

A opção **Habilitar Auditoria e Detecção de ameaças nos bancos de dados SQL** contém as seguintes informações:

* Uma lista de bancos de dados SQL
* O servidor no qual eles estão localizados
* Informações indicando se essa configuração foi herdada do servidor ou se é exclusiva do banco de dados
* O estado atual
* A gravidade do problema

Quando você clicar no banco de dados para lidar com essa recomendação, a opção **Auditoria e Detecção de Ameaças** será aberta, conforme mostrado na tela a seguir.

![Auditoria e Detecção de ameaças](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Para habilitar a auditoria, selecione **ATIVADO** na opção **Auditoria**.

### <a name="monitor-identity--access"></a>Monitorar identidade e acesso

Veja [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md) para saber mais.

## <a name="see-also"></a>Consulte também
Neste artigo, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança na Central de Segurança do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.
