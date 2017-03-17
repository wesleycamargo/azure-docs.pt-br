---
title: "Adicionar runbooks de automação do Azure aos planos de recuperação no Site Recovery | Microsoft Docs"
description: "Este artigo descreve como o Azure Site Recovery permite a ampliação dos planos de recuperação usando a Automação do Azure para concluir tarefas complexas durante a recuperação para o Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: ac56273cf85aff550febecf0d75ec87d5c6dbbca
ms.openlocfilehash: 26547135548dde96e9da601f2e0ccfe96c626880
ms.lasthandoff: 02/23/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar runbooks de automação do Azure aos planos de recuperação
Este tutorial descreve como o Azure Site Recovery é integrado à Automação do Azure para fornecer extensibilidade aos planos de recuperação. Os planos de recuperação podem coordenar a recuperação de máquinas virtuais protegidas usando o Azure Site Recovery para cenários de replicação na nuvem secundária e replicação no Azure. Eles também ajudam a tornar a recuperação **precisa de forma consistente**, **reproduzível** e **automatizada**. Se você estiver realizando o failover de suas máquinas virtuais para o Azure, a integração com a Automação do Azure estende os planos de recuperação e oferece a capacidade de executar runbooks, possibilitando tarefas avançadas de automação.

Se você ainda não souber o que é a Automação do Azure, inscreva-se [aqui](https://azure.microsoft.com/services/automation/) e baixe os exemplos de script [aqui](https://azure.microsoft.com/documentation/scripts/). Leia mais sobre o [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) e como coordenar a recuperação no Azure usando o planos de recuperação [aqui](https://azure.microsoft.com/blog/?p=166264).

Neste tutorial, mostramos como você pode integrar os runbooks de Automação do Azure aos planos de recuperação. Automatizamos tarefas simples que exigiam uma intervenção manual e veremos como converter uma recuperação com várias etapas em uma ação de recuperação de clique único.

## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação
1. Vamos começa abrindo a folha de recursos do plano de recuperação. É possível ver que o plano de recuperação tem duas máquinas virtuais adicionadas a ele para recuperação.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Clique no botão personalizar para começar a adicionar um runbook. 

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Clique com o botão direito do mouse no grupo de inicialização 1 e selecione para adicionar um “Adicionar ação posterior”.
2. Selecione esta opção para escolher um script na nova folha.
3. Nomeie o script “Hello, World”.
4. Escolha um nome para a Conta de Automação. 
    >[!NOTE]
    > A conta de automação pode estar em qualquer geografia do Azure, mas ela precisa estar na mesma assinatura que o cofre de Site Recovery.
    
5. Selecione um runbook na Conta de Automação. Esse runbook é o script que será executado durante a execução do plano de recuperação após a recuperação do primeiro grupo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Selecione OK para salvar o script. O script será adicionado ao grupo de ação posterior do Grupo 1: Iniciar.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Pontos de destaque da adição de um script
1. É possível clicar com o botão direito do mouse no script e escolher “excluir etapa” ou “atualizar script”.
2. Um script pode ser executado no Azure durante o failover do Local para o Azure e pode ser executado no Azure como um script de lado primário antes do desligamento, durante o failback do Azure para o local.
3. Quando um script é executado, ele injeta um contexto do plano de recuperação.

Veja a seguir um exemplo da aparência da variável de contexto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
                
                "ResourceGroupName":"ContosoRG",
                
                "CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test",
                
                "RecoveryPointId":"TimeStamp"}

                }

        }


A tabela a seguir contém o nome e uma descrição para cada variável no contexto.

| **Nome da variável** | **Descrição** |
| --- | --- |
| RecoveryPlanName |Nome do plano de execução. Essa variável ajuda você a executar diferentes ações com base no nome e reutilizando o script |
| FailoverType |Especifica se o failover é de teste, planejado ou não planejado. |
| FailoverDirection |Especifica se a recuperação é para o principal ou secundário |
| GroupID |Identifica o número de grupo no plano de recuperação quando o plano está em execução |
| VmMap |Matriz de todas as máquinas virtuais do grupo |
| Chave VMMap |Chave exclusiva (GUID) para cada VM. É igual à ID do VMM da máquina virtual onde aplicável. |
| SubscriptionId |ID da Assinatura do Azure em que a VM é criada. |
| RoleName |Nome da VM do Azure que está sendo recuperada |
| CloudServiceName |Nome do serviço de nuvem do Azure sob o qual a máquina virtual é criada. |
| ResourceGroupName|O nome do Grupo de Recursos do Azure no qual a máquina virtual é criada. |
| RecoveryPointId|Carimbo de data/hora em que a VM é recuperada. |

Você também precisa garantir que a Conta de Automação tenha os seguintes módulos adicionados. Todos os módulos devem ser de versões compatíveis. Uma maneira fácil para se certificar de que todos os módulos tenham a versão mais recente disponível.
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>Acessar todas as VMs do VmMap em um loop
Use o trecho a seguir para executar um loop em todas as VMs do VmMap.

```
    $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
    $vmMap = $RecoveryPlanContext.VmMap
     foreach($VMID in $VMinfo)
     {
         $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
             #this check is to ensure that we skip when some data is not available else it will fail
         Write-output "Resource group name ", $VM.ResourceGroupName
         Write-output "Rolename " = $VM.RoleName
         }
     }

```

> [!NOTE]
> O nome do Grupo de Recursos e os valores de nome de Função estão vazios quando o script é uma ação prévia a um grupo de inicialização. Os valores só são preenchidos se a VM do grupo conseguir fazer o failover e o script for uma ação posterior ao grupo de inicialização.

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>Usar o mesmo runbook de Automação com vários planos de recuperação

