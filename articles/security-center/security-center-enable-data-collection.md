---
title: Coleta de dados na Central de Segurança do Azure | Microsoft Docs
description: " Saiba como habilitar a coleta de dados na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 9043c6583a15d3be9d0d468e83a4bf79b3121794
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304111"
---
# <a name="data-collection-in-azure-security-center"></a>Coleta de dados na Central de Segurança do Azure
A Central de Segurança coleta dados de suas VMs (máquinas virtuais) do Azure e dos computadores não Azure a fim de monitorar as ameaças e vulnerabilidades de segurança. Os dados são coletados usando o Microsoft Monitoring Agent, que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados em seu espaço de trabalho para serem analisados. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP e usuário conectado. O Microsoft Monitoring Agent também copia os arquivos de despejo de memória para seu espaço de trabalho.

A coleta de dados é necessária para fornecer visibilidade sobre atualizações ausentes, definições de segurança do sistema operacional configuradas incorretamente, habilitação da proteção de ponto de extremidade e detecções de ameaças e da integridade. 

Este artigo fornece diretrizes sobre como instalar um Microsoft Monitoring Agent e definir um espaço de trabalho do Log Analytics no qual armazenar os dados coletados. As duas operações são necessárias para habilitar a coleta de dados. 

> [!NOTE]
> - A coleta de dados é necessária apenas para recursos de computação (VMs e computadores não Azure). Você pode aproveitar a Central de Segurança do Azure mesmo se não provisionar agentes, no entanto, a segurança será limitada e as funcionalidades listadas acima não terão suporte.  
> - Para ver a lista das plataformas compatíveis, consulte [Plataformas compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md).
> - Atualmente, a coleta de dados não é compatível com o conjunto de dimensionamento de máquinas virtuais.


## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Habilitar o provisionamento automático do Microsoft Monitoring Agent     
Para coletar os dados dos computadores, instale o Microsoft Monitoring Agent.  A instalação do agente pode ser realizada automaticamente (recomendado) ou você pode optar por instalar o agente manualmente.  

>[!NOTE]
> O provisionamento automático é desativado por padrão. Para configurar a Central de Segurança para instalar o provisionamento automático por padrão, defina-o como **Ativado**.
>

