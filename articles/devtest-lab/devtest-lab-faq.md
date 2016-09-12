<properties
	pageTitle="Perguntas frequentes do DevTest Labs | Microsoft Azure"
	description="Encontre respostas para perguntas comuns do DevTest Labs"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="tarcher"/>

# Perguntas frequentes do DevTest Labs

Este artigo responde algumas das perguntas mais comuns sobre o DevTest Labs.

## E se dúvida não foi respondida aqui?
Se sua pergunta não estiver listada aqui, fale conosco e nós ajudaremos a encontrar uma resposta.

- Poste uma pergunta sobre este artigo no [Thread do Disqus](#comments) no final desta seção de perguntas frequentes e entre em contato com a equipe do Cache do Azure e outros membros da comunidade.
- Para atingir um público mais amplo, poste uma pergunta no [Fórum do Azure DevTest Labs no MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs) e entre em contato com a equipe do Azure DevTest Labs e outros membros da comunidade.
- Para fazer uma solicitação de recurso, envie solicitações e ideias para o [User Voice do Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## Introdução
- [Como faço para começar a usar os DevTest Labs?](http://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## Geral
- [Por que devo usar os DevTest Labs?](#why-should-i-use-devtest-labs)
- [O que significa "sem preocupação, autoatendimento"?](#what-does-quotworry-free-self-servicequot-mean)
- [Como usar os DevTest Labs?](#how-can-i-use-devtest-labs)
- [Como serei cobrado por pelos DevTest Labs?](#how-will-i-be-charged-for-devtest-labs)
 
## Segurança 
- [Quais são os diferentes níveis de segurança nos DevTest Labs?](#what-are-the-different-security-levels-in-devtest-labs)
- [Como criar uma função específica para permitir que os usuários executem apenas uma única tarefa?](#how-do-i-create-a-specific-role-to-allow-users-to-perform-only-a-single-task)
 
## Integração e Automação de CI/CD 
 
- [Os DevTest Labs se integram à minha cadeia de ferramentas CI/CD?](#does-devtest-labs-integrate-with-my-cicd-toolchain)
 
## Máquinas Virtuais 
 
- [Por que não consigo ver determinadas VMs na folha Máquinas Virtuais do Azure que vejo no DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-devtest-labs)
- [Qual é a diferença entre imagens e fórmulas personalizadas?](#what-is-the-difference-between-custom-images-and-formulas)
- [Como criar várias VMs do mesmo modelo simultaneamente?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
- [Como mover minhas VMs do Azure existentes em meu laboratório do DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab)
- [Posso anexar vários discos às minhas VMs?](#can-i-attach-multiple-disks-to-my-vms)
- [Como automatizar o processo de carregamento de arquivos VHD para criar imagens personalizadas?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
 
## Artefatos 
 
- [O que são os artefatos?](#what-are-artifacts)
 
## Configuração do laboratório 
 
- [Como criar um laboratório de um modelo do Azure Resource Manager?](#how-do-i-create-a-lab-from-an-arm-template)
- [Por que todas as minhas VMs foram criadas em grupos de recursos diferentes com nomes arbitrários? Posso renomear ou modificar esses grupos de recursos?](#why-are-all-of-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
- [Quantos laboratórios posso criar na mesma assinatura?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Quantas VMs posso criar por laboratório?](#how-many-vms-can-i-create-per-lab)
 
## Solucionar problemas 
 
- [Meu artefato falhou durante a criação da VM. Como solucionar isso?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
- [Por que a minha rede virtual existente não está salvando corretamente?](#why-isnt-my-existing-virtual-network-saving-properly)

### Por que devo usar os DevTest Labs? 
O DevTest Labs pode economizar dinheiro e tempo da equipe. Os desenvolvedores podem criar seus próprios ambientes usando um número de bases diferentes e usar artefatos para implantar e configurar aplicativos rapidamente. Usando fórmulas e imagens personalizadas, as máquinas virtuais pode ser salvas como modelos e reproduzidas facilmente. Além disso, os laboratórios oferecem várias políticas configuráveis, como o desligamento automático, o limite de custo, o limite máximo de VMs por usuário e de tamanho de VMs, que permitem aos administradores de laboratório reduzir o desperdício e gerenciar ambientes da equipe. Para obter uma explicação mais detalhada sobre o DevTest Labs, leia a [Visão geral](devtest-lab-overview.md) ou confira o [vídeo introdutório](/documentation/videos/videos/what-is-azure-devtest-labs).

### O que significa "sem preocupação, autoatendimento"?
"Sem preocupação, autoatendimento” significa que os desenvolvedores e testadores podem criar seus próprios ambientes conforme a necessidade, e os administradores têm a segurança de saber que o DevTest Labs os ajudará a minimizar o desperdício e a controlar os custos. Os administradores podem especificar quais tamanhos de VM são permitidos, o número máximo de VMs e quando as VMs são inicializadas e desligadas. O DevTest Labs também torna mais fácil monitorar os custos e definir alertas para se saber como os recursos do laboratório estão sendo usados.

### Como usar os DevTest Labs? 
O DevTest Labs será útil sempre que você precisar de ambientes de desenvolvimento ou teste e desejar reproduzi-los rapidamente e/ou gerenciá-los com as políticas de economia de custo.

Aqui estão alguns cenários em que nossos clientes usam o DevTest Labs:

- Gerenciando ambientes de desenvolvimento e teste em um único lugar, usando políticas para reduzir custos e imagens personalizadas para compartilhar compilações com a equipe.
- Desenvolvendo um aplicativo usando imagens personalizadas para salvar o estado do disco em todas as fases de desenvolvimento.
- Controlando o custo em relação ao andamento.
- Criando ambientes de teste em massa para testes de garantia de qualidade.
- Usando fórmulas e artefatos para configurar e reproduzir um aplicativo em vários ambientes rapidamente.
- Distribuindo as VMs para hackathons (trabalho colaborativo de desenvolvimento ou teste) e desprovisionando-as quando o evento termina.

### Como serei cobrado por pelo DevTest Labs? 
O DevTest Labs é um serviço gratuito, o que significa que a criação e a configuração de políticas, modelos e artefatos são gratuitas. Você paga apenas pelos recursos do Azure usados em seus laboratórios, como máquinas virtuais, contas de armazenamento e redes virtuais. Para saber mais sobre o custo de recursos de laboratório, leia sobre o [preço do Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

### Quais são os diferentes níveis de segurança nos DevTest Labs?  
O acesso de segurança é determinado pelo [RBAC (controle de acesso baseado em função) do Azure](../active-directory/role-based-access-built-in-roles.md). Para entender como funciona o acesso, é importante entender as diferenças entre um escopo, uma permissão e uma função, conforme definido pelo RBAC. Uma permissão é um acesso definido para uma ação específica (por exemplo, acesso de leitura para todas as máquinas virtuais). Uma função é um conjunto de permissões que podem ser agrupados e atribuídos a um usuário (por exemplo, "proprietário da assinatura" tem acesso a todos os recursos em uma assinatura). Um escopo é um nível na hierarquia de recursos do Azure (por exemplo, um grupo de recursos, um único laboratório ou toda a assinatura).
 
Dentro do escopo do DevTest Labs, há dois tipos de funções para definir permissões de usuário: usuário de laboratório e proprietário de laboratório.

- Proprietário de laboratório - um proprietário de laboratório tem acesso a qualquer recurso no laboratório. Assim, eles podem modificar políticas, ler e gravar todas as máquinas virtuais, alterar a rede virtual e assim por diante.
- Usuário de laboratório - um usuário de laboratório pode exibir todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas não pode modificar políticas ou as máquinas virtuais criadas por outros usuários. Também é possível criar funções personalizadas no DevTest Labs, e você pode aprender como fazer isso neste artigo, [Conceder permissões de usuário para políticas específicas do laboratório](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).
 
Como os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, ele recebe essas permissões automaticamente em cada escopo de nível inferior que está englobado. Por exemplo, se um usuário recebe a função de proprietário da assinatura, ele tem acesso a todos os recursos em uma assinatura, o que inclui todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Portanto, um proprietário de assinatura herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdadeiro. O proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de assinatura. Portanto, um proprietário de laboratório não pode ver máquinas virtuais, redes virtuais ou recursos que estão fora do laboratório.

### Como criar uma função para permitir que os usuários executem uma tarefa específica?
Um artigo abrangente sobre como criar funções personalizadas e atribuir permissões a essa função pode ser encontrado aqui. Eis um exemplo de um script que cria a função "Usuário avançado do DevTest Labs", que tem permissão para inicializar e desligar todas as máquinas virtuais do laboratório:
 
	$policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User") 
	$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
	$policyRoleDef.Id = $null 
	$policyRoleDef.Name = "DevTest Labs Advance User" 
	$policyRoleDef.IsCustom = $true 
	$policyRoleDef.AssignableScopes.Clear() 
	$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
	$policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)  
 
### O DevTest Labs se integra à minha cadeia de ferramentas CI/CD? 
Se você estiver usando o VSTS, há uma [extensão Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que permite que você automatize seu pipeline de lançamento no DevTest Labs. Alguns dos usos dessa extensão incluem:

- Criando e implantando uma VM automaticamente e configurá-la com a compilação mais recente usando tarefas de Cópia de Arquivo do Azure ou VSTS do PowerShell.
- Capturando o estado de uma VM automaticamente após os testes para reproduzir um bug na mesma VM a fim de investigar melhor.
- Excluindo a VM no final do pipeline de lançamento quando ela não for mais necessária.

As postagens de blog a seguir fornecem orientações e informações sobre como usar a extensão do VSTS:
 
- [Azure DevTest Labs – extensão do VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
- [Implantando uma nova VM em uma AzureDevTestLab existente do VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Usando o Release Management do VSTS para implantações contínuas no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)
 
Para outras cadeias de ferramentas CI/CD, todos os cenários mencionados anteriormente que podem ser obtidos por meio da extensão de tarefas do VSTS podem ser obtidos da mesma forma por meio da implantação de [modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) usando [cmdlets do Azure PowerShell](../resource-group-template-deploy.md) e [SDKs do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Você também pode usar [APIs REST para DevTest Labs](http://aka.ms/dtlrestapis) a fim de integrar sua cadeia de ferramentas.

### Por que não consigo ver determinadas VMs na folha Máquinas Virtuais do Azure que vejo no DevTest Labs?
Quando uma máquina virtual é criada no DevTest Labs, uma permissão é dada para acessar a VM e você poderá exibi-las na folha de laboratórios e na folha **Máquinas Virtuais**. Se você é um usuário DevTest Labs, isso permite que você veja todas as máquinas virtuais criadas no laboratório na folha **Todas as Máquinas Virtuais**. No entanto, como um usuário DevTest Labs, você não recebe acesso de leitura aos recursos de VM que outros criaram automaticamente e, portanto, eles não serão exibidos na folha **Máquinas Virtuais**.

### Qual é a diferença entre imagens e fórmulas personalizadas? 
Uma imagem personalizada é um VHD (disco rígido virtual) e uma fórmula é uma imagem que você pode definir com configurações adicionais que podem ser salvas e reproduzidas. Uma imagem personalizada pode ser preferível se você quer criar rapidamente vários ambientes com a mesma imagem básica e imutável. Uma fórmula pode ser melhor se você deseja reproduzir a configuração da VM com um tamanho específico, uma sub-rede/rede virtual ou bits mais recentes. Para uma explicação mais detalhada, confira o artigo [Comparando imagens personalizadas e fórmulas no DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).
 
### Como criar várias VMs do mesmo modelo simultaneamente? 
Você pode usar a [extensão Tarefas VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [gerar um modelo do Azure Resource Manager](devtest-lab-add-vm-with-artifacts.md#save-arm-template) durante a criação de uma máquina virtual e [implantar o modelo do Azure Resource Manager do Windows PowerShell](../resource-group-template-deploy.md).
 
### Como mover minhas VMs do Azure existentes em meu laboratório do DevTest Labs? 
Podemos projetar uma solução mover VMs para o DevTest Labs diretamente, mas, no momento, você pode copiar suas VMs existentes para o DevTest Labs da seguinte maneira:

1. Copie o arquivo VHD da VM existente usando este [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)
1. [Crie a imagem personalizada](devtest-lab-create-template.md) em seu laboratório do DevTest Labs.
1. Criar uma máquina virtual no laboratório com sua imagem personalizada
 
### Posso anexar vários discos às minhas VMs? 
Há suporte para anexar vários discos a máquinas virtuais.
 
### Como automatizar o processo de carregamento de arquivos VHD para criar imagens personalizadas? 
Há duas opções:

- O [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) pode ser usado para copiar ou carregar arquivos VHD na conta de armazenamento associada ao laboratório.
- O [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que é executado no Windows, no OSX e no Linux.
 
Para localizar a conta de armazenamento de destino associada a seu laboratório:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Grupos de Recursos** no painel esquerdo.
1. Localize e selecione o grupo de recursos associado ao seu laboratório.
1. Na folha **Visão geral**, selecione uma das contas de armazenamento.
1. Selecione **Blobs**.
1. Procure os carregamentos na lista. Se não houver nenhum, retorne para a etapa 4 e tente outra conta de armazenamento.
1. Use a **URL** como seu destino no comando AzCopy.

### O que são os artefatos? 
Artefatos são elementos personalizáveis que podem ser usados para implantar os bits mais recentes ou as ferramentas de desenvolvimento em uma VM. Eles estão anexados à sua VM durante a criação com apenas alguns cliques, e depois que a VM é provisionada, os artefatos implantam e configuram a VM. Há um número de artefatos preexistentes no nosso [repositório Github público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mas você pode [criar seus próprios artefatos](devtest-lab-artifact-author.md) facilmente.

### Como criar um laboratório de um modelo do Azure Resource Manager? 
Temos um [repositório Github de modelos do Azure Resource manager de laboratório](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates). Cada um desses modelos possui um link que você pode clicar para implantar o laboratório DevTest Labs em sua própria assinatura do Azure.
 
### Por que todas as minhas VMs foram criadas em grupos de recursos diferentes com nomes arbitrários? Posso renomear ou modificar esses grupos de recursos? 
Grupos de recursos são criados dessa maneira para que o DevTest Labs gerencie as permissões e acessos de usuário às máquinas virtuais. Embora você possa mover a VM para outro grupo de recursos com seu nome desejado, isso não é recomendável. Estamos trabalhando na melhoria dessa experiência para proporcionar mais flexibilidade.
 
### Quantos laboratórios posso criar na mesma assinatura? 
Não há nenhum limite específico do número de laboratórios que podem ser criados por assinatura, mas os recursos usados são limitados por assinatura. Você pode ler sobre os [limites e cotas impostas às assinaturas do Azure](../azure-subscription-service-limits.md) e sobre [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).
 
### Quantas VMs posso criar por laboratório? 
Não há nenhum limite específico de número de máquinas virtuais que podem ser criadas por laboratório, mas atualmente o laboratório tem suporte apenas para cerca de 40 VMs em execução ao mesmo tempo no armazenamento padrão e 25 VMs em execução simultânea no armazenamento premium. Atualmente, estamos trabalhando para aumentar essas limitações.
 
### Meu artefato falhou durante a criação da VM. Como solucionar isso? 
Consulte a postagem do blog [Como solucionar problemas de falha de Artefatos no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs), escrito por um dos nossos MVPs, para saber como obter logs sobre o artefato com falha.
 
### Por que a minha rede virtual existente não está salvando corretamente?  
Uma das possibilidades é que o nome da rede virtual contém pontos. Nesse caso, tente remover os pontos ou substituí-los por hifens e tente salvar a rede virtual novamente.

<!---HONumber=AcomDC_0831_2016-->