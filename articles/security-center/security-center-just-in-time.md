---
title: Acesso JIT à máquina virtual na Central de Segurança do Azure | Microsoft Docs
description: Este documento demonstra como o acesso Just-In-Time à VM na Central de Segurança do Azure ajuda a controlar o acesso às suas máquinas virtuais do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: monhaber
ms.openlocfilehash: 66a7171aff7b9bab5f320df1d71e9cab4ce0477d
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758309"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gerenciar o acesso à máquina virtual usando JIT

O acesso Just-In-Time (JIT) à VM (máquina virtual) pode ser usado para bloquear o tráfego de entrada às suas VMs do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para conectar às VMs quando necessário.

> [!NOTE]
> O recurso Just-In-Time está disponível na camada Standard da Central de Segurança.  Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="attack-scenario"></a>Cenário de ataque

Ataques de força bruta geralmente se destinam às portas de gerenciamento como um meio para obter acesso a uma VM. Se for bem-sucedido, um invasor poderá assumir o controle sobre a VM e estabelecer uma base em seu ambiente.

Uma maneira de reduzir a exposição a um ataque de força bruta é limitar a quantidade de tempo que uma porta fica aberta. As portas de gerenciamento não precisam ficar abertas o tempo todo. Elas só precisam ser abertas enquanto você estiver conectado à VM, por exemplo, para realizar tarefas de manutenção ou gerenciamento. Quando o Just-In-Time está habilitado, a Central de Segurança usa as regras do [NSG](../virtual-network/security-overview.md#security-rules) (Grupo de Segurança de Rede), que restringem o acesso às portas de gerenciamento, de modo que elas não se tornem alvo de invasores.

![Cenário JIT](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Como funciona o acesso JIT?

Quando o JIT está habilitado, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure, criando uma regra de NSG. Você seleciona as portas na VM para as quais o tráfego de entrada será bloqueado. Essas portas são controladas pela solução Just-In-Time.

Quando um usuário solicita acesso a uma VM, a Central de Segurança verifica se o usuário tem permissões de [RBAC (Controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md), que permitem que eles solicitem com sucesso o acesso à VM. Se a solicitação for aprovada, a Central de Segurança configura automaticamente os NSGs (Grupos de Segurança de Rede) para permitir o tráfego de entrada às portas selecionadas e aos endereços ou intervalos IP de origem solicitados pelo período de tempo especificado. Depois que o tempo expirar, a Central de Segurança restaura os NSGs aos seus estados anteriores. No entanto, as conexões já estabelecidas não estão sendo interrompidas.

> [!NOTE]
> O acesso JIT à VM da Central de Segurança atualmente oferece suporte somente a VMs implantadas por meio do Azure Resource Manager. Para saber mais sobre os modelos de implantação clássica e do Resource Manager, consulte [Azure Resource Manager vs implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

Você pode acessar o JIT ao:
- [Usar o acesso JIT na Central de Segurança do Azure](#jit-asc)
- [Usar o acesso JIT em uma folha de VM do Azure](#jit-vm)

## Usar o acesso JIT na Central de Segurança do Azure <a name="jit-asc"></a>

1. Abra o painel **Central de Segurança**.

2. No painel à esquerda, selecione **Acesso à VM Just-In-Time**.

![Bloco de acesso JIT à VM](./media/security-center-just-in-time/just-in-time.png)

A janela **Acesso à VM Just-In-Time** é aberta.

![Bloco de acesso JIT à VM](./media/security-center-just-in-time/just-in-time-access.png)

O **Acesso à VM Just-In-Time** fornece informações sobre o estado das suas VMs:

- **Configurado**: VMs que foram configuradas para dar suporte ao acesso JIT à VM. Os dados apresentados são da última semana e incluem, para cada VM, o número de solicitações aprovadas, a data e a hora do último acesso e o último usuário.
- **Recomendado**: VMs que podem oferecer suporte ao acesso JIT à VM, mas não foram configuradas para isso. Recomenda-se que você habilite para essas VMs o controle de acesso JIT à VM. Consulte [Configurar uma política de acesso Just-In-Time](#jit-config).
- **Nenhuma recomendação** – as razões que podem fazer com que uma VM não seja recomendada são:
  - NSG ausente: a solução Just-In-Time exige que um NSG esteja em vigor.
  - VM Clássica: o acesso Just-In-Time à VM pela Central de Segurança atualmente oferece suporte apenas às VMs implantadas por meio do Azure Resource Manager. Não há suporte para uma implantação clássica na solução Just-In-Time.
  - Outros: uma VM entra nessa categoria se a solução Just-In-Time estiver desativada na política de segurança da assinatura ou do grupo de recursos, ou se estiver faltando um IP público da VM e ela não tiver um NSG em vigor.

### Configurar uma política de acesso JIT<a name="jit-config"></a>

Para selecionar as VMs que você deseja habilitar:

1. Em **Acesso à VM Just-In-Time**, selecione a guia **Recomendado**.

   ![Habilitar acesso Just-In-Time](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. Em **MÁQUINA VIRTUAL**, selecione as VMs que deseja habilitar. Isso coloca uma marca de seleção ao lado de uma VM.
3. Selecione **Habilitar JIT nas VMs**.
   1. Esta folha exibe as portas padrão recomendadas pela Central de Segurança do Azure:
      - 22: SSH
      - 3389: RDP
      - 5985: WinRM 
      - 5986: WinRM
   2. Você também pode configurar portas personalizadas. Para isso, selecione **Adicionar**. 
   3. Em **Adicionar configuração de porta** para cada porta que você optar por configurar, padrão ou personalizada, é possível personalizar as seguintes configurações:
      - **Tipo de protocolo**: o protocolo permitido nesta porta quando uma solicitação for aprovada.
      - **Endereços IP de origem permitida**: os intervalos de IP permitidos nesta porta quando uma solicitação for aprovada.
      - **Tempo máximo de solicitação**: o intervalo de tempo máximo durante o qual uma porta específica pode ficar aberta.

4. Clique em **Salvar**.


> [!NOTE]
>Quando o acesso à VM de JIT está habilitado para uma VM, a Central de segurança do Azure cria regras "Negar todo o tráfego de entrada" para as portas selecionadas nos grupos de segurança de rede associados a ele. Se outras regras tinham sido criadas para as portas selecionadas, em seguida, as regras existentes têm prioridade sobre as novas regras "Negar todo o tráfego de entrada". Se não houver nenhuma regra existente nas portas selecionadas, as novas regras "Negar todo o tráfego de entrada" levam a prioridade mais alta nos grupos de segurança de rede.
>

### <a name="request-jit-access-to-a-vm"></a>Solicitar acesso JIT a uma VM

Para solicitar acesso a uma VM:
1.  Em **Acesso à VM Just-In-Time**, selecione a guia **Configurado**.
2.  Em **VMs**, marque as VMs para as quais deseja habilitar o acesso Just-In-Time.
3.  Selecione **Solicitar acesso**. 
  ![Solicitar acesso à VM](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  Em **Solicitar acesso**, configure para cada VM as portas a abrir, os endereços IP de origem para os quais a porta fica aberta e o intervalo de tempo durante o qual a porta fica aberta. É possível solicitar acesso somente para as portas que estão configuradas na política Just-In-Time. Cada porta tem um tempo máximo permitido derivado da política Just-In-Time.
5.  Selecione **Abrir portas**.

> [!NOTE]
> Se um usuário que está solicitando acesso estiver atrás de um proxy, a opção **Meu IP** pode não funcionar. Pode ser que você precise definir o intervalo completo de endereços IP da organização.

### <a name="editing-a-jit-access-policy"></a>Editar uma política de acesso JIT

Você pode alterar a política Just-In-Time existente de uma VM adicionando e configurando uma nova porta a ser aberta para essa VM ou alterando qualquer outro parâmetro relacionado a uma porta já protegida.

Para editar a política Just-In-Time existente de uma VM:
1. Em **Configurado**, sob **VMs**, selecione a VM à qual será adicionada uma porta clicando nos três pontos dentro da linha dessa VM. 
2. Selecione **Editar**.
3. Em **Configuração de acesso JIT à VM**, você pode editar as configurações existentes de uma porta já protegida ou pode adicionar uma nova porta personalizada. Para saber mais, consulte [Configurar as políticas de acesso Just-In-Time](#jit-config). 
  ![Acesso JIT à VM](./media/security-center-just-in-time/edit-policy.png)

## Usar o acesso JIT em uma folha de VM do Azure<a name="jit-vm"></a>

Para sua conveniência, é possível se conectar a uma VM usando JIT diretamente de dentro da folha da VM no Azure.

### <a name="configuring-a-just-in-time-access-policy"></a>Configurar uma política de acesso Just-In-Time 
Para tornar mais fácil distribuir acesso Just-In-Time entre suas VMs, você pode definir uma VM para permitir apenas o acesso Just-In-Time diretamente de dentro da VM.

1. No portal do Azure, selecione **Máquinas Virtuais**.
2. Clique na máquina virtual que você deseja limitar o acesso Just-In-Time.
3. No menu, clique em **Configuração**.
4. Em **Acesso Just-In-Time** clique em **Habilitar política Just-In-Time**. 

Isso permite o acesso Just-In-Time para a VM usando as seguintes configurações:

- Servidores Windows:
    - RDP porta 3389
    - 3 horas de acesso máximo permitido
    - Endereços IP de origem permitidos é definida como qualquer
- Servidores Linux:
    - SSH porta 22
    - 3 horas de acesso máximo permitido
    - Endereços IP de origem permitidos é definida como qualquer
     
Se uma VM já tiver o Just-In-Time habilitado quando você acessar a página de configuração, você poderá ver que o Just-In-Time está habilitado e você pode usar o link para abrir a política na Central de Segurança do Azure para exibir e alterar as configurações.

![configuração de JIT na vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>Solicitar acesso JIT a uma VM

No portal do Azure, ao tentar se conectar a uma VM, o Azure verifica se você configurou uma política de acesso Just-In-Time naquela VM.

- Se não tiver configurado o JIT em uma VM, receberá uma solicitação para configurar a política JIT.

  ![Solicitação de JIT](./media/security-center-just-in-time/jit-prompt.png)

- Se houver uma política JIT configurada na VM, é possível clicar em **Solicitar acesso** para poder ter acesso de acordo com a política JIT definida para a VM. O acesso é solicitado com os seguintes parâmetros de padrão:
    - **IP de origem**: 'Any' (*) (não pode ser alterado)
    - **intervalo de tempo**: 3 horas (não pode ser alterado)
    - **número da porta** RDP da porta 3389 para Windows / porta 22 para Linux (você pode alterar o número da porta a **conectar-se a máquina virtual** caixa de diálogo.)


  >![Solicitar acesso JIT](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Auditar a atividade do acesso JIT

Você pode obter informações sobre as atividades de VM usando a pesquisa de logs. Para exibir os logs:

1. Em **Acesso à VM Just-In-Time**, selecione a guia **Configurado**.
2. Em **VMs**, selecione uma VM para exibir as respectivas informações, clicando nos três pontos dentro da linha dessa VM. Isso abre um menu.
3. Selecione **Log de atividades** no menu. Isso abre **Log de atividades**.

   ![Selecionar o log de atividades](./media/security-center-just-in-time/select-activity-log.png)

   **Log de atividades** fornece uma exibição filtrada das operações anteriores dessa VM junto com a hora, a data e a assinatura.

Você pode baixar as informações de log selecionando **Clique aqui para baixar todos os itens como CSV**.

Modifique os filtros e selecione **Aplicar** para criar uma pesquisa e um log.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e usar o JIT

Defina os privilégios necessários para permitir que um usuário configure ou edite uma política JIT para uma VM.

Atribua essas *ações* à função: 
- No escopo de uma assinatura ou Grupo de recursos que esteja associado à VM:
  - Microsoft.Security/locations/jitNetworkAccessPolicies/write
- No escopo de uma assinatura, Grupo de recursos ou VM:
  - Microsoft.Compute/virtualMachines/write 

Defina esses privilégios para permitir que um usuário solicite com êxito o acesso JIT a uma VM: Atribua essas *ações* ao usuário:
- No escopo de uma assinatura ou Grupo de recursos que esteja associado à VM:
  - Microsoft.Security/locations/{local_da_VM}/jitNetworkAccessPolicies/ initiate/action
- No escopo de uma Assinatura, Grupo de recursos ou VM:
  - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Use JIT via programação
Você pode configurar e usar Just-In-Time por meio de APIs REST e do PowerShell.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Usando o acesso Just-In-Time à VM por meio das APIs de REST

O recurso de acesso Just-In-Time à VM pode ser usado por meio da API da Central de Segurança do Azure. Você pode obter informações sobre VMs configuradas, adicionar novos, solicitar o acesso a uma VM e mais, por meio dessa API. Consulte [Políticas de acesso à rede JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) para saber mais sobre a API REST de Just-In-Time.

### <a name="using-jit-vm-access-via-powershell"></a>Usar o acesso JIT à VM por meio do PowerShell 

Para usar a solução de acesso Just-In-Time à VM por meio do PowerShell, use os cmdlets oficiais do PowerShell da Central de Segurança do Azure e, especificamente, `Set-AzJitNetworkAccessPolicy`.

O exemplo a seguir define uma política de acesso Just-In-Time à VM para uma VM específica e define o seguinte:
1.  Fechar as portas 22 e 3389.
2.  Definir uma janela de tempo máximo de 3 horas para cada, de modo que possam ser abertas por solicitação aprovada.
3.  Permitir que o usuário que está solicitando acesso controle os endereços IP de origem e permitir que o usuário estabeleça uma sessão com êxito mediante uma solicitação de acesso Just-In-Time aprovada.

Para fazer isso, execute o seguinte no PowerShell:

1.  Atribuir uma variável que contenha a política de acesso Just-In-Time à VM para uma VM:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Inserir a política de acesso Just-In-Time à VM em uma matriz:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configurar a política de acesso Just-In-Time à VM na VM selecionada:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>Solicitando acesso a uma VM

No exemplo a seguir, é possível ver uma solicitação de acesso Just-In-Time à VM para uma VM específica, na qual é solicitada a abertura da porta 22 para um endereço IP específico e por um período de tempo específico:

Execute o seguinte no PowerShell:
1.  Configurar as propriedades de acesso de solicitação da VM

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insira os parâmetros de solicitação de acesso da VM em uma matriz:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Enviar o acesso da solicitação (use a ID do recurso que você recebeu na etapa 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Para obter mais informações, consulte a documentação do cmdlet do PowerShell.


## <a name="next-steps"></a>Próximas etapas
Nesse artigo você aprendeu como o acesso Just-In-Time à VM na Central de Segurança o ajuda a controlar o acesso às suas máquinas virtuais do Azure.

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar recomendações de segurança](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
- [Monitoramento da integridade da segurança](security-center-monitoring.md) – saiba como monitorar a integridade dos seus recursos do Azure.
- [Gerenciar e responder aos alertas de segurança](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança.
- [Monitorar as soluções de parceiros](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes da Central de Segurança](security-center-faq.md) - encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.

