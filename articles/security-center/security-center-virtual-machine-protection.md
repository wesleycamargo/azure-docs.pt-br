---
title: Protegendo suas máquinas e aplicativos na Central de Segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações da Central de Segurança que ajudam a proteger suas máquinas virtuais, seus computadores e os ambientes de aplicativos Web e do Serviço de Aplicativo.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7cf5f86d9a2d121ff54c40e27c6bc50847a4dfdf
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43132560"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Protegendo suas máquinas e aplicativos na Central de Segurança do Azure
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que orientam você durante o processo de configuração dos controles necessários. As recomendações aplicam-se aos tipos de recursos do Azure: VMs (máquinas virtuais) e computadores, aplicativos, rede, SQL e a identidade e acesso.

Este artigo aborda as recomendações que se aplicam a computadores e aplicativos.

## <a name="monitoring-security-health"></a>Monitoramento de integridade da segurança
Você pode monitorar o estado de segurança de seus recursos na **Central de segurança – visão geral** painel. A seção **Recursos** fornece o número de problemas identificados e o estado de segurança de cada tipo de recurso.

Você pode visualizar uma lista de todos os problemas selecionando **Recomendações**. Para obter mais informações sobre como aplicar recomendações, confira [Implementando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

Para obter uma lista completa de recomendações dos Serviços de Computação e de Aplicativos, confira [Recomendações](security-center-virtual-machine-recommendations.md).

Para continuar, selecione **Computação e aplicativos** em **Recursos** ou no menu principal da Central de Segurança.
![Painel da Central de Segurança][1]

## <a name="monitor-compute-and-app-services"></a>Monitorar Serviços de Computação e de Aplicativos
Em **Computação**, há quatro guias:

- **Visão geral**: monitoramento e recomendações identificadas pela Central de Segurança.
- **VMs e computadores**: lista de suas máquinas virtuais, computadores e estado de segurança atual de cada um.
- **Serviços de nuvem**: lista de suas funções web e de trabalho monitoradas pela Central de Segurança.
- **Serviços de Aplicativos (versão prévia)**: lista de seus ambientes do Serviço de Aplicativo e o estado de segurança atual de cada um.
Para continuar, selecione **Computação e aplicativos** em **Recursos** ou no menu principal da Central de Segurança.

![Computação][2]

Há várias seções em cada guia e, em cada seção, você pode selecionar uma opção individual para ver mais detalhes sobre as etapas recomendadas e resolver esse problema específico.

### <a name="monitoring-recommendations"></a>Recomendações de monitoramento
Esta seção mostra o número total de VMs e computadores que foram inicializados para provisionamento automático e seus status atuais. Neste exemplo, há uma recomendação, **Monitoramento de problemas de integridade do agente**. Selecione essa recomendação.

![Monitorar problemas de integridade do agente][3]

**Monitoramento de problemas de integridade do agente** é aberto. VMs e computadores em que a Central de Segurança não pode monitorar com êxito são listados. Selecione uma VM ou um computador para obter informações detalhadas. **ESTADO DE MONITORAMENTO** fornece um motivo porque a Central de Segurança não é capaz de monitorar. Veja o [guia de solução de problemas da Central de Segurança](security-center-troubleshooting-guide.md) para obter uma lista de valores, descrições e etapas de resolução do **ESTADO DE MONITORAMENTO**.

### VMs e computadores não monitorados <a name="unmonitored-vms-and-computers"></a>
As VM ou os computadores não são monitorados pela Central de Segurança quando não estão executando a extensão do Microsoft Monitoring Agent. Um computador pode ter um agente local já instalado, por exemplo, o agente direto do OMS ou o agente do SCOM. Os computadores com esses agentes são identificados como não monitorados porque não há suporte total para esses agentes na Central de Segurança. Para aproveitar ao máximo todos os recursos da Central de Segurança, é necessária a extensão do Microsoft Monitoring Agent.

Você pode instalar a extensão na VM ou no computador não monitorado, além do agente local já instalado. Configure os dois agentes iguais, conectando-os ao mesmo espaço de trabalho. Isso permite que a Central de Segurança interaja com a extensão do Microsoft Monitoring Agent e colete dados. Consulte [Habilitar a extensão da VM](../log-analytics/log-analytics-quick-collect-azurevm.md) para obter instruções sobre como instalar a extensão do Microsoft Monitoring Agent.

Consulte [Monitorando problemas de integridade do agente](security-center-troubleshooting-guide.md#mon-agent) para saber mais sobre o motivo pelo qual a Central de Segurança não consegue monitorar com êxito as VMs e os computadores inicializados para o provisionamento automático.

### <a name="recommendations"></a>Recomendações
Esta seção tem um conjunto de recomendações para cada VM e computador, funções web e de trabalho, Aplicativos Web do Serviço de Aplicativo do Azure e o ambiente do Serviço de Aplicativo do Azure que a Central de Segurança monitora. A primeira coluna lista a recomendação. A segunda coluna mostra o número total de recursos que são afetados por essa recomendação. A terceira coluna mostra a severidade do problema, conforme ilustrado na captura de tela abaixo:

![Recomendações][4]

Cada recomendação tem um conjunto de ações que você poderá executar depois de selecioná-la. Por exemplo, se você selecionar **Atualizações do sistema ausentes**, o número de VMs e computadores com patches ausentes e a gravidade da atualização ausente serão exibidos, conforme é mostrado na seguinte captura de tela:

![Aplicar atualizações do sistema][5]

**Aplicar atualizações do sistema** tem um resumo de atualizações críticas no formato de gráfico, um para Windows e outro para Linux. A segunda parte tem uma tabela com as seguintes informações:

- **NOME**: nome da atualização ausente.
- **NÃO. DE VMs E COMPUTADORES**: número total de máquinas virtuais e computadores que não têm essa atualização.
- **GRAVIDADE DA ATUALIZAÇÃO**: descreve a gravidade dessa recomendação específica:

    - **Crítico**: existe uma vulnerabilidade em um recurso significativo (aplicativo, máquina virtual ou grupo de segurança de rede) e ela requer atenção.
    - **Importante**: são necessárias etapas adicionais ou não críticas para concluir um processo ou eliminar uma vulnerabilidade.
    - **Moderado**: uma vulnerabilidade deve ser resolvida, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje exibi-las.)


- **ESTADO**: o estado atual da recomendação:

    - **Aberta**: a recomendação ainda não foi resolvida.
    - **Em Andamento**: a recomendação está sendo aplicada atualmente aos recursos, e não é necessário que você realize nenhuma ação.
    - **Resolvido**: a recomendação já foi concluída. (Quando o problema foi resolvido, a entrada será esmaecida).

Para exibir os detalhes de recomendação, clique no nome da atualização ausente na lista.

![Detalhes da recomendação][6]

> [!NOTE]
> As recomendações de segurança aqui são as mesmas que as do bloco **Recomendações**. Confira [Implementando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) para obter mais informações de como resolver as recomendações.
>
>

### <a name="vms-and-computers"></a>VMs e computadores
A seção de VMs e computadores fornece uma visão geral de todas as recomendações para VMs e computadores. Cada coluna representa um conjunto de recomendações, conforme mostrado na seguinte captura de tela:

![Recomendações para VMs e computadores][7]

Há quatro tipos de ícones representados nesta lista:

![Computador não Azure][8] Computador não Azure.

![VM do Azure Resource Manager][9] VM do Azure Resource Manager.

![VM Clássica do Azure][10] VM Clássica do Azure.

![VMs identificadas no espaço de trabalho][11] VMs identificadas somente pelo espaço de trabalho que faz parte da assinatura exibida. Isso inclui VMs de outras assinaturas que se reportam ao espaço de trabalho nesta assinatura e VMs que foram instaladas com o agente direto SCOM e não têm nenhuma ID de recurso.

O ícone que aparece em cada recomendação ajuda a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Você também pode usar a opção Filtrar para selecionar quais opções serão exibidas nesta tela.

![Filter][12]

No exemplo anterior, uma VM tem uma recomendação crítica relacionada à proteção de ponto de extremidade. Selecione a VM para obter mais informações sobre ela:

![Recomendação crítica][13]

Veja aqui os detalhes de segurança da VM ou do computador. Na parte inferior, você pode ver a ação recomendada e a gravidade de cada problema.

### <a name="cloud-services"></a>Serviços de Nuvem
Para os serviços de nuvem, uma recomendação é criada quando a versão do sistema operacional está desatualizada, conforme mostra a captura de tela a seguir:

![Serviços de Nuvem][14]

Em um cenário no qual há uma recomendação (que não é o caso do exemplo anterior), você precisa seguir as etapas na recomendação para atualizar a versão do sistema operacional. Quando uma atualização estiver disponível, você terá um alerta (vermelho ou laranja - depende da gravidade do problema). Ao selecionar esse alerta nas linhas WebRole1 (executa o Windows Server com o aplicativo Web implantado automaticamente no IIS) ou WorkerRole1 (executa o Windows Server com o aplicativo Web implantado automaticamente no IIS), você verá mais detalhes sobre essa recomendação, conforme é mostrado na seguinte captura de tela:

![WorkerRole1][15]

Para ver uma explicação mais detalhada sobre essa recomendação, clique em **Atualizar versão do sistema operacional** na coluna **DESCRIÇÃO**.

![Atualizar a versão do sistema operacional][16]

### <a name="app-services-preview"></a>Serviços de Aplicativos (versão prévia)

> [!NOTE]
> O monitoramento do Serviço de Aplicativo está na versão prévia e está disponível apenas no nível Standard da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
>
>

Em **Serviços de Aplicativos**, você encontra uma lista de seus ambientes do Serviço de Aplicativo e o resumo de integridade com base na avaliação executada pela Central de Segurança.

![Serviços de aplicativos][17]

Há três tipos de ícones representados nesta lista:

![Ambiente de Serviços de Aplicativos][18] Ambiente de Serviços de Aplicativos.

![Aplicativo Web][19] Aplicativo Web.

![Aplicativo de função][24] Aplicativo de função.

1. Selecione um aplicativo Web. Abre uma exibição de resumo com três guias:

  - **Recomendações**: com base nas avaliações realizadas pela Central de segurança que falhou.
  - **Avaliações aprovadas**: lista de avaliações realizadas pela Central de Segurança que foram aprovadas.
  - **Avaliações não disponíveis**: lista de avaliações que falharam devido a um erro ou a recomendação não é relevante para o Serviço de Aplicativo específico

  Em **Recomendações** há uma lista das recomendações para o aplicativo Web selecionado e a gravidade de cada recomendação.

  ![Exibição do resumo][20]

2. Selecione uma recomendação para obter uma descrição da recomendação, uma lista de recursos não íntegros, de recursos íntegros e de recursos não verificados.

  ![Descrição da recomendação][21]

  Em **Avaliações aprovadas**, há uma lista de avaliações aprovadas.  Gravidade dessas avaliações sempre é verde.

  ![Avaliações aprovadas][22]

3. Selecione uma avaliação passada na lista para obter uma descrição da avaliação, uma lista de recursos íntegros e não íntegros e uma lista de recursos não examinados. Há uma guia para os recursos não íntegros, mas essa lista está sempre vazia, pois a avaliação foi aprovada.

    ![Recursos íntegros][23]



## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:


* [Noções básicas sobre as recomendações da Central de Segurança do Azure para máquinas virtuais](security-center-virtual-machine-recommendations.md)
* [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
