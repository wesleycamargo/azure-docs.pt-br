---
title: Perguntas frequentes sobre a Central de Segurança do Azure | Microsoft Docs
description: Encontre respostas para perguntas frequentes sobre a Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: monhaber
ms.openlocfilehash: 79faab0dcf2dd4c5592fe0543fa63f2538facf36
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664005"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a Central de Segurança do Azure
Estas perguntas frequentes estão relacionadas à Central de Segurança do Azure, um serviço que ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Microsoft Azure.

> [!NOTE]
> A partir do início de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md). As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-security-center"></a>O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como posso obter a Central de Segurança do Azure?
A Central de Segurança do Azure é habilitada com sua assinatura do Microsoft Azure e pode ser acessada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Entre no portal](https://portal.azure.com), selecione **Procurar** e role até a **Central de Segurança**).  

## <a name="billing"></a>Cobrança
### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona o faturamento para a Central de Segurança do Azure?
A Central de Segurança é oferecida em duas camadas:

A **Camada gratuita** fornece visibilidade do estado de segurança de seus recursos do Azure, a política de segurança básica, recomendações de segurança e integração a produtos de segurança e serviços de parceiros.

A **camada Standard** adiciona recursos de detecção avançada de ameaças, inclusive inteligência de ameaças, análise comportamental, detecção de anomalias, incidentes de segurança e relatórios de atribuição de ameaças. Você pode iniciar uma avaliação gratuita da camada Standard. Para atualizar, selecione [Tipo de Preço](https://docs.microsoft.com/azure/security-center/security-center-pricing) na política de segurança. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Como acompanhar quem na minha organização realizadas alterações de camada de preços na Central de segurança do Azure
Como uma assinatura do Azure pode ter vários administradores com permissões para alterar o tipo de preço, um usuário pode querer saber quem executou a alteração na camada de preços. Para usá-lo, é possível usar o Log de atividades do Azure. Consulte mais instruções [aqui](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832)

## <a name="permissions"></a>Permissões
A Central de Segurança do Azure usa o [RBAC (Controle de Acesso Baseado em Função)](../role-based-access-control/role-assignments-portal.md), que fornece [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.

## <a name="data-collection-agents-and-workspaces"></a>Coleta de dados, agentes e workspaces
A Central de segurança coleta dados de suas máquinas virtuais (VMs), conjuntos de dimensionamento de máquinas virtuais (VMSS), os contêineres de IaaS e computadores não Azure (incluindo local) para monitorar as ameaças e vulnerabilidades de segurança. Os dados são coletados usando o Microsoft Monitoring Agent, que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados em seu workspace para serem analisados.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Sou cobrado para logs do Azure Monitor em espaços de trabalho criados pela Central de segurança?
 Não. Espaços de trabalho criados pela Central de segurança, embora sejam configurados para logs do Azure Monitor por cobrança de nó, não incorrerão em encargos de logs do Azure Monitor. A cobrança da Central de Segurança sempre tem base em sua política de segurança da Central de Segurança e nas soluções instaladas em um workspace:

- **Camada gratuita**: a Central de Segurança instala a solução 'SecurityCenterFree' no workspace padrão. Você não será cobrado pela Camada gratuita.
- **Camada Standard**: a Central de Segurança habilita a solução 'Security' no workspace padrão.

Para saber mais sobre preços, confira [preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/). A página de preços aborda as mudanças no armazenamento de dados de segurança e cobrança rateada começando em junho de 2017.

> [!NOTE]
> O tipo de preço do espaços de trabalho criados pela Central de segurança do log analytics não afeta a cobrança da Central de segurança.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>O que qualifica uma VM para o provisionamento automático da instalação do Microsoft Monitoring Agent?
As VMs Windows ou Linux IaaS se qualificam se:

- A extensão do Microsoft Monitoring Agent não está instalada na VM atualmente.
- A VM está em estado de execução.
- O Windows ou Linux [agente de máquina Virtual do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/agent-windows) está instalado.
- A máquina virtual não é usada como um dispositivo, como o firewall de aplicativo Web ou o firewall mais recente.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso excluir os workspaces padrão criados pela Central de Segurança?
**Não recomendamos a exclusão do workspace padrão.** A Central de Segurança usa os workspaces padrão para armazenar dados de segurança de suas VMs.  Se você excluir um workspace, a Central de Segurança não poderá coletar esses dados, e algumas recomendações de segurança e alertas não estarão disponíveis.

Para recuperar, remova o Microsoft Monitoring Agent nas VMs conectadas ao workspace excluído. A Central de Segurança reinstala o agente e cria novos workspaces padrão.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar meu workspace existente do Log Analytics?

Você pode selecionar um workspace existente do Log Analytics para armazenar dados coletados pela Central de Segurança. Para usar o workspace existente do Log Analytics:

- O workspace deve ser associado à sua assinatura do Azure selecionada.
- No mínimo, você deve ter permissões de leitura para acessar o workspace.

Para selecionar um workspace existente do Log Analytics:

1. Em **Política de segurança – Coleta de dados**, selecione **Usar outro workspace**.

   ![Usar outro workspace][5]

2. No menu suspenso, selecione um workspace para armazenar os dados coletados.

   > [!NOTE]
   > No menu suspenso, são mostrados apenas os workspaces aos quais você tem acesso e que estejam em sua assinatura do Azure.
   >
   >

3. Clique em **Salvar**.
4. Ao selecionar **Salvar**, você será questionado se deseja reconfigurar as VMs monitoradas.

   - Selecione **Não** se quiser que as novas configurações de workspace sejam **aplicadas somente às novas VMs**. As novas configurações de workspace se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se quiser que as novas configurações de workspace sejam **aplicadas a todas as VMs**. Além disso, todas as VMs conectadas a um workspace criado da Central de Segurança serão reconectadas ao novo workspace de destino.

   > [!NOTE]
   > Se selecionar Sim, você não deverá excluir os workspaces criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo workspace de destino. Essa operação falhará se um workspace for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

### E se o Microsoft Monitoring Agent já foi instalado como uma extensão na VM?<a name="mmaextensioninstalled"></a>
Quando o agente de monitoramento é instalado como uma extensão, a configuração de extensão permite relatar a apenas um espaço de trabalho. A Central de Segurança não substitui as conexões existentes nos workspaces do usuário. Central de segurança armazena dados de segurança de uma VM em um espaço de trabalho que já esteja conectado, desde que a solução "Segurança" ou "SecurityCenterFree" foi instalada nele. A Central de segurança pode atualizar a versão da extensão para a versão mais recente neste processo.

Para obter mais informações, consulte [provisionamento automático em caso de uma instalação de agente preexistente](security-center-enable-data-collection.md#preexisting).


### E se eu tivesse um Microsoft Monitoring Agent é instalado diretamente na máquina, mas não como uma extensão (agente direto)?<a name="directagentinstalled"></a>
Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de segurança instalará a extensão Microsoft Monitoring Agent e pode atualizar o Microsoft Monitoring agent para a versão mais recente.
O agente instalado continuará reportando para seus espaços de trabalho já configurados e Além disso, irá relatar ao espaço de trabalho configurado na Central de segurança (há suporte para hospedagem múltipla).
Se o espaço de trabalho configurado é um espaço de trabalho do usuário (não a Central de segurança espaço de trabalho padrão), você precisará instalar o "segurança / relatórios de solução de"SecurityCenterFree"nele para a Central de segurança iniciar o processamento de eventos de VMs e computadores para que espaço de trabalho.

Para máquinas existentes em integrado de assinaturas para a Central de segurança antes de 2019-03-17, quando um agente existente será detectado, a extensão Microsoft Monitoring Agent não será instalada e a máquina não será afetada. Para esses computadores, consulte a recomendação "Resolver problemas de integridade do agente em seus computadores de monitoramento" para resolver os problemas de instalação do agente nessas máquinas

 Para saber mais, confira a próxima seção [O que acontece se um agente SCOM ou agente direto do OMS já está instalado em minha VM?](#scomomsinstalled)

### O que acontece se um agente do System Center Operations Manager (SCOM) já está instalado em minha VM?<a name="scomomsinstalled"></a>
A Central de segurança será instalado a Microsoft Monitoring Agent extensão lado a lado para o agente do System Center Operations Manager existente. O agente do SCOM existente continuarão relatar para o System Center Operations Manager server normalmente. Observe que o agente do System Center Operations Manager e o Microsoft Monitoring Agent compartilham bibliotecas comuns do tempo de execução, que serão atualizadas para a versão mais recente durante esse processo. Observação: se o System Center Operations Manager 2012 de versão do agente estiver instalado, ativa o fornecimento automático nas (recursos de gerenciamento podem ser perdidos quando o servidor do System Center Operations Manager também é a versão 2012).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção dessas extensões?
Se você remover a Extensão de Monitoramento da Microsoft, a Central de Segurança não será capaz de coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Dentro de 24 horas, a Central de Segurança determinará que a VM não possui a extensão e reinstalará a extensão.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como fazer para impedir a instalação automática do agente e a criação do workspace?
Você pode desligar o provisionamento automático para suas assinaturas na política de segurança, mas isso não é recomendado. Desativar o provisionamento automático limita as recomendações e os alertas da Central de Segurança. Para desabilitar o provisionamento automático:

1. Se sua assinatura estiver configurada para a camada Standard, abra a política de segurança para essa assinatura e selecione a camada **Gratuita**.

   ![Tipo de preço][1]

2. Em seguida, desligue o provisionamento automático, selecionando **Desativar** na folha **Política de segurança – Coleta de dados**.
   ![Coleta de dados][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Devo recusar a instalação do agente e a criação do workspace automáticas?

> [!NOTE]
> Reveja as seções [Quais são as implicações da recusa?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se você optar por recusar o provisionamento automático.
>
>

Talvez seja ideal recusar o provisionamento automático nos seguintes casos:

- A instalação automática do agente pela Central de Segurança se aplica a toda a assinatura.  Você não pode aplicar a instalação automática em um subconjunto de VMs. Se houver VMs essenciais que não podem ser instaladas com o Microsoft Monitoring Agent, você deverá recusar o provisionamento automático.
- A versão do agente de atualizações de instalação da extensão do agente MMA (Microsoft Monitoring). Isso se aplica a um agente direto e um agente do SCOM (na segunda, o SCOM e o MMA compartilham bibliotecas comuns do tempo de execução - que serão atualizadas no processo). Se a versão do agente SCOM instalada for 2012 e estiver atualizada, os recursos de gerenciamento poderão ser perdidos quando o servidor SCOM também for a versão 2012. Você deve pensar em recusar o provisionamento automático se a versão do agente SCOM instalada é 2012.
- Se você tiver um workspace personalizado fora da assinatura (um workspace centralizado), recuse o provisionamento automático. Você pode instalar a extensão do Microsoft Monitoring Agent manualmente e conectá-la ao workspace sem que a Central de Segurança substitua a conexão.
- Se você quiser evitar a criação de vários workspaces por assinatura e tiver seu próprio workspace personalizado na assinatura, terá duas opções:

   1. Você pode recusar o provisionamento automático. Após a migração, defina as configurações de workspace padrão conforme descrito em [Como fazer para usar meu workspace do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Ou você pode permitir que a migração seja concluída, que o Microsoft Monitoring Agent seja instalado nas VMs e que as VMs sejam conectadas ao workspace criado. Em seguida, selecione o seu próprio workspace personalizado definindo a configuração do workspace padrão com a opção de aceitar para reconfigurar os agentes já instalados. Para saber mais, confira [Como fazer para usar meu workspace do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de se recusar o provisionamento automático?
Quando a migração for concluída, a Central de Segurança não será capaz de coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Se você recusá-lo, deverá instalar o Microsoft Monitoring Agent manualmente. Confira as [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são as etapas recomendadas para a recusa do provisionamento automático?

Você deve instalar manualmente a extensão do Microsoft Monitoring Agent para que a Central de Segurança possa coletar dados de segurança de suas VMs e fornecer recomendações e alertas. Consulte [Instalação do agente para VM do Windows](../virtual-machines/extensions/oms-windows.md) ou [Instalação do agente para VM do Linux](../virtual-machines/extensions/oms-linux.md) para obter orientações sobre a instalação.

Você pode conectar o agente a qualquer workspace personalizado existente ou a um workspace criado pela Central de Segurança. Se um workspace personalizado não tiver as soluções 'Security' ou 'SecurityCenterFree' habilitadas, você precisará aplicar uma solução. Para aplicar, selecione o workspace ou a assinatura personalizada e aplique uma camada de preços na folha **Política de segurança – tipo de preço**.

   ![Tipo de preço][1]

A Central de Segurança habilitará a solução correta no workspace com base no tipo de preço selecionado.

### Como fazer para remover extensões do OMS instaladas pela Central de Segurança?<a name="remove-oms"></a>
Você pode remover manualmente o Microsoft Monitoring Agent. Isso não é recomendado, pois limita as recomendações e os alertas da Central de Segurança.

> [!NOTE]
> Se a coleta de dados estiver habilitada, a Central de Segurança reinstalará o agente após a remoção.  Você precisa desabilitar a coleta de dados antes de remover manualmente o agente. Confira Como fazer para impedir a instalação automática do agente e a criação do workspace? para obter instruções sobre como desabilitar a coleta de dados.
>
>

Para remover manualmente o agente:

1.  No portal, abra o **Log Analytics**.
2.  Na folha Log Analytics, selecione um workspace:
3.  Selecione cada VM que você não quer monitorar e selecione **Desconectar**.

   ![Remova o agente][3]

> [!NOTE]
> Se uma VM do Linux já tiver um agente do OMS, mas não como extensão, a remoção da extensão também removerá o agente, e o cliente precisará reinstalá-lo.
>
>
### <a name="how-do-i-disable-data-collection"></a>Como desabilitar a coleta de dados?
O provisionamento automático é desativado por padrão. Você pode desabilitar o provisionamento automático de recursos a qualquer momento, desativando essa configuração na política de segurança. O provisionamento automático é altamente recomendável a fim de obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades do sistema operacional e proteção do ponto de extremidade.

Para desabilitar a coleta de dados, [Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Selecionar política**. Selecione a assinatura em que você deseja desabilitar o provisionamento automático. Quando você seleciona uma assinatura **Política de segurança - coleta de dados** é exibido. Em **Provisionamento automático**, selecione **Desabilitado**.

### <a name="how-do-i-enable-data-collection"></a>Como habilitar a coleta de dados?
É possível habilitar a coleta de dados para suas assinaturas do Azure na Política de segurança. Para habilitar a coleta de dados. [Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política de segurança**. Selecione a assinatura em que você deseja habilitar o provisionamento automático. Quando você seleciona uma assinatura **Política de segurança - coleta de dados** é exibido. Em **Provisionamento automático**, selecione **Habilitado**.

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a coleta de dados é habilitada?
Quando o provisionamento automático está habilitado, a Central de Segurança provisiona o Microsoft Monitoring Agent em todas as VMs do Azure com suporte, bem como em quaisquer novas VMs que forem criadas. O provisionamento automático é altamente recomendável, mas a instalação manual do agente também está disponível. [Saiba como instalar a extensão do Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente habilita o evento de criação de processo 4688 e o campo *CommandLine* dentro do evento 4688. Novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da Central de Segurança. Para obter informações sobre os detalhes registrados para cada novo processo, consulte [Campos de descrição no 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). O agente também coleta os eventos 4688 criados na VM e os armazena na pesquisa.

O agente também habilita a coleta de dados para [Controles de Aplicativos Adaptáveis](security-center-adaptive-application.md), a Central de Segurança configura uma política do AppLocker local no modo de Auditoria para permitir todos os aplicativos. Isso fará com que o AppLocker gere eventos que são coletados e aproveitados pela Central de Segurança. É importante observar que essa política não será configurada em computadores nos quais já houver uma política do AppLocker configurada. 

Quando a Central de Segurança detecta atividade suspeita na VM, o cliente é notificado por email caso [informações de contato de segurança](security-center-provide-security-contact-details.md) tenham sido fornecidas. Um alerta também fica visível no painel de alertas de segurança da Central de Segurança.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O Monitoring Agent afeta o desempenho dos meus servidores?
O agente consome uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho, bem como o agente e a extensão, consulte o [guia de planejamento e operações](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Onde meus dados são armazenados?
Os dados coletados desse agente são armazenados no análise de Log workspace do Log Analytics existente associado à sua assinatura do Azure ou a novos workspaces. Para obter mais informações, consulte [Segurança de Dados](security-center-data-security.md).

## Os clientes de logs existente do Azure Monitor<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Central de Segurança substitui todas as conexões existentes entre as VMs e os workspaces?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a Central de Segurança não substituirá a conexão do workspace existente. Em vez disso, a Central de Segurança usará o workspace existente. A VM estarão protegida desde que a solução "Segurança" ou "SecurityCenterFree" tiver sido instalada no espaço de trabalho que ele está se comunicando. 

Uma solução da Central de segurança está instalado no espaço de trabalho selecionado na tela de coleta de dados se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes. Quando você adiciona uma solução, ela é implantada automaticamente por padrão em todos os agentes do Windows e Linux conectados ao seu workspace do Log Analytics. [Direcionamento de Solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite a aplicação de um escopo às suas soluções.

Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de Segurança não instalará o Microsoft Monitoring Agent e o monitoramento de segurança será limitado.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>A Central de Segurança instala soluções em meus workspaces existentes do Log Analytics? Quais são as implicações de cobrança?
Quando a Central de Segurança identificar que uma VM já está conectada a um workspace que você criou, a Central de Segurança habilitará as soluções neste workspace de acordo com seu tipo de preço. As soluções são aplicadas apenas às VMs do Azure relevantes, por meio do [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md), portanto, a cobrança permanece igual.

- **Camada gratuita** – A Central de Segurança instala a solução 'SecurityCenterFree' no workspace. Você não será cobrado pela Camada gratuita.
- **Camada Standard**: a Central de Segurança instala a solução 'Security' no workspace.

   ![Soluções no workspace padrão][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Eu já tenho workspaces em meu ambiente, posso usá-los para coletar dados de segurança?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a Central de Segurança usará o workspace existente conectado. Uma solução da Central de Segurança será instalado no workspace, se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes por meio do [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md).

Quando a Central de Segurança instala o Microsoft Monitoring Agent em VMs, ela usa os workspaces padrão criados pela Central de Segurança.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho a solução de segurança em meus workspaces. Quais são as implicações de cobrança?
A solução de Segurança e Auditoria é usada para habilitar recursos da camada Standard da Central de Segurança para VMs do Azure. Se a solução de Segurança e Auditoria já estiver instalada em um workspace, a Central de Segurança usará a solução existente. Não há nenhuma alteração na cobrança.

## <a name="using-azure-security-center"></a>Como usar a Central de Segurança do Azure
### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança do Azure, você pode definir as políticas para as assinaturas do Azure de acordo com os requisitos de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

As políticas de segurança habilitadas na Central de Segurança do Azure determinam as recomendações de segurança e o monitoramento. Para saber mais sobre as políticas de segurança, consulte a seção [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, você deve ser um Administrador de Segurança ou o Proprietário ou Colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>O que é são recomendações de segurança?
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações guiam você pelo processo de configuração do controle necessário. Os exemplos abrangem:

* Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
* [Grupos de segurança de rede](../virtual-network/security-overview.md) e regras para controlar o tráfego para máquinas virtuais
* Provisionamento de um Firewall do Aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
* Como implantar atualizações de sistema ausentes
* Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Somente as recomendações que são habilitadas nas Políticas de segurança são mostradas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como é possível ver o estado atual da segurança dos meus recursos do Azure?
A folha **Visão Geral da Central de Segurança** mostra a postura de segurança geral do ambiente dividida em Computação, Rede, Armazenamento e Dados e, por fim, Aplicativos. Cada tipo de recurso tem um indicador mostrando se possíveis vulnerabilidades de segurança foram identificadas. Clicar em cada bloco exibe uma lista dos problemas de segurança identificados pela Central de Segurança, junto com um inventário dos recursos em sua assinatura.

### <a name="what-triggers-a-security-alert"></a>O que dispara um alerta de segurança?
A Central de Segurança do Azure automaticamente coleta, analisa e funde os dados de log de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

* As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
* Malware avançado detectado com o relatório de erros do Windows
* Ataques por força bruta contra máquinas virtuais
* Alertas de segurança das soluções de segurança de parceiro integradas, como antimalware ou Firewalls de aplicativo Web

### Por que secure pontuações valores alterados? <a name="secure-score-faq"></a>
A partir de fevereiro de 2019, a Central de segurança ajustado a pontuação de algumas recomendações para ajustar melhor sua gravidade. Como resultado, esse ajuste pode haver alterações em geral proteger os valores de pontuação.  Para obter mais informações sobre a pontuação segura, consulte [proteger o cálculo de pontuação](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detectadas e alertadas pelo Microsoft Security Response Center versus pela Central de Segurança do Azure?
O MSRC (Microsoft Security Response Center) executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros. Quando o MSRC fica ciente de que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal ou que o uso do cliente do Azure não está de acordo com os termos de Uso Aceitável, um gerente de incidentes de segurança notifica o cliente. Normalmente, a notificação ocorre com o envio de um email para os contatos de segurança especificados na Central de Segurança do Azure ou para o proprietário da assinatura do Azure, caso um contato de segurança não seja especificado.

A Central de Segurança é um serviço do Azure que monitora o ambiente do cliente do Azure continuamente e aplica a análise para detectar automaticamente uma ampla gama de atividades potencialmente mal-intencionadas. Essas detecções são exibidas como alertas de segurança no painel da Central de Segurança.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos do Azure são monitorados pela Central de Segurança do Azure?
A Central de Segurança do Azure monitora os seguintes recursos do Azure:

* VMs (máquinas virtuais) (incluindo os [Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md))
* (VMSSs) de conjuntos de dimensionamento de máquina virtual
* Redes Virtuais do Azure
* Serviço do SQL Azure
* Conta de Armazenamento do Azure
* Aplicativos Web do Azure (em um [Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md))
* Soluções de parceiros integradas com sua assinatura do Azure, como um firewall de aplicativo Web em VMs e no Ambiente do Serviço de Aplicativo

Além disso, computadores não Azure (incluindo local) também podem ser monitorados pela Central de segurança do Azure (ambos [computadores Windows](./quick-onboard-windows-computer.md) e [computadores Linux](./quick-onboard-linux-computer.md) têm suporte)

## <a name="virtual-machines"></a>Máquinas Virtuais
### <a name="what-types-of-virtual-machines-are-supported"></a>Quais tipos de máquinas virtuais têm suporte?
O monitoramento e as recomendações estão disponíveis para VMs (máquinas virtuais) criadas usando os [modelos de implantação clássico e do Resource Manager](../azure-classic-rm.md).

Consulte [Plataformas com suporte na Central de Segurança do Azure](security-center-os-coverage.md) para obter uma lista de plataformas com suporte.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que a Central de Segurança do Azure não reconhece a solução antimalware em execução em minha VM do Azure?
A Central de Segurança do Azure só tem visibilidade de antimalware instalado por meio de extensões do Azure. Por exemplo, a Central de Segurança não será capaz de detectar antimalware que foi pré-instalado em uma imagem fornecida por você ou se você tiver instalado antimalware em suas máquinas virtuais usando seus próprios processos (como sistemas de gerenciamento de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que recebo a mensagem "Dados de verificação ausentes" para minha VM?
Esta mensagem aparece quando não há nenhum dado de verificação para uma VM. Pode levar algum tempo (menos de uma hora) para que os dados de exame sejam populados depois que a Coleta de Dados é habilitada na Central de Segurança do Azure. Após a população inicial de dados de verificação, você receberá esta mensagem porque não há nenhum dado de verificação ou não há dados de verificação recentes. Os exames não são populados para uma VM em um estado parado. Essa mensagem também pode aparecer se dados de verificação não foram populados recentemente (de acordo com a política de retenção para o agente do Windows, que tem um valor padrão de 30 dias).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Com que frequência a Central de Segurança examina vulnerabilidades do sistema operacional, atualizações do sistema e problemas de endpoint protection?
A latência na Central de Segurança examina vulnerabilidades, atualizações e problemas é:

- Configurações de segurança do sistema operacional – os dados são atualizados dentro de 48 horas
- Atualizações do sistema – os dados são atualizados dentro de 24 horas
- Problemas de Endpoint Protection – os dados são atualizados dentro de 8 horas

A Central de segurança normalmente procura novos dados a cada hora e atualiza as recomendações de forma adequada. 

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que recebo a mensagem "O Agente de VM está ausente?"
O Agente de VM deve ser instalado nas VMs para habilitar a Coleta de Dados. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. Para obter informações sobre como instalar o Agente da VM em outras VMs, consulte a postagem de blog sobre o [Agente de VM e Extensões](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
