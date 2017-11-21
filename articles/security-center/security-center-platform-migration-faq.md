---
title: "Perguntas frequentes sobre a migração da plataforma da Central de Segurança | Microsoft Docs"
description: "Encontre respostas para perguntas frequentes sobre a migração da plataforma da Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: terrylan
ms.openlocfilehash: 69d0c368eb11953d1a6e954990a3be10df7044f0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="security-center-platform-migration-faq"></a>Perguntas frequentes sobre a migração da plataforma da Central de Segurança
No início de junho de 2017, a Central de Segurança do Azure começou a usar o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md). Encontre respostas para perguntas frequentes sobre a migração da plataforma.

## <a name="data-collection-agents-and-workspaces"></a>Coleta de dados, agentes e espaços de trabalho

### <a name="how-is-data-collected"></a>Como os dados são coletados?
A Central de Segurança usa o Microsoft Monitoring Agent para coletar dados de segurança de suas VMs. Os dados de segurança incluem informações sobre:

- configurações de segurança - usadas para identificar vulnerabilidades
- eventos de segurança - usados para detectar ameaças

Os dados coletados pelo agente são armazenados em um espaço de trabalho do Log Analytics conectado à VM ou um novo espaço de trabalho criado pela Central de Segurança. Quando a Central de Segurança cria um novo espaço de trabalho, a localização geográfica da VM é levada em consideração.

> [!NOTE]
> O Microsoft Monitoring Agent é o mesmo agente usado pelo OMS (Operations Management Suite), o serviço Log Analytics e pelo SCOM (System Center Operations Manager).
>
>

Quando o provisionamento automático (antigamente chamado de Coleção de Logs) é habilitado pela primeira vez, ou quando suas assinaturas são migradas, a Central de Segurança verifica se o Microsoft Monitoring Agent já está instalado como uma extensão do Azure em cada uma de suas VMs. Se o Microsoft Monitoring Agent não estiver instalado, a Central de Segurança, por padrão:

- Instalará a extensão Microsoft Monitoring Agent na VM.

   - Se um espaço de trabalho criado pela Central de Segurança já existir na mesma localização geográfica da VM, o agente estará conectado a esse espaço de trabalho.
   - Se não houver um espaço de trabalho, a Central de Segurança criará um novo grupo de recursos e um espaço de trabalho padrão nessa localização geográfica, e conectará o agente ao espaço de trabalho. A convenção de nomenclatura para o grupo de recursos e o espaço de trabalho é:

       Espaço de trabalho: DefaultWorkspace-[ID da assinatura]-[localização geográfica]

       Grupo de recursos: DefaultResouceGroup-[localização geográfica]

- Habilitar uma solução da Central de Segurança no espaço de trabalho de acordo com o tipo de preço associado à VM na Central de Segurança. Para saber mais sobre preços, confira [preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).
- Somente para assinaturas migradas, a Central de Segurança também removerá o agente de monitoramento do Azure anterior.

