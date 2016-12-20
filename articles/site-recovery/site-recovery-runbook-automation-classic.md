---
title: "Adicionar runbooks de automação do Azure aos planos de recuperação | Microsoft Docs"
description: "Este artigo descreve como o Azure Site Recovery permite a ampliação dos planos de recuperação usando a Automação do Azure para concluir tarefas complexas durante a recuperação para o Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: f24eaa62-9dea-4fce-92e1-a72513ca0289
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 10/23/2016
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 946719d1cef70c841c119c49df8d17482f1e9b98


---
# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Adicionar runbooks de automação do Azure aos planos de recuperação - Clássico
Este tutorial descreve como o Azure Site Recovery é integrado à Automação do Azure para fornecer extensibilidade aos planos de recuperação. Os planos de recuperação podem coordenar a recuperação de máquinas virtuais protegidas usando o Azure Site Recovery para cenários de replicação na nuvem secundária e replicação no Azure. Eles também ajudam a tornar a recuperação **precisa de forma consistente**, **reproduzível** e **automatizada**. Se você estiver realizando o failover de suas máquinas virtuais para o Azure, a integração com a Automação do Azure estende os planos de recuperação e oferece a capacidade de executar runbooks, possibilitando tarefas avançadas de automação.

Se você ainda não souber o que é a Automação do Azure, inscreva-se [aqui](https://azure.microsoft.com/services/automation/) e baixe os exemplos de script [aqui](https://azure.microsoft.com/documentation/scripts/). Leia mais sobre o [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) e como coordenar a recuperação no Azure usando o planos de recuperação [aqui](https://azure.microsoft.com/blog/?p=166264).

Neste breve tutorial, veremos como você pode integrar os runbooks de automação do Azure aos planos de recuperação. Automatizaremos tarefas simples que exigiam uma intervenção manual e veremos como converter uma recuperação com várias etapa em uma ação de recuperação de clique único. Também veremos como você pode solucionar problemas de um script simples, caso ocorra algum erro.

## <a name="protect-the-application-to-azure"></a>Proteger o aplicativo no Azure
Vamos começa com um aplicativo simples composto por duas máquinas virtuais. Aqui, temos um aplicativo HRweb da Fabrikam. Fabrikam-HRweb-frontend e Fabrikam-Hrweb-backend são duas máquinas virtuais protegidas no Azure usando o Azure Site Recovery. Para proteger as máquinas virtuais com o Azure Site Recovery, execute as etapas abaixo.

1. Habilite a proteção para as máquinas virtuais.
2. Verifique se as máquinas virtuais concluíram a replicação inicial e se estão replicando.
3. Aguarde até a conclusão da replicação inicial e o status de Replicação indicar Protegido.

## ![](media/site-recovery-runbook-automation/01.png)
Neste tutorial, criaremos um plano de recuperação para o aplicativo Fabrikam HRweb, a fim de realizar o failover do aplicativo no Azure. Em seguida, realizaremos uma integração com um runbook que criará um ponto de extremidade na máquina virtual do Azure em estado de failover para servir páginas da Web na porta 80.

Primeiro, vamos criar um plano de recuperação para nosso aplicativo.

## <a name="create-the-recovery-plan"></a>Criar o plano de recuperação
Para recuperar o aplicativo no Azure, você precisa criar um plano de recuperação.
Com um plano de recuperação, você pode especificar a ordem de recuperação das máquinas virtuais. A máquina virtual colocada no grupo 1 será recuperada e iniciada primeiro, em seguida, a máquina virtual no grupo 2.

Crie um Plano de recuperação parecido com o exibido abaixo.

![](media/site-recovery-runbook-automation/12.png)

Para ler mais sobre os planos de recuperação, leia a documentação contida [aqui](https://msdn.microsoft.com/library/azure/dn788799.aspx "aqui").

Em seguida, vamos criar os artefatos necessários na Automação do Azure.

## <a name="create-the-automation-account-and-its-assets"></a>Criar a conta de automação e seus ativos
Você precisa de uma conta de Automação do Azure para criar runbooks. Se você ainda não tiver uma conta, navegue até a guia Automação do Azure indicada por ![](media/site-recovery-runbook-automation/02.png)e crie uma nova conta.

1. Dê um nome à conta para identificá-la.
2. Especifique uma região geográfica na qual você deseja colocar a conta.

É recomendável colocar a conta na mesma região que o cofre ASR.

![](media/site-recovery-runbook-automation/03.png)

Em seguida, crie os seguintes ativos na Conta.

### <a name="add-a-subscription-name-as-asset"></a>Adicionar um nome de assinatura como ativo
1. Adicione uma nova configuração ![](media/site-recovery-runbook-automation/04.png) nos Ativos de Automação do Azure e selecione ![](media/site-recovery-runbook-automation/05.png)
2. Selecione o tipo de variável como **String**
3. Especifique o nome da variável como **AzureSubscriptionName**
   
   ![](media/site-recovery-runbook-automation/06.png)
4. Especifique o nome real de sua Assinatura do Azure como o valor da variável.
   
   ![](media/site-recovery-runbook-automation/07_1.png)

Você pode identificar o nome de sua assinatura na página Configurações de sua conta no Portal do Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Adicionar uma credencial de logon do Azure como ativo
A Automação do Azure usa o Azure PowerShell para se conectar à assinatura e opera nos artefatos de lá. Para isso, você precisa autenticar usando sua conta da Microsoft ou uma conta corporativa ou de estudante.
Você pode armazenar as credenciais da conta em um ativo que será usado com segurança pelo runbook.

1. Adicione uma nova configuração ![](media/site-recovery-runbook-automation/04.png) nos Ativos de Automação do Azure e selecione ![](media/site-recovery-runbook-automation/09.png)
2. Selecione o Tipo de credencial como **Credencial do Windows PowerShell**
3. Especifique o nome como **AzureCredential**
   
   ![](media/site-recovery-runbook-automation/10.png)
4. Especifique o nome de usuário e a senha para logon.

Agora, essas duas configurações estão disponíveis em seus ativos.

![](media/site-recovery-runbook-automation/11.png)

Para saber mais sobre como se conectar à sua assinatura por meio do PowerShell clique [aqui](../powershell-install-configure.md).

Em seguida, você criará um runbook na Automação do Azure que pode adicionar um ponto de extremidade à máquina virtual front-end após o failover.

## <a name="azure-automation-context"></a>Contexto de automação do Azure
ASR passa uma variável de contexto para o runbook a fim de ajudar você a escrever scripts deterministas. Alguém poderia argumentar que os nomes do Serviço de nuvem e da Máquina virtual são previsíveis, mas nem sempre esse é o caso devido a certos cenários, por exemplo, aquele no qual o nome da máquina virtual pode ter sido alterado devido a caracteres sem suporte no Azure. Portanto, essas informações são passadas ao plano de recuperação ASR como parte do *contexto*.

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

Para identificar a Chave de VmMap no contexto, também é possível acessar a página de propriedades da VM no ASR e examinar a propriedade VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Criar um runbook de automação
Agora, crie o runbook para abrir a porta 80 na máquina virtual front-end.

1. Crie um novo runbook na conta de Automação do Azure com o nome **OpenPort80**
   
   ![](media/site-recovery-runbook-automation/14.png)
2. Navegue até o modo de exibição de Criação do runbook e entre no modo de rascunho.
3. Primeiro, especifique a variável a ser usada como o contexto do plano de recuperação
   
   ```
       param (
           [Object]$RecoveryPlanContext
       )
   
   ```
4. Em seguida, conecte-se à assinatura usando o nome da credencial e da assinatura
   
   ```
       $Cred = Get-AutomationPSCredential -Name 'AzureCredential'
   
       # Connect to Azure
       $AzureAccount = Add-AzureAccount -Credential $Cred
       $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
       Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
   ```
   
   Observe que você usa aqui os ativos do Azure – **AzureCredential** e **AzureSubscriptionName**.
5. Agora, especifique os detalhes do ponto de extremidade e o GUID da máquina virtual para a qual você deseja expor o ponto de extremidade. Neste caso, a máquina virtual de front-end.
   
   ```
       # Specify the parameters to be used by the script
       $AEProtocol = "TCP"
       $AELocalPort = 80
       $AEPublicPort = 80
       $AEName = "Port 80 for HTTP"
       $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
   ```
   
   Isso especifica o protocolo do ponto de extremidade do Azure, a porta local na VM e sua porta pública mapeada. Essas variáveis são parâmetros exigidos pelos comandos do Azure que adicionam pontos de extremidade às máquinas virtuais. O VMGUID contém o GUID da máquina virtual na qual você precisa operar.
6. Agora, o script extrairá o contexto do VM GUID determinado e criará um ponto de extremidade na máquina virtual indicada por ele.
   
   ```
       #Read the VM GUID from the context
       $VM = $RecoveryPlanContext.VmMap.$VMGUID
   
       if ($VM -ne $null)
       {
           # Invoke pipeline commands within an InlineScript
   
           $EndpointStatus = InlineScript {
               # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
               # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)
   
               $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                   Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                   Update-AzureVM
               Write-Output $Status
           }
       }
   ```
7. Quando isso for concluído, pressione Publicar ![](media/site-recovery-runbook-automation/20.png) para permitir que seu script esteja disponível para execução.

O script completo é fornecido abaixo para sua referência

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Adicione o script ao plano de recuperação
Quando o script estiver pronto, você poderá adicioná-lo ao plano de recuperação criado anteriormente.

1. No plano de recuperação que você criou, escolha adicionar um script após o grupo 2. ![](media/site-recovery-runbook-automation/15.png)
2. Especifique um nome de script. Esse é apenas um nome amigável para mostrar o script no Plano de recuperação.
3. No script do failover para o Azure – Selecione o nome da Conta de Automação do Azure.
4. Nos Runbooks do Azure, selecione o runbook criado por você.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Scripts do lado principal
Quando você estiver executando um failover para o Azure, também poderá optar por executar scripts do lado principal. Esses scripts serão executados no servidor VMM durante o failover.
Scripts do lado principal só ficam disponíveis para estágios pré-desligamento e pós-desligamento. Isso ocorre porque esperamos que o site principal fique normalmente indisponível quando ocorre um desastre.
Durante um failover não planejado, apenas se você optar por operações de site principal, ele tentará executar os scripts do lado principal. Se não for possível acessá-los ou tempo limite esgotar, o failover continuará a recuperar as máquinas virtuais.
Scripts do lado principal não estão disponíveis para sites de VMware/físico/Hyper-v sem o VMM protegido para o Azure - durante o failover para o Azure.
No entanto, quando você realiza o failback do Azure para o local, os scripts do lado principal (Runbooks) podem ser usados em todos os destinos, exceto no VMware.

## <a name="test-the-recovery-plan"></a>Testar o plano de recuperação
Após adicionar o runbook ao plano, você poderá iniciar um failover de teste e vê-lo em ação. É sempre recomendável executar um failover de teste para testar seu aplicativo e o plano de recuperação a fim de garantir que não exista erros.

1. Selecione o plano de recuperação e inicie um failover de teste.
2. Durante a execução do plano, você pode ver se o runbook foi executado ou não por meio do status.
   
   ![](media/site-recovery-runbook-automation/17.png)
3. Você também pode ver o status detalhado de execução do runbook na página de trabalhos da Automação do Azure para o runbook.
   
   ![](media/site-recovery-runbook-automation/18.png)
4. Após a conclusão do failover, exceto o resultado da execução do runbook, você poderá ver se a execução foi bem-sucedida ou não, visitando a página da máquina virtual do Azure e observando os pontos de extremidade.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Exemplos de scripts
Embora tenhamos mostrado neste tutorial como automatizar uma tarefa usada normalmente, a adição de um ponto de extremidade a uma máquina virtual do Azure, você pode fazer várias outras tarefas avançadas de automação usando a Automação do Azure. A Microsoft e a comunidade da Automação do Azure fornecem exemplos de runbooks que podem ajudar você a começar a criar suas próprias soluções, e de runbooks utilitários, que você pode usar como blocos de construção para tarefas maiores de automação. Comece a usá-los na galeria e crie planos de recuperação avançados e realizados com um único clique para seus aplicativos usando o Azure Site Recovery.

## <a name="additional-resources"></a>Recursos adicionais
[Visão geral da Automação](http://msdn.microsoft.com/library/azure/dn643629.aspx "Visão geral da Automação")

[Exemplos de scripts da Automação do Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Exemplos de scripts da Automação do Azure")




<!--HONumber=Nov16_HO3-->


