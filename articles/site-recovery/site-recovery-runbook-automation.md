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
ms.date: 02/06/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 44b6ff6e588d529fd833a4a7fdd61df7e933ddd8
ms.openlocfilehash: b88974ef713211a40b52aafab1b079ed8dbfec49


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar runbooks de automação do Azure aos planos de recuperação
Este tutorial descreve como o Azure Site Recovery é integrado à Automação do Azure para fornecer extensibilidade aos planos de recuperação. Os planos de recuperação podem coordenar a recuperação de máquinas virtuais protegidas usando o Azure Site Recovery para cenários de replicação na nuvem secundária e replicação no Azure. Eles também ajudam a tornar a recuperação **precisa de forma consistente**, **reproduzível** e **automatizada**. Se você estiver realizando o failover de suas máquinas virtuais para o Azure, a integração com a Automação do Azure estende os planos de recuperação e oferece a capacidade de executar runbooks, possibilitando tarefas avançadas de automação.

Se você ainda não souber o que é a Automação do Azure, inscreva-se [aqui](https://azure.microsoft.com/services/automation/) e baixe os exemplos de script [aqui](https://azure.microsoft.com/documentation/scripts/). Leia mais sobre o [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) e como coordenar a recuperação no Azure usando o planos de recuperação [aqui](https://azure.microsoft.com/blog/?p=166264).

Neste tutorial, veremos como você pode integrar os runbooks de automação do Azure aos planos de recuperação. Automatizaremos tarefas simples que exigiam uma intervenção manual e veremos como converter uma recuperação com várias etapa em uma ação de recuperação de clique único. Também veremos como você pode solucionar problemas de um script simples, caso ocorra algum erro.

## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação
1. Vamos começa abrindo a folha de recursos do plano de recuperação. É possível ver que o plano de recuperação tem duas máquinas virtuais adicionadas a ele para recuperação.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Clique no botão personalizar para começar a adicionar um runbook. Isso abrirá a folha de personalização do plano de recuperação.

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Clique com o botão direito do mouse no grupo de inicialização 1 e selecione para adicionar um “Adicionar ação posterior”.
2. Selecione esta opção para escolher um script na nova folha.
3. Nomeie o script “Hello, World”.
4. Escolha um nome para a Conta de Automação. Esta é a conta de Automação do Azure. Observe que esta conta pode estar em qualquer geografia do Azure, mas ela precisa estar na mesma assinatura que o cofre de Site Recovery.
5. Selecione um runbook na Conta de Automação. Este é o script que será executado durante a execução do plano de recuperação após a recuperação do primeiro grupo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Selecione OK para salvar o script. Isso adicionará o script ao grupo de ação posterior do Grupo 1: iniciar.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Pontos de destaque da adição de um script
1. É possível clicar com o botão direito do mouse no script e escolher “excluir etapa” ou “atualizar script”.
2. Um script pode ser executado no Azure durante o failover do Local para o Azure e pode ser executado no Azure como um script de lado primário antes do desligamento, durante o failback do Azure para o local.
3. Quando um script é executado, ele injetará um contexto do plano de recuperação.

Veja abaixo um exemplo da aparência da variável de contexto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


A tabela a seguir contém o nome e uma descrição para cada variável no contexto.

| **Nome da variável** | **Descrição** |
| --- | --- |
| RecoveryPlanName |Nome do plano de execução. Ajuda você a agir com base no nome usando o mesmo script |
| FailoverType |Especifica se o failover é de teste, planejado ou não planejado. |
| FailoverDirection |Especifica se a recuperação é para o principal ou secundário |
| GroupID |Identifica o número de grupo no plano de recuperação quando o plano está em execução |
| VmMap |Matriz de todas as máquinas virtuais do grupo |
| Chave VMMap |Chave exclusiva (GUID) para cada VM. É igual à ID do VMM da máquina virtual onde aplicável. |
| RoleName |Nome da VM do Azure que está sendo recuperada |
| CloudServiceName |Nome do serviço de nuvem do Azure sob o qual a máquina virtual é criada. |
| CloudServiceName (no modelo de implantação do Resource Manager) |O nome do Grupo de Recursos do Azure no qual a máquina virtual é criada. |

## <a name="using-complex-variables-per-recovery-plan"></a>Usando variáveis complexos por plano de recuperação
Às vezes, um runbook exige mais informações do que apenas o RecoveryPlanContext. Não há nenhum mecanismo de primeira classe para passar um parâmetro para um runbook. No entanto, se você desejar usar o mesmo script por meio de vários planos de recuperação, use a variável “RecoveryPlanName” de Contexto do Plano de Recuperação e use a técnica experimental abaixo para usar uma variável complexa de Automação do Azure em um runbook. O exemplo a seguir mostra como você pode criar três ativos de variável complexa diferentes e usá-los no runbook com base no nome do plano de recuperação.

Considere a possibilidade de usar 3 parâmetros adicionais em um runbook. Vamos codificá-los em um formato JSON {"Var1":"testautomation","Var2":"Unplanned","Var3":"PrimaryToSecondary"}

Use [Variável complexa de AA](../automation/automation-variables.md#variable-types) para criar um novo ativo de Automação.
Nomeie a variável como <RecoveryPlanName>- params.
É possível usar a referência aqui para criar uma [variável complexa](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Para diferentes planos de recuperação, nomeie a variável como

1. recoveryPlanName1>-params
2. recoveryPlanName2>-params
3. recoveryPlanName3>-params

Agora, no script, fazer referência aos parâmetros como

1. Obtenha o nome do RP da variável $rpname = $Recoveryplancontext
2. Obtenha o ativo de $paramValue = "$($rpname)-params"
3. Use isso como uma variável complexa para o plano de recuperação chamando Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue.

Por exemplo, para obter a variável complexa/parâmetro para o plano de recuperação do SharepointApp, crie uma variável de complexos de Automação do Azure chamada 'params SharepointApp'.

Use-a no plano de recuperação extraindo a variável do ativo por meio da instrução Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue. [Consulte isto para obter mais detalhes](https://msdn.microsoft.com/library/dn913772.aspx)

Dessa forma, o mesmo script pode ser usado para um plano de recuperação diferente armazenando a variável complexa específica do plano nos ativos.

## <a name="sample-scripts"></a>Exemplos de scripts
Para um repositório de scripts que você pode importar diretamente para sua conta de automação, consulte [Repositório do OMS para scripts de Kristian Nese](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

O script aqui é um modelo do Azure Resource Manager que implantará todos os scripts abaixo

* NSG

O runbook NSG atribuirá endereços IP públicos a cada VM no Plano de Recuperação e anexará seus adaptadores de rede virtual a um grupo de segurança de rede que permitem a comunicação padrão

* PublicIP

O runbook do IP público atribuirá endereços IP públicos a cada VM no Plano de Recuperação. O acesso a computadores e aplicativos dependerão das configurações de firewall em cada convidado

* CustomScript

O runbook CustomScript atribuirá endereços IP públicos a cada VM no Plano de Recuperação e instalará uma extensão de script personalizado que efetuará o pull do script que você consultar durante a implantação do modelo

* NSGwithCustomScript

O runbook NSGwithCustomScript atribuirá endereços IP públicos a cada VM no Plano de Recuperação, instalará um script personalizado usando a extensão e conectará os adaptadores de rede virtual a um NSG que permita comunicação padrão de entrada e saída para acesso remoto

## <a name="additional-resources"></a>Recursos adicionais
[Conta Executar como do serviço de Automação do Azure](../automation/automation-sec-configure-azure-runas-account.md)

[Visão geral da Automação](http://msdn.microsoft.com/library/azure/dn643629.aspx "Visão geral da Automação")

[Exemplos de scripts da Automação do Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Exemplos de scripts da Automação do Azure")



<!--HONumber=Jan17_HO5-->