Quando o provisionamento automático está Ativado, a Central de Segurança provisiona o Microsoft Monitoring Agent em todas as VMs do Azure com suporte, bem como nas novas VMs que forem criadas. O provisionamento automático é altamente recomendável, mas a instalação manual do agente também está disponível. [Saiba como instalar a extensão do Microsoft Monitoring Agent](#manualagent).



Para habilitar o provisionamento automático do Microsoft Monitoring Agent:
1. No menu principal da Central de segurança, selecione **Política de segurança**.
2. Selecione a assinatura.

  ![Escolha a assinatura][7]

3. Em **Política de segurança**, selecione **Coleta de Dados**.
4. Em **Provisionamento Automático**, selecione **Ativar** para habilitar o provisionamento automático.
5. Clique em **Salvar**.

  ![Habilitar o provisionamento automático][1]

>[!NOTE]
> - Para obter instruções sobre como provisionar uma instalação já existente, veja [Provisionamento automático em caso de uma instalação de agente pré-existente](#preexisting).
> - Para ver as instruções de provisionamento manual, consulte [Instalar a extensão do Microsoft Monitoring Agent manualmente](#manualagent).
> - Para ver as instruções sobre como desativar o provisionamento automático, consulte [Desativar o provisionamento automático](#offprovisioning).
>


## <a name="workspace-configuration"></a>Configuração do espaço de trabalho
Os dados coletados pela Central de Segurança são armazenados no espaço do Log Analytics.  Você pode optar por ter os dados coletados de VMs do Azure armazenados em espaços de trabalho criados pela Central de Segurança ou em um espaço de trabalho existente que você criou. 

A configuração do espaço de trabalho é definida por assinatura, e várias assinaturas podem usar o mesmo espaço de trabalho.

### <a name="using-a-workspace-created-by-security-center"></a>Usar espaços de trabalho criados pela Central de Segurança

A Central de Segurança pode criar automaticamente um espaço de trabalho padrão no qual armazenar os dados. 

Para selecionar um espaço de trabalho criados pela Central de Segurança:

1.  Em **Configuração do espaço de trabalho padrão**, selecione Usar espaços de trabalho criados pela Central de Segurança.
   ![Selecione o tipo de preço][10] 

2. Clique em **Salvar**.<br>
    A Central de Segurança criará um novo grupo de recursos e um espaço de trabalho padrão nessa geolocalização e conectará o agente a esse espaço de trabalho. A convenção de nomenclatura para o grupo de recursos e o espaço de trabalho é:<br>
**Workspace: DefaultWorkspace-[subscription-ID]-[geo]<br> Resource Group: DefaultResouceGroup-[geo]**

   Se uma assinatura contém VMs de várias localizações, a Central de Segurança cria vários espaços de trabalho. Vários espaços de trabalho são criados para manter as regras de privacidade de dados.
-   A Central de segurança habilitará automaticamente uma solução da Central de Segurança no espaço de trabalho por tipo de preço definido para a assinatura. 

> [!NOTE]
> Espaços de trabalho criados pela Central de Segurança não incorrem em encargos do Log Analytics. O tipo de preço do Log Analytics para espaços de trabalho criados pela Central de Segurança não afeta a cobrança da Central de Segurança. A cobrança da Central de Segurança sempre se baseia na sua política de segurança da Central de Segurança e nas soluções instaladas em um espaço de trabalho. Para a Camada gratuita, a Central de Segurança instala a solução *SecurityCenterFree* no espaço de trabalho padrão. Para a Camada Standard, a Central de Segurança habilita a solução *Security* no espaço de trabalho padrão.

Para saber mais sobre preços, confira [preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre as contas existentes do Log Analytics, consulte [Clientes existentes do Log Analytics](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Usar um espaço de trabalho existente

Se você já tiver um espaço de trabalho existente do Log Analytics, convém usar esse mesmo espaço de trabalho.

Para usar o espaço de trabalho existente do Log Analytics, você precisará ter permissões de leitura e gravação no espaço de trabalho.

> [!NOTE]
> Soluções habilitadas no espaço de trabalho existente serão aplicadas às VMs do Azure que estão conectados a ele. Para soluções pagas, isso pode resultar em cobranças adicionais. Por questões de considerações de privacidade de dados, verifique se seu espaço de trabalho selecionado está na região geográfica correta.
>

Para selecionar um espaço de trabalho existente do Log Analytics:

1. Em **Configuração do espaço de trabalho padrão**, selecione **Usar outro espaço de trabalho**.

   ![Selecionar um espaço de trabalho existente][2]

2. No menu suspenso, selecione um espaço de trabalho para armazenar os dados coletados.

  > [!NOTE]
  > No menu suspenso, todos os espaços de trabalho em todas as suas inscrições estão disponíveis. Consulte a [seleção de espaço de trabalho de assinatura cruzada](security-center-enable-data-collection.md#cross-subscription-workspace-selection) para obter mais informações. Você precisa de permissão para acessar o espaço de trabalho.
  >
  >

3. Clique em **Salvar**.
4. Após selecionar **Salvar**, será perguntado se você deseja reconfigurar as VMs monitoradas que estavam conectadas ao espaço de trabalho padrão anteriormente.

   - Selecione **Não** se quiser que as novas configurações de espaço de trabalho sejam aplicadas somente às novas VMs. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se quiser que as novas configurações de espaço de trabalho sejam aplicadas a todas as VMs. Além disso, todas as VMs conectadas a um espaço de trabalho criado da Central de Segurança serão reconectadas ao novo espaço de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, você não deverá excluir os espaços de trabalho criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo espaço de trabalho de destino. Essa operação falhará se um espaço de trabalho for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

     ![Selecionar um espaço de trabalho existente][3]

5. Selecione o tipo de preço para o espaço de trabalho desejado que você pretende definir no Microsoft Monitoring Agent. <br>Para usar um espaço de trabalho existente, defina o tipo de preço do espaço de trabalho. Isso instalará uma solução da Central de Segurança no espaço de trabalho, se ainda não existir.

    a.  No menu principal da Central de Segurança, selecione **Política de segurança**.
     
    b.  Selecione o espaço de trabalho desejado ao qual você pretende conectar o agente.
        ![Selecione o espaço de trabalho][8] c. Defina o tipo de preço.
        ![Selecione o tipo de preço][9] 
   
   >[!NOTE]
   >Se o espaço de trabalho já tiver uma solução de **Segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Seleção de espaço de trabalho entre assinaturas
Ao selecionar um espaço de trabalho para armazenar os dados, todos os espaços de trabalho em todas as assinaturas estarão disponíveis. A seleção do espaço de trabalho entre assinaturas permite coletar dados de máquinas virtuais em execução em assinaturas diferentes e armazená-los no espaço de trabalho de sua preferência. Essa seleção será útil se você utilizar um espaço de trabalho centralizado na sua organização e quiser usá-lo para coleta de dados de segurança. Para obter mais informações sobre como gerenciar espaços de trabalho, consulte [Gerenciar o acesso ao espaço de trabalho](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Camada de coleta de dados
A Central de Segurança pode reduzir o volume de eventos, mantendo, ao mesmo tempo, eventos suficientes para investigação, auditoria e detecção de ameaças. Você pode escolher a política de filtragem correta para as suas assinaturas e espaços de trabalho dentre quatro conjuntos de eventos a serem coletados pelo agente.

- **Todos os eventos** – para clientes que desejam se certificar de que todos os eventos sejam coletados. Esse é o padrão.
- **Comum** – este é um conjunto de eventos que satisfaz a maioria dos clientes, garantindo uma trilha de auditoria completa.
- **Mínimo** – um conjunto menor de eventos, para clientes que desejam minimizar o volume de eventos.
- **Nenhum** – desabilitar a coleta de eventos de segurança da segurança e dos logs do AppLocker. Para clientes que escolherem esta opção, os painéis de segurança apresentarão somente os logs do Firewall do Windows e as avaliações proativas, como antimalware, linha de base e atualização.

> [!NOTE]
> Esses conjuntos de eventos de segurança estão disponíveis apenas na Camada Standard da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
Esses conjuntos foram projetados para lidar com cenários típicos. Avalie qual atende às suas necessidades antes de implementá-los.
>
>

Para determinar os eventos que farão parte dos conjuntos de eventos **Comum** e **Mínimo**, trabalhamos com clientes e padrões do setor para saber mais sobre a frequência não filtrada de cada evento e seu uso. Neste processo, usamos as seguintes diretrizes:

- **Mínimo** – Certificar-se de que esse conjunto aborde apenas os eventos que possam indicar uma violação com êxito e eventos importantes que tenham um volume muito baixo. Por exemplo, este conjunto conterá logon de usuário bem-sucedidos e malsucedidos (IDs de evento 4624 e 4625), mas não conterá o logout que seja importante para a auditoria, mas não seja significativo para a detecção e que tenha um volume relativamente alto. A maior parte do volume de dados desse conjunto é dos eventos de logon e do evento de criação de processo (ID de evento 4688).
- **Comum** – fornecer uma trilha de auditoria de usuário completa neste conjunto. Por exemplo, este conjunto contém tanto os logons quanto os logoffs de usuário (ID de evento 4634). Incluímos ações de auditoria, como alterações de grupo de segurança, operações Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações do setor.

Eventos que têm um volume muito baixo foram incluídos no conjunto Comum, pois a principal motivação para escolhê-los dentre todos os eventos era reduzir o volume e não filtrar eventos específicos.

Aqui está um detalhamento completo das IDs de eventos de Segurança e do AppLocker para cada conjunto:

| Camada de dados | Indicadores de eventos coletados |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comum | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se você está usando o GPO (Objeto de Política de Grupo), é recomendável habilitar o Evento de Criação de Processo 4688 das políticas de auditoria, bem como o campo *CommandLine* dentro do evento 4688. Para obter mais informações sobre o Processo de Criação do Evento 4688, consulte as [Perguntas Frequentes](security-center-faq.md#what-happens-when-data-collection-is-enabled) da Central de Segurança. Para obter mais informações sobre essas políticas de auditoria, consulte [Recomendações de Política de Auditoria](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Para habilitar a coleta de dados para [Controles de Aplicativo Adaptáveis](security-center-adaptive-application.md), a Central de Segurança configura uma política do AppLocker local no modo de Auditoria para permitir todos os aplicativos. Isso fará com que o AppLocker gere eventos que são coletados e aproveitados pela Central de Segurança. É importante observar que essa política não será configurada em computadores nos quais já houver uma política do AppLocker configurada. 
> - Para coletar a Plataforma para Filtros do Windows [ID de evento 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), será preciso habilitar a [Conexão da Plataforma para Filtros de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

Para escolher a política de filtragem:
1. Na folha **Política de Segurança – Coleta de Dados**, selecione a política de filtragem em **Eventos de Segurança**.
2. Clique em **Salvar**.

   ![Escolher a política de filtragem][5]

### Provisionamento automático em caso de uma instalação de agente pré-existente <a name="preexisting"></a> 

Os casos de uso a seguir especificam como o provisionamento automático funciona quando já existe um agente ou extensão instalados. 

- O Microsoft Monitoring Agent está instalado no computador, mas não como uma extensão<br>
Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de Segurança não instalará o Microsoft Monitoring Agent. É possível ativar o provisionamento automático e selecionar o espaço de trabalho do usuário relevante na configuração de provisionamento de automático da Central de Segurança. Se você escolher o mesmo espaço de trabalho ao qual a VM já está conectada, o agente existente será encapsulado com uma extensão do Microsoft Monitoring Agent. 

> [!NOTE]
> Se o agente do SCOM versão 2012 estiver instalado, **não** ative o provisionamento automático. 

Para saber mais, consulte [O que acontece se um agente direto do SCOM ou OMS já estiver instalado na minha VM?](security-center-faq.md#scomomsinstalled)

-   Já há uma extensão de VM existente<br>
    - A Central de Segurança é compatível com instalações de extensão existentes, mas não substitui as conexões existentes. A Central de Segurança armazena dados da VM no espaço de trabalho já conectado e fornece proteção com base nas soluções habilitadas no espaço de trabalho.   
    - Para ver para qual espaço de trabalho a extensão existente está enviando dados, execute o teste para [Validar a conectividade com a Central de Segurança do Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Outra opção é abrir o Log Analytics, selecionar um espaço de trabalho, selecionar a VM e examinar a conexão do Microsoft Monitoring Agent. 
    - Se você tiver um ambiente no qual o Microsoft Monitoring Agent está instalado nas estações de trabalho do cliente e envia relatórios para um espaço de trabalho do Log Analytics existente, veja a lista de [sistemas operacionais compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md) para verificar se seu sistema operacional é compatível. Consulte [Clientes existentes do Log Analytics](security-center-faq.md#existingloganalyticscust) para obter mais informações.
 
### Desativar o provisionamento automático<a name="offprovisioning"></a>
É possível desativar o provisionamento automático de recursos a qualquer momento desativando essa configuração na política de segurança. 


1. Retorne ao menu principal da Central de Segurança e selecione a Política de segurança.
2. Selecione a assinatura em que você deseja desabilitar o provisionamento automático.
3. Na folha **Política de Segurança – Coleta de Dados** em **Provisionamento Automático** selecione **Desativar**.
4. Clique em **Salvar**.

  ![Desabilitar o provisionamento automático][6]

Quando o provisionamento automático está desabilitado (desativado), a seção de configuração do espaço de trabalho padrão não é exibida.

Se você desativar o provisionamento automático que estava ativado anteriormente:
-   Os agentes não serão provisionados em novas VMs.
-   A Central de Segurança interromperá a coleta de dados do espaço de trabalho padrão.
 
> [!NOTE]
>  Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure nas quais o agente foi provisionado. Para obter informações sobre como remover a extensão do OMS, consulte [Como fazer para remover extensões OMS instaladas pela Central de Segurança](security-center-faq.md#remove-oms).
>
    
## Provisionamento manual de agente <a name="manualagent"></a>
 
Há diversas maneiras de instalar o Microsoft Monitoring Agent manualmente. Verifique se o provisionamento automático está desabilitado ao efetuar a instalação manual.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implantação de extensão de VM do Operations Management Suite 

É possível instalar manualmente o Microsoft Monitoring Agent para que a Central de Segurança possa coletar dados de segurança das suas VMs e faça recomendações e alertas.
1.  Selecione o Provisionamento automático – Desativado.
2.  Crie um espaço de trabalho e defina o tipo de preço para o espaço de trabalho que você pretende definir no Microsoft Monitoring Agent:

    a.  No menu principal da Central de Segurança, selecione **Política de segurança**.
     
    b.  Selecione o Espaço de trabalho ao qual você pretende conectar o agente. Verifique se que o espaço de trabalho está na mesma assinatura usada na Central de Segurança e se você tem permissões de leitura/gravação no espaço de trabalho.
        ![Selecione o espaço de trabalho][8]
3. Defina o tipo de preço.
   ![Selecione o tipo de preço][9] 
   >[!NOTE]
   >Se o espaço de trabalho já tiver uma solução de **Segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 
   > 

4.  Se você quiser implantar os agentes em novas VMs usando um modelo do Resource Manager, instale a extensão de máquina virtual do OMS:

    a.  [Instalar a extensão da máquina virtual do OMS para Windows](../virtual-machines/extensions/oms-windows.md)
    
    b.  [Instalar a extensão da máquina virtual do OMS para Linux](../virtual-machines/extensions/oms-linux.md)
5.  Para implantar as extensões em VMs existentes, siga as instruções em [Coletar dados sobre Máquinas Virtuais do Microsoft Azure](../log-analytics/log-analytics-quick-collect-azurevm.md).

  > [!NOTE]
  > A seção **Coletar dados de desempenho e eventos** é opcional.
  >
6. Para usar o PowerShell para implantar a extensão: use o seguinte exemplo do PowerShell:
    1.  Acesse o **Log Analytics** e clique em **Configurações avançadas**.
    
        ![Definir o Log Analytics][11]

    2. Copie os valores de **WorkspaceID** e **Chave primária**.
  
       ![Copiar valores][12]

    3. Popule a configuração pública e a configuração privada com estes valores:
     
            $PublicConf = '{
                "workspaceId": "WorkspaceID value",
                "MultipleConnectistopOnons": true
            }' 
 
            $PrivateConf = '{
                "workspaceKey": "<Primary key value>”
            }' 

      - Ao instalar em uma VM do Windows:
        
             Set-AzureRmVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
       - Ao instalar em uma VM do Linux:
        
             Set-AzureRmVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`




## <a name="troubleshooting"></a>solução de problemas

-   Para identificar problemas de instalação do provisionamento automático, consulte [Problemas de integridade do agente de monitoramento](security-center-troubleshooting-guide.md#mon-agent).

-  Para identificar os requisitos de rede do agente de monitoramento, veja [Solução de problemas dos requisitos de rede do agente de monitoramento](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas de integração, consulte [Como solucionar problemas de integração do Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)

- Para identificar problemas de computadores e VMs não monitoradas, consulte [Computadores e VMs não monitoradas](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers)

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como a coleta de dados e o provisionamento automático na Central de Segurança funcionam. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