> [!NOTE]
> Você pode substituir a instalação automática do Microsoft Monitoring Agent e usar seu próprio espaço de trabalho.  Confira [como interromper a instalação de agentes e a criação do espaço de trabalho automáticas](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) e [como usar o espaço de trabalho existente](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

O local do espaço de trabalho tem base no local da VM. Para saber mais, consulte [Segurança dos Dados](security-center-data-security.md). Se uma assinatura contém VMs de várias localizações, a Central de Segurança cria vários espaços de trabalho. Vários espaços de trabalho são criados para manter as regras de privacidade de dados.

> [!NOTE]
> Antes da migração da plataforma, Central de Segurança coletava dados de segurança de suas VMs usando o Azure Monitoring Agent, e os dados eram armazenados em sua conta de armazenamento. Após a migração da plataforma, a Central de Segurança usa o Microsoft Monitoring Agent e o espaço de trabalho para coletar e armazenar os mesmos dados. A conta de armazenamento pode ser removida após a migração.  A Central de Segurança também remove os agentes de monitoramento do Azure após a migração da plataforma.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Sou cobrado pelo Log Analytics ou pelo OMS nos espaços de trabalho criados pela Central de Segurança?
Não. Os espaços de trabalho criados pela Central de Segurança, embora sejam configurados para cobrança de OMS por nó, não incorrerão em encargos do OMS. A cobrança da Central de Segurança sempre tem base em sua política de segurança da Central de Segurança e nas soluções instaladas em um espaço de trabalho:

- **Camada gratuita**: a Central de Segurança instala a solução 'SecurityCenterFree' no espaço de trabalho padrão. Você não será cobrado pela Camada gratuita.
- **Camada Standard**: a Central de Segurança habilita a solução 'Security' no espaço de trabalho padrão.

Para saber mais sobre preços, confira [preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/). A página de preços aborda as mudanças no armazenamento de dados de segurança e cobrança rateada começando em junho de 2017.

> [!NOTE]
> O tipo de preço do OMS para espaços de trabalho criados pela Central de Segurança não afeta a cobrança da Central de Segurança.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>O que qualifica uma VM para o provisionamento automático da instalação do Microsoft Monitoring Agent?
As VMs Windows ou Linux IaaS se qualificam se:

- A extensão do Microsoft Monitoring Agent não está instalada na VM atualmente.
- A VM está em estado de execução.
- O agente de VM Linux ou Windows está instalado.
- A máquina virtual não é usada como um dispositivo, como o firewall de aplicativo Web ou o firewall mais recente.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso excluir os espaços de trabalho padrão criados pela Central de Segurança?
**Não recomendamos a exclusão do espaço de trabalho padrão.** A Central de Segurança usa os espaços de trabalho padrão para armazenar dados de segurança de suas VMs.  Se você excluir um espaço de trabalho, a Central de Segurança não poderá coletar esses dados, e algumas recomendações de segurança e alertas não estarão disponíveis.

Para recuperar, remova o Microsoft Monitoring Agent nas VMs conectadas ao espaço de trabalho excluído. A Central de Segurança reinstala o agente e cria novos espaços de trabalho padrão.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar meu espaço de trabalho existente do Log Analytics?

Você pode selecionar um espaço de trabalho existente do Log Analytics para armazenar dados coletados pela Central de Segurança. Para usar o espaço de trabalho existente do Log Analytics:

- O espaço de trabalho deve ser associado à sua assinatura do Azure selecionada.
- No mínimo, você deve ter permissões de leitura para acessar o espaço de trabalho.

Para selecionar um espaço de trabalho existente do Log Analytics:

1. Em **Política de segurança – Coleta de dados**, selecione **Usar outro espaço de trabalho**.

   ![Usar outro espaço de trabalho][5]

2. No menu suspenso, selecione um espaço de trabalho para armazenar os dados coletados.

   > [!NOTE]
   > No menu suspenso, são mostrados apenas os espaços de trabalho aos quais você tem acesso e que estejam em sua assinatura do Azure.
   >
   >

3. Selecione **Salvar**.
4. Ao selecionar **Salvar**, você será questionado se deseja reconfigurar as VMs monitoradas.

   - Selecione **Não** se quiser que as novas configurações de espaço de trabalho sejam **aplicadas somente às novas VMs**. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se quiser que as novas configurações de espaço de trabalho sejam **aplicadas a todas as VMs**. Além disso, todas as VMs conectadas a um espaço de trabalho criado da Central de Segurança serão reconectadas ao novo espaço de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, você não deverá excluir os espaços de trabalho criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo espaço de trabalho de destino. Essa operação falhará se um espaço de trabalho for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

      ![Reconfigurar VMs monitoradas][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o Microsoft Monitoring Agent já estivesse instalado como uma extensão na VM?
A Central de Segurança não substitui as conexões existentes nos espaços de trabalho do usuário. A Central de Segurança armazena dados de segurança da VM no espaço de trabalho que já está conectado. A Central de Segurança atualiza a versão da extensão para incluir a ID de recurso do Azure da VM para dar suporte ao uso da Central de Segurança.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>E se eu já tivesse um Microsoft Monitoring Agent instalado no computador, mas não como uma extensão?
Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de Segurança não instalará o Microsoft Monitoring Agent e o monitoramento de segurança será limitado.

Para saber mais, confira a próxima seção [O que acontece se um agente SCOM ou agente direto do OMS já está instalado em minha VM?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>O que acontece se um agente SCOM ou agente direto do OMS já está instalado em minha VM?
A Central de Segurança não pode identificar antecipadamente que um agente está instalado.  A Central de Segurança tenta instalar a extensão do Microsoft Monitoring Agent e falha devido ao agente instalado existente.  Essa falha impede a substituição das configurações de conexão do agente para seu espaço de trabalho e evita a criação de hospedagem múltipla.

> [!NOTE]
> A versão do agente é atualizada para a versão mais recente do agente do OMS.  Isso também se aplica a usuários do SCOM.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção dessas extensões?
Se você remover a Extensão de Monitoramento da Microsoft, a Central de Segurança não será capaz de coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Dentro de 24 horas, a Central de Segurança determinará que a VM não possui a extensão e reinstalará a extensão.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como fazer para impedir a instalação automática do agente e a criação do espaço de trabalho?
Você pode desligar o provisionamento automático para suas assinaturas na política de segurança, mas isso não é recomendado. Desativar o provisionamento automático limita as recomendações e os alertas da Central de Segurança. O provisionamento automático é necessário para assinaturas no tipo de preço Standard. Para desabilitar o provisionamento automático:

1. Se sua assinatura estiver configurada para a camada Standard, abra a política de segurança para essa assinatura e selecione a camada **Gratuita**.

   ![Tipo de preço ][1]

2. Em seguida, desligue o provisionamento automático, selecionando **Desativar** na folha **Política de segurança – Coleta de dados**.
   ![Coleta de dados][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Devo recusar a instalação do agente e a criação do espaço de trabalho automáticas?

> [!NOTE]
> Reveja as seções [Quais são as implicações da recusa?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se você optar por recusar o provisionamento automático.
>
>

Talvez seja ideal recusar o provisionamento automático nos seguintes casos:

- A instalação automática do agente pela Central de Segurança se aplica a toda a assinatura.  Você não pode aplicar a instalação automática em um subconjunto de VMs. Se houver VMs essenciais que não podem ser instaladas com o Microsoft Monitoring Agent, você deverá recusar o provisionamento automático.
- A instalação da extensão do Microsoft Monitoring Agent atualiza a versão do agente. Isso se aplica a um agente direto e a um agente SCOM. Se a versão do agente SCOM instalada for 2012 e estiver atualizada, os recursos de gerenciamento poderão ser perdidos quando o servidor SCOM também for a versão 2012. Você deve pensar em recusar o provisionamento automático se a versão do agente SCOM instalada é 2012.
- Se você tiver um espaço de trabalho personalizado fora da assinatura (um espaço de trabalho centralizado), recuse o provisionamento automático. Você pode instalar a extensão do Microsoft Monitoring Agent manualmente e conectá-la ao espaço de trabalho sem que a Central de Segurança substitua a conexão.
- Se você quiser evitar a criação de vários espaços de trabalho por assinatura e tiver seu próprio espaço de trabalho personalizado na assinatura, terá duas opções:

   1. Você pode recusar o provisionamento automático. Após a migração, defina as configurações de espaço de trabalho padrão conforme descrito em [Como fazer para usar meu espaço de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Ou você pode permitir que a migração seja concluída, que o Microsoft Monitoring Agent seja instalado nas VMs e que as VMs sejam conectadas ao espaço de trabalho criado. Em seguida, selecione o seu próprio espaço de trabalho personalizado definindo a configuração do espaço de trabalho padrão com a opção de aceitar para reconfigurar os agentes já instalados. Para saber mais, confira [Como fazer para usar meu espaço de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de se recusar o provisionamento automático?
Quando a migração for concluída, a Central de Segurança não será capaz de coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Se você recusá-lo, deverá instalar o Microsoft Monitoring Agent manualmente. Confira as [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são as etapas recomendadas para a recusa do provisionamento automático?
Você deve instalar o Microsoft Monitoring Agent manualmente para que a Central de Segurança possa coletar dados de segurança de suas VMs e fornecer recomendações e alertas. Confira [Conectar computadores Windows ao serviço Log Analytics no Azure](../log-analytics/log-analytics-windows-agents.md) para obter diretrizes sobre a instalação.

Você pode conectar o agente a qualquer espaço de trabalho personalizado existente ou a um espaço de trabalho criado pela Central de Segurança. Se um espaço de trabalho personalizado não tiver as soluções 'Security' ou 'SecurityCenterFree' habilitadas, você precisará aplicar uma solução. Para aplicar, selecione o espaço de trabalho ou a assinatura personalizada e aplique uma camada de preços na folha **Política de segurança – tipo de preço**.

   ![Tipo de preço ][1]

A Central de Segurança habilitará a solução correta no espaço de trabalho com base no tipo de preço selecionado.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Como fazer para remover as extensões do OMS instaladas pela Central de Segurança?
Você pode remover manualmente o Microsoft Monitoring Agent. Isso não é recomendado, pois limita as recomendações e os alertas da Central de Segurança.

> [!NOTE]
> Se a coleta de dados estiver habilitada, a Central de Segurança reinstalará o agente após a remoção.  Você precisa desabilitar a coleta de dados antes de remover manualmente o agente. Confira [Como fazer para impedir a instalação automática do agente e a criação do espaço de trabalho?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) para obter instruções sobre como desabilitar a coleta de dados.
>
>

Para remover manualmente o agente:

1.  No portal, abra o **Log Analytics**.
2.  Na folha Log Analytics, selecione um espaço de trabalho:
3.  Selecione cada VM que você não quer monitorar e selecione **Desconectar**.

   ![Remova o agente][3]

> [!NOTE]
> Se uma VM do Linux já tiver um agente do OMS, mas não como extensão, a remoção da extensão também removerá o agente, e o cliente precisará reinstalá-lo.
>
>

## <a name="existing-oms-customers"></a>Clientes existentes do OMS

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Central de Segurança substitui todas as conexões existentes entre as VMs e os espaços de trabalho?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a Central de Segurança não substituirá a conexão do espaço de trabalho existente. Em vez disso, a Central de Segurança usará o espaço de trabalho existente.

Uma solução da Central de Segurança será instalado no espaço de trabalho, se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes. Quando você adiciona uma solução, ela é implantada automaticamente por padrão em todos os agentes do Windows e Linux conectados ao seu espaço de trabalho do Log Analytics. [Direcionamento de Solução](../operations-management-suite/operations-management-suite-solution-targeting.md), que é um recurso do OMS que permite a aplicação de um escopo às suas soluções.

Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de Segurança não instalará o Microsoft Monitoring Agent e o monitoramento de segurança será limitado.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>O que devo fazer se suspeitar de que a migração da plataforma de dados interrompeu a conexão entre uma das minhas VMs e meu espaço de trabalho?
Isso não deve ocorrer. Se ocorrer, [Crie uma solicitação de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md) e inclua os seguintes detalhes:

- A ID de recurso do Azure da VM afetada
- A ID de recurso do Azure do espaço de trabalho configurado na extensão antes da conexão ser interrompida
- O agente e a versão instalados anteriormente

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>A Central de Segurança instala soluções em meus espaços de trabalho existentes do OMS? Quais são as implicações de cobrança?
Quando a Central de Segurança identificar que uma VM já está conectada a um espaço de trabalho que você criou, a Central de Segurança habilitará as soluções neste espaço de trabalho de acordo com seu tipo de preço. As soluções são aplicadas apenas às VMs do Azure relevantes, por meio do [direcionamento de solução](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), portanto, a cobrança permanece igual.

- **Camada gratuita** – A Central de Segurança instala a solução 'SecurityCenterFree' no espaço de trabalho. Você não será cobrado pela Camada gratuita.
- **Camada Standard**: a Central de Segurança instala a solução 'Security' no espaço de trabalho.

   ![Soluções no espaço de trabalho padrão][4]

> [!NOTE]
> A solução 'Security' no Log Analytics é a solução de Segurança e Auditoria no OMS.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Eu já tenho espaços de trabalho em meu ambiente, posso usá-los para coletar dados de segurança?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a Central de Segurança usará o espaço de trabalho existente conectado. Uma solução da Central de Segurança será instalado no espaço de trabalho, se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes por meio do [direcionamento de solução](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Quando a Central de Segurança instala o Microsoft Monitoring Agent em VMs, ela usa os espaços de trabalho padrão criados pela Central de Segurança. Em breve, os clientes poderão configurar quais espaços de trabalho são usados.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho a solução de segurança em meus espaços de trabalho. Quais são as implicações de cobrança?
A solução de Segurança e Auditoria é usada para habilitar recursos da camada Standard da Central de Segurança para VMs do Azure. Se a solução de Segurança e Auditoria já estiver instalada em um espaço de trabalho, a Central de Segurança usará a solução existente. Não há nenhuma alteração na cobrança.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a migração de plataforma da Central de Segurança, confira

- [Migração de plataforma da Central de Segurança do Azure](security-center-platform-migration.md)
- [Guia de solução de problemas da Central de Segurança do Azure](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png