Um único script pode ser usado em vários planos de recuperação por meio do uso de variáveis externas. Você pode usar as [variáveis de Automação do Azure](../automation/automation-variables.md) para armazenar os parâmetros que podem ser passados para a execução de um plano de recuperação. Ao corrigir previamente a variável com o nome do plano de recuperação, você pode criar variáveis individuais para cada plano de recuperação e usá-las como parâmetro. Você pode alterar o parâmetro sem alterar o script e fazer com que o script funcione de modo diferente.

### <a name="using-simple-string-variables-in-runbook-script"></a>Uso de variáveis de cadeia de caracteres simples no script de runbook

Considere o script que usa a entrada de um NSG e a aplica às VMs de um plano de recuperação.

Para que o script entenda qual plano de recuperação está em execução, você pode usar o Contexto do Plano de Recuperação.

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Para aplicar um NSG existente, você precisa do nome do NSG e do grupo de recursos do NSG. Nós fornecemos essas variáveis como uma entrada para os scripts do plano de recuperação. Para fazer isso, crie duas variáveis nos ativos das contas de Automação e um inclua um prefixo com o nome do plano de recuperação para o qual os parâmetros estão sendo criados.

1. Crie uma variável para armazenar o nome do NSG. Inclua um prefixo com o nome do plano de recuperação.
    ![Criar variável do nome do NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Crie uma variável para armazenar o nome do grupo de recursos do NSG. Inclua um prefixo com o nome do plano de recuperação.
    ![Criar nome do grupo de recursos do NSG](media/site-recovery-runbook-automation-new/var2.png)


No script, obtenha os valores das variáveis usando o seguinte código de referência:

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue 
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

Em seguida, você pode usar as variáveis no runbook e aplicar o NSG as Adaptador de Rede de máquina virtual em que foi feito o failover.

```
     InlineScript { 
         if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
```

Para cada plano de recuperação, crie variáveis independentes para que você possa reutilizar o script e incluir um prefixo com o nome do plano de recuperação. Um script completo de ponta a ponta para este exemplo é [fornecido aqui](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="using-complex-variable-to-store-more-information"></a>Usar variável complexa para armazenar mais informações

Considere um cenário em que você deseja que apenas um script ative um IP público em VMs específicas. Outro exemplo seria aplicar NSGs diferentes a máquinas virtuais diferentes (não todas). Esse script deve poder ser reutilizado em qualquer outro plano de recuperação. Cada plano de recuperação pode ter um número variável de máquinas virtuais (exemplo, uma recuperação de sharepoint tem dois front-ends, um aplicativo de LOB simples tem apenas um front-end). Para obter esse resultado, é impossível criar variáveis separadas por plano de recuperação. Aqui, nós usamos uma nova técnica e criamos uma [variável complexa](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) nos ativos da conta de Automação do Azure, especificando vários valores. Você precisa do Azure PowerShell para executar as etapas a seguir.

1. No Azure PowerShell, faça logon para sua assinatura.

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. Para armazenar os parâmetros, crie uma variável complexa com o mesmo nome do plano de recuperação.

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    Na variável complexa, **VMDetails* é a ID de VM da máquina virtual protegida. Você pode encontrar essa informação nas propriedades da máquina virtual no portal. Aqui, criamos uma variável para armazenar os detalhes de duas máquinas virtuais.

    ![ID da VM a ser usada como GUID](media/site-recovery-runbook-automation-new/vmguid.png)

3. Use essa variável em seu runbook e aplique o NSG à máquina virtual se alguma das VMGUID especificadas for encontrada no contexto do plano de recuperação.

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName 
    ```

4. Em seu runbook, execute um loop nas VMs do contexto do plano de recuperação e verifique se a VM também existe em **$VMDetailsObj**. Se existir, aplique o NSG ao acessar as propriedades da variável.
    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap
           
        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID
            
            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Você pode usar o mesmo script com planos de recuperação diferentes e fornecer parâmetros diferentes ao armazenar o valor correspondente a diferentes planos de recuperação em outra variável.

## <a name="sample-scripts"></a>Exemplos de scripts
Para um repositório de scripts que você pode importar diretamente para sua conta de automação, consulte [Repositório do OMS para scripts de Kristian Nese](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

O script aqui é um modelo do Azure Resource Manager que implantará todos os seguintes scripts

* NSG

O runbook do NSG vai atribuir endereços IP públicos a cada VM no Plano de Recuperação e anexar seus adaptadores de rede virtual a um grupo de segurança de rede que permite a comunicação padrão

* PublicIP

O runbook do IP público atribui endereços IP públicos a cada VM no Plano de Recuperação. O acesso a computadores e aplicativos dependerão das configurações de firewall em cada convidado

* CustomScript

O runbook CustomScript atribui endereços IP públicos a cada VM no Plano de Recuperação e instalará uma extensão de script personalizado que efetuará o pull do script que você consultar durante a implantação do modelo

* NSGwithCustomScript

O runbook NSGwithCustomScript atribui endereços IP públicos a cada VM no Plano de Recuperação, instalará um script personalizado usando a extensão e conectará os adaptadores de rede virtual a um NSG que permita comunicação padrão de entrada e saída para acesso remoto

## <a name="additional-resources"></a>Recursos adicionais
[Conta Executar como do serviço de Automação do Azure](../automation/automation-sec-configure-azure-runas-account.md)

[Visão geral da Automação](http://msdn.microsoft.com/library/azure/dn643629.aspx "Visão geral da Automação")

[Exemplos de scripts da Automação do Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Exemplos de scripts da Automação do Azure")

