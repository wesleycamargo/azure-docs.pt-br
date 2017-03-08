---
title: "Azure Active Directory Domain Services: guia de administração | Microsoft Docs"
description: "Ingresse uma máquina virtual do Windows em um domínio gerenciado usando o Azure PowerShell e o modelo de implantação clássico."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: fdc53ee24c623218e218cdda1c24dffbbec50bc5
ms.openlocfilehash: e7c7e9504985fa98185286ee9a28cec80fb87df6
ms.lasthandoff: 01/05/2017


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Ingressar uma máquina virtual do Windows Server em um domínio gerenciado usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal clássico do Azure - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássica. Os Serviços de Domínio do Azure AD no momento não dá suporte para o modelo do Resource Manager.
>
>

Estas etapas mostram como personalizar um conjunto de comandos do Azure PowerShell que criam e pré-configuram uma máquina virtual do Azure baseada em Windows usando uma abordagem de bloco de construção. Essas etapas ajudam a criar uma máquina virtual do Azure baseada no Windows e a ingressá-la no domínio gerenciado dos Serviços de Domínio do Azure AD.

Estas etapas seguem uma abordagem de preencher lacunas para criar conjuntos de comandos do Azure PowerShell. Esta abordagem poderá ser útil se você ainda não conhece o PowerShell ou se quiser saber quais valores especificar para uma configuração bem-sucedida. Os usuários avançados do PowerShell podem pegar os comandos e substituí-los por seus próprios valores de variáveis (as linhas que começam com "$").

Se você ainda não fez isso, use as instruções em [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs) para instalar o PowerShell do Azure no computador local. Em seguida, abra um prompt de comando do Windows PowerShell.

## <a name="step-1-add-your-account"></a>Etapa 1: adicionar sua conta
1. No prompt do PowerShell, digite **Add-AzureAccount** e clique em **Enter**.
2. Digite o endereço de email associado à sua assinatura do Azure e clique em **Continuar**.
3. Digite a senha da sua conta.
4. Clique em **Entrar**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Etapa 2: Definir a assinatura e a conta de armazenamento
Defina a assinatura e a conta de armazenamento do Azure executando estes comandos no prompt de comando do Windows PowerShell. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelos nomes corretos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Você pode obter o nome de assinatura correto na propriedade SubscriptionName da saída do comando **Get-AzureSubscription** . Você pode obter o nome da conta de armazenamento correto na propriedade Label da saída do comando **Get-AzureStorageAccount**, após executar o comando **Select-AzureSubscription**.

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Etapa 3: Passo a passo - provisionar a máquina virtual e ingressa-la ao domínio gerenciado
Aqui está o conjunto de comandos do PowerShell do Azure correspondente para criar essa máquina virtual, com linhas em branco entre cada bloco para facilitar a leitura.

Especifique as informações sobre a máquina virtual do Windows a ser provisionada.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Para os valores de InstanceSize para máquinas virtuais da série D, DS ou G, confira [Tamanhos de máquina virtual e serviços de nuvem no Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Forneça informações sobre o domínio gerenciado.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Especifique o nome do serviço de nuvem.

    $svcname="Contoso100-test"

Especifique o nome da rede virtual na qual a VM deve ser ingressada. Certifique-se de que o domínio gerenciado do DS do AAD esteja disponível nessa rede virtual.

    $vnetname="MyPreviewVnet"

Selecione a imagem da VM a ser usada para provisionar a VM.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configure a VM: defina o nome da VM, o tamanho da instância e a imagem a ser usada.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obtenha credenciais de administrador local para a VM. Escolha uma senha de administrador local forte.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obtenha credenciais de uma conta de usuário que pertence ao grupo ‘Administradores do DC do AAD’ para ingressar a VM ao domínio gerenciado. Não especifique o nome de domínio: por exemplo, em nosso exemplo, podemos especificar “bob” como nome de usuário.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configure a VM: especifique o requisito de ingresso no domínio e as credenciais necessárias.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Defina uma sub-rede para a VM.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Opcional: aponte para o endereço IP do domínio. Se você definir os endereços IP dos domínios gerenciados de Serviços de Domínio do Azure AD para serem servidores DNS para a rede virtual, essa etapa não será necessária.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Agora, provisione a VM do Windows ingressada no domínio.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script para provisionar uma VM do Windows e automaticamente ingressar em um domínio gerenciado dos Serviços de Domínio do AAD
Esse conjunto de comandos do PowerShell cria uma máquina virtual para um servidor de linha de negócios que:

* Usa a imagem do Windows Server 2012 R2 Datacenter.
* É uma máquina virtual extra pequena.
* Tem o nome Contoso100-test.
* É automaticamente ingressada no domínio para o domínio gerenciado contoso100.
* É adicionado à mesma rede virtual como o domínio gerenciado.

Aqui está o script de exemplo completo para criar a máquina virtual do Windows e ingressá-la automaticamente no domínio gerenciado dos Serviços de Domínio do Azure AD.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

