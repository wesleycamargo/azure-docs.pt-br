---
title: Perguntas frequentes sobre o Azure DevTest Labs | Microsoft Docs
description: Encontre respostas para perguntas comuns sobre o Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: e0adac5193ae412845c8413dfee6b5557096359a
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017


---
# <a name="azure-devtest-labs-faq"></a>Perguntas frequentes sobre o Azure DevTest Labs
Este artigo responde algumas das perguntas mais comuns sobre o Azure DevTest Labs.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Geral
* [E se dúvida não foi respondida aqui?](#what-if-my-question-isnt-answered-here)
* [Por que devo usar o Azure DevTest Labs?](#why-should-i-use-azure-devtest-labs)
* [O que significa "autoatendimento sem preocupação"?](#what-does-worry-free-self-service-mean)
* [Como usar o Azure DevTest Labs?](#how-can-i-use-azure-devtest-labs)
* [Como eu sou cobrado pelo Azure DevTest Labs?](#how-am-i-billed-for-azure-devtest-labs)

## <a name="security"></a>Segurança
* [Quais são os diferentes níveis de segurança do Azure DevTest Labs?](#what-are-the-different-security-levels-in-azure-devtest-labs)
* [Como criar uma função para permitir que os usuários executem uma tarefa específica?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task)

## <a name="cicd-integration--automation"></a>Integração e Automação de CI/CD
* [O Azure DevTest Labs se integra à minha cadeia de ferramentas CI/CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain)

## <a name="virtual-machines"></a>Máquinas Virtuais
* [Por que não consigo ver determinadas VMs na folha Máquinas Virtuais do Azure que vejo no Azure DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs)
* [Qual é a diferença entre imagens e fórmulas personalizadas?](#what-is-the-difference-between-custom-images-and-formulas)
* [Como criar várias VMs do mesmo modelo simultaneamente?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
* [Como mover minhas VMs do Azure existentes para meu laboratório do Azure DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab)
* [Posso anexar vários discos às minhas VMs?](#can-i-attach-multiple-disks-to-my-vms)
* [Se eu quiser usar uma imagem do sistema operacional Windows para o meu teste, preciso comprar uma assinatura do MSDN?](#if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription)
* [Como automatizar o processo de carregamento de arquivos VHD para criar imagens personalizadas?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
* [Como é possível automatizar o processo de exclusão de todas as VMs em meu laboratório?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)

## <a name="artifacts"></a>Artefatos
* [O que são os artefatos?](#what-are-artifacts)

## <a name="lab-configuration"></a>Configuração do laboratório
* [Como criar um laboratório de um modelo do Azure Resource Manager?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template)
* [Por que as minhas VMs são criadas em grupos de recursos diferentes com nomes arbitrários? Posso renomear ou modificar esses grupos de recursos?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
* [Quantos laboratórios posso criar na mesma assinatura?](#how-many-labs-can-i-create-under-the-same-subscription)
* [Quantas VMs posso criar por laboratório?](#how-many-vms-can-i-create-per-lab)
* [Como faço para compartilhar um link direto para o meu laboratório?](#how-do-i-share-a-direct-link-to-my-lab)
* [O que é uma conta da Microsoft?](#what-is-a-microsoft-account)

## <a name="troubleshooting"></a>Solucionar problemas
* [Meu artefato falhou durante a criação da VM. Como solucionar isso?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
* [Por que a minha rede virtual existente não está salvando corretamente?](#why-isnt-my-existing-virtual-network-saving-properly)
* [Por que recebo um erro "Recurso pai não encontrado" ao provisionar do PowerShell?](#why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell)  
* [Onde posso encontrar mais informações sobre erros se há falha na implantação de uma VM?](#where-can-i-find-more-error-information-if-a-vm-deployment-fails)  

### <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Caso sua pergunta não esteja listada aqui, fale conosco e nós o ajudaremos a encontrar uma resposta.

* Poste uma pergunta sobre este artigo no [Thread do Disqus](#comments) no final desta seção de perguntas frequentes e entre em contato com a equipe do Cache do Azure e outros membros da comunidade.
* Para atingir um público mais amplo, poste uma pergunta no [Fórum do Azure DevTest Labs no MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)e entre em contato com a equipe do Azure DevTest Labs e outros membros da comunidade.
* Para fazer uma solicitação de recurso, envie solicitações e ideias para o [User Voice do Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Por que devo usar o Azure DevTest Labs?
O Azure DevTest Labs pode economizar dinheiro e tempo da equipe. Os desenvolvedores podem criar seus próprios ambientes usando várias bases diferentes e usar artefatos para implantar e configurar aplicativos rapidamente. Usando fórmulas e imagens personalizadas, as máquinas virtuais pode ser salvas como modelos e reproduzidas facilmente. Além disso, os laboratórios oferecem várias políticas configuráveis que permitem aos administradores do laboratório reduzir o desperdício e gerenciar os ambientes da equipe. Essas políticas incluem desligamento automático, limite de custo, máximo de VMs por usuário e tamanhos máximos de VM. Para obter uma explicação mais detalhada sobre o Azure DevTest Labs, leia a [visão geral](devtest-lab-overview.md) ou assista ao [vídeo introdutório](/documentation/videos/videos/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>O que significa "autoatendimento sem preocupação"?
"Autoatendimento sem preocupação" significa que os desenvolvedores e testadores podem criar seus próprios ambientes conforme a necessidade, e os administradores têm a segurança de saber que o Azure DevTest Labs ajudará a minimizar o desperdício e a controlar os custos. Os administradores podem especificar quais tamanhos de VM são permitidos, o número máximo de VMs e quando as VMs são inicializadas e desligadas. O Azure DevTest Labs também torna mais fácil monitorar os custos e definir alertas para se saber como os recursos do laboratório estão sendo usados.

### <a name="how-can-i-use-azure-devtest-labs"></a>Como usar o Azure DevTest Labs?
O Azure DevTest Labs será útil sempre que você precisar de ambientes de desenvolvimento ou teste e desejar reproduzi-los rapidamente e/ou gerenciá-los com as políticas de economia de custo.

Aqui estão alguns cenários em que nossos clientes usam o Azure DevTest Labs:

* Gerenciando ambientes de desenvolvimento e teste em um único lugar, usando políticas para reduzir custos e imagens personalizadas para compartilhar compilações com a equipe.
* Desenvolvendo um aplicativo usando imagens personalizadas para salvar o estado do disco em todas as fases de desenvolvimento.
* Controlando o custo em relação ao andamento.
* Criando ambientes de teste em massa para testes de garantia de qualidade.
* Usando fórmulas e artefatos para configurar e reproduzir um aplicativo em vários ambientes rapidamente.
* Distribuindo as VMs para hackathons (trabalho colaborativo de desenvolvimento ou teste) e desprovisionando-as quando o evento termina.

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Como eu sou cobrado pelo Azure DevTest Labs?
O Azure DevTest Labs é um serviço gratuito, o que significa que a criação e a configuração de políticas, modelos e artefatos são gratuitas. Você paga apenas pelos recursos do Azure usados em seus laboratórios, como máquinas virtuais, contas de armazenamento e redes virtuais. Para saber mais sobre o custo de recursos de laboratório, leia sobre o [preço do Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Quais são os diferentes níveis de segurança do Azure DevTest Labs?
O acesso de segurança é determinado pelo [RBAC (controle de acesso baseado em função) do Azure](../active-directory/role-based-access-built-in-roles.md). Para entender como funciona o acesso, é importante entender as diferenças entre um escopo, uma permissão e uma função, conforme definido pelo RBAC.

* **Permissão** – uma permissão é um acesso definido a uma ação específica. Por exemplo, uma permissão pode ser de acesso de leitura a todas as máquinas virtuais.
* **Função** – uma função é um conjunto de permissões que podem ser agrupadas e atribuídas a um usuário. Por exemplo, um "proprietário da assinatura" tem acesso a todos os recursos dentro de uma assinatura.
* **Escopo** – um escopo é um nível dentro da hierarquia do recurso do Azure. Por exemplo, um escopo pode ser um grupo de recursos, um único laboratório ou toda a assinatura.

Dentro do escopo do Azure DevTest Labs, há dois tipos de funções para definir permissões de usuário: usuário de laboratório e proprietário de laboratório.

* **Proprietário de laboratório** – um proprietário de laboratório tem acesso a qualquer recurso no laboratório. Sendo assim, ele pode modificar políticas, ler e gravar quaisquer VMs, alterar a rede virtual e assim por diante.
* **Usuário de laboratório** – um usuário de laboratório pode exibir todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas não pode modificar políticas ou VMs criadas por outros usuários. Também é possível criar funções personalizadas no Azure DevTest Labs, e você pode aprender a fazer isso neste artigo, [Conceder permissões de usuário para políticas específicas do laboratório](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Como os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, ele recebe essas permissões automaticamente em cada escopo de nível inferior que está englobado. Por exemplo, se a função de proprietário da assinatura é atribuída a um usuário, ele tem acesso a todos os recursos em uma assinatura. Esses recursos incluem todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Portanto, um proprietário de assinatura herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdadeiro. O proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de assinatura. Portanto, um proprietário de laboratório não pode ver máquinas virtuais, redes virtuais ou recursos que estão fora do laboratório.

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Como criar uma função para permitir que os usuários executem uma tarefa específica?
Um artigo abrangente sobre como criar funções personalizadas e atribuir permissões a essa função pode ser encontrado aqui. Eis um exemplo de um script que cria a função "Usuário avançado do DevTest Labs", que tem permissão para inicializar e desligar todas as máquinas virtuais do laboratório:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advance User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  

### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>O Azure DevTest Labs se integra à minha cadeia de ferramentas CI/CD?
Se você está usando o VSTS, há uma [extensão Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que permite automatizar seu pipeline de lançamento no Azure DevTest Labs. Alguns dos usos dessa extensão incluem:

* Criando e implantando uma VM automaticamente e configurá-la com a compilação mais recente usando tarefas de Cópia de Arquivo do Azure ou VSTS do PowerShell.
* Capturando o estado de uma VM automaticamente após os testes para reproduzir um bug na mesma VM a fim de investigar melhor.
* Excluindo a VM no final do pipeline de lançamento quando ela não for mais necessária.

As postagens de blog a seguir fornecem orientações e informações sobre como usar a extensão do VSTS:

* [Azure DevTest Labs – extensão do VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Implantando uma nova VM em uma AzureDevTestLab existente do VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Usando o Release Management do VSTS para implantações contínuas no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outras cadeias de ferramentas CI/CD, todos os cenários mencionados anteriormente que podem ser obtidos por meio da extensão de tarefas do VSTS podem ser obtidos da mesma forma por meio da implantação de [modelos do Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) usando [cmdlets do Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [SDKs do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Você também pode usar [APIs REST para DevTest Labs](http://aka.ms/dtlrestapis) a fim de integrar sua cadeia de ferramentas.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Por que não consigo ver determinadas VMs na folha Máquinas Virtuais do Azure que vejo no Azure DevTest Labs?
Quando uma VM é criada no Azure DevTest Labs, é concedida a permissão para acessar essa VM. Você pode vê-la na folha do laboratório e na folha **Máquinas Virtuais** . Usuários na função do DevTest Labs podem ver todas as máquinas virtuais criadas no laboratório na folha **Todas as Máquinas Virtuais** . No entanto, usuários na função do DevTest Labs não recebem automaticamente acesso de leitura a recursos da VM criados por outros usuários. Portanto, as VMs não são exibidas na folha **Máquinas Virtuais** .

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Qual é a diferença entre imagens e fórmulas personalizadas?
Uma imagem personalizada é um VHD (disco rígido virtual) e uma fórmula é uma imagem que você pode definir com configurações adicionais que podem ser salvas e reproduzidas. Uma imagem personalizada pode ser preferível se você quer criar rapidamente vários ambientes com a mesma imagem básica e imutável. Uma fórmula pode ser melhor se você deseja reproduzir a configuração da VM com um tamanho específico, uma sub-rede/rede virtual ou bits mais recentes. Para uma explicação mais detalhada, confira o artigo [Comparando imagens personalizadas e fórmulas no DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como criar várias VMs do mesmo modelo simultaneamente?
Você pode usar a [extensão de tarefas VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [gerar um modelo do Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) durante a criação de uma VM e [implantar o modelo do Azure Resource Manager do Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Como mover minhas VMs do Azure existentes para meu laboratório do Azure DevTest Labs?
Siga as etapas a seguir para copiar suas VMs existentes em para o Azure DevTest Labs:

1. Copie o arquivo VHD da VM existente usando este [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)
2. [Crie a imagem personalizada](devtest-lab-create-template.md) em seu laboratório do Azure DevTest Labs.
3. Criar uma máquina virtual no laboratório com sua imagem personalizada

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Posso anexar vários discos às minhas VMs?
Há suporte para anexar vários discos a máquinas virtuais.  

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se eu quiser usar uma imagem do sistema operacional Windows para o meu teste, preciso comprar uma assinatura do MSDN?
Se você precisar usar imagens do sistema operacional cliente Windows (Windows 7 ou posterior) para o desenvolvimento ou teste no Azure, deverá:

- [Comprar uma assinatura do MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se você tiver um Contrato Enterprise, crie uma assinatura do Azure com a [oferta de Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos Azure para cada oferta do MSDN, consulte [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/).

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como automatizar o processo de carregamento de arquivos VHD para criar imagens personalizadas?
Há duas opções:

* [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) pode ser usado para copiar ou carregar arquivos VHD na conta de armazenamento associada ao laboratório.
* [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que é executado no Windows, no OSX e no Linux.   

Para localizar a conta de armazenamento de destino associada a seu laboratório, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Grupos de Recursos** no painel esquerdo.
3. Localize e selecione o grupo de recursos associado ao seu laboratório.
4. Na folha **Visão geral** , selecione uma das contas de armazenamento.
5. Selecione **Blobs**.
6. Procure os carregamentos na lista. Se não houver nenhum, retorne para a etapa 4 e tente outra conta de armazenamento.
7. Use a **URL** como seu destino no comando AzCopy.

### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como é possível automatizar o processo de exclusão de todas as VMs em meu laboratório?
Além de excluir as VMs do laboratório no portal do Azure, é possível excluir todas as VMs no laboratório usando um script do PowerShell. No exemplo a seguir, modifique os valores de parâmetro no comentário **Valores a serem alterados**. Você pode recuperar os valores `subscriptionId`, `labResourceGroup` e `labName` da folha do laboratório no portal do Azure.

    # Delete all the VMs in a lab

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>O que são os artefatos?
Artefatos são elementos personalizáveis que podem ser usados para implantar os bits mais recentes ou as ferramentas de desenvolvimento em uma VM. Eles estão anexados à sua VM durante a criação com apenas alguns cliques, e depois que a VM é provisionada, os artefatos implantam e configuram a VM. Há diversos artefatos preexistentes no nosso [repositório GitHub público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mas você pode [criar seus próprios artefatos](devtest-lab-artifact-author.md) facilmente.

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Como criar um laboratório de um modelo do Azure Resource Manager?
Nós fornecemos um [repositório GitHub dos modelos do laboratório do Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) que você pode implantar como são ou modificar para criar modelos personalizados para seus laboratórios. Cada um desses modelos tem um link em que você pode clicar para implantar o laboratório no estado em que se encontra sob sua própria assinatura do Azure, ou você pode personalizar o modelo e [implantar usando o PowerShell ou CLI do Azure](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Por que as minhas VMs são criadas em grupos de recursos diferentes com nomes arbitrários? Posso renomear ou modificar esses grupos de recursos?
Grupos de recursos são criados dessa maneira para que o Azure DevTest Labs gerencie as permissões e acessos de usuário às máquinas virtuais. Embora você possa mover a VM para outro grupo de recursos com o nome desejado, não é recomendável fazer isso. Estamos trabalhando na melhoria dessa experiência para proporcionar mais flexibilidade.   

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quantos laboratórios posso criar na mesma assinatura?
Não há nenhum limite específico quanto ao número de laboratórios que podem ser criados por assinatura. No entanto, os recursos usados são limitados por assinatura. Você pode ler sobre os [limites e cotas impostas às assinaturas do Azure](../azure-subscription-service-limits.md) e sobre [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-many-vms-can-i-create-per-lab"></a>Quantas VMs posso criar por laboratório?
Não há nenhum limite específico quanto ao número de VMs que podem ser criadas por laboratório. No entanto, os recursos usados são limitados por assinatura (núcleos de VMs por exemplo, IPs públicos, etc.). Você pode ler sobre os [limites e cotas impostas às assinaturas do Azure](../azure-subscription-service-limits.md) e sobre [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como faço para compartilhar um link direto para o meu laboratório?
Para compartilhar um link direto para os usuários do laboratório, realize o seguinte procedimento:

1. Navegue até o laboratório no portal do Azure.
2. Copie a URL do laboratório do navegador e compartilhe-a com os usuários do laboratório.

> [!NOTE]
> Se os usuários do laboratório forem usuários externos com uma [conta da Microsoft](#what-is-a-microsoft-account) e não pertencerem ao Active Directory de sua empresa, talvez eles recebam um erro ao navegar para o link fornecido. Se receberem um erro, instrua-os a clicar no próprio nome no canto superior direito do portal do Azure e selecionar o diretório em que existe o laboratório na seção **Diretório** do menu.
>
>

### <a name="what-is-a-microsoft-account"></a>O que é uma conta da Microsoft?
Uma conta da Microsoft é o que você utiliza para quase tudo o que faz com os serviços e dispositivos da Microsoft. É um endereço de email e a senha que você usa para entrar no Skype, Outlook.com, OneDrive, Windows Phone e Xbox LIVE – e isso significa que seus arquivos, fotos, contatos e configurações podem acompanhar você em qualquer dispositivo.

> [!NOTE]
> A conta da Microsoft usada será chamada de "Windows Live ID".
>
>

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefato falhou durante a criação da VM. Como solucionar isso?
Consulte a postagem no blog [Como solucionar Artefatos com falha no Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) – escrita por um de nossos MVPs – para saber como obter logs sobre o artefato com falha.

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que a minha rede virtual existente não está salvando corretamente?
Uma das possibilidades é que o nome da rede virtual contém pontos. Nesse caso, tente remover os pontos ou substituí-los por hifens e tente salvar a rede virtual novamente.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell"></a>Por que recebo um erro "Recurso pai não encontrado" ao provisionar uma VM do PowerShell?
Quando um recurso é pai de outro recurso, o recurso pai deve existir antes da criação do recurso filho. Se ele não existir, você receberá um erro **ParentResourceNotFound**. Se você não especificar uma dependência no recurso pai, o recurso filho poderá obter implantado antes do pai.

As VMs são recursos filhos em um laboratório em um grupo de recursos. Quando você usa modelos do Azure Resource Manager para implantar por meio do PowerShell, o nome do grupo de recursos fornecido no script do PowerShell deve ser o nome do grupo de recursos do laboratório. Para obter mais informações, consulte [Solução de erros comuns de implantação do Azure ](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações sobre erros se há falha na implantação de uma VM?
Os erros de implantação de VM são capturados nos logs de atividades. É possível encontrar os logs de atividades de VMs do laboratório por meio dos **Logs de auditoria** ou do **Diagnóstico de máquina virtual** no menu de recursos da folha de VMs do laboratório (a folha é exibida após a seleção da VM na lista **Minhas máquinas virtuais**).

Às vezes, o erro de implantação ocorre antes do início da implantação da VM – por exemplo, quando o limite da assinatura de um recurso criado com a VM é excedido. Nesse caso, os detalhes do erro são capturados nos **Logs de atividades** em nível de laboratório, que podem ser encontrados na parte inferior das configurações **Configuração e políticas**. Para obter mais informações sobre como usar os logs de atividades no Azure, consulte [Exibir logs de atividades para auditar ações em recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit).

