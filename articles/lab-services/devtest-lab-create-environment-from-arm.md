---
title: Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager | Microsoft Docs
description: Saiba como criar ambientes de várias VMs e recursos de PaaS no Azure DevTest Labs com base em um modelo do Azure Resource Manager
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: ebe5c65f701c0a1c7c02182800a35bfbeed5b0be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181377"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager

O [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) permite a você [adicionar uma VM de cada vez a um laboratório](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm) de maneira fácil. No entanto, se o ambiente contiver várias VMs, cada VM deverá ser criada individualmente. Para cenários como um aplicativo Web com várias camadas ou um farm do SharePoint, é necessário um mecanismo para permitir a criação de várias VMs em uma única etapa. Usando os modelos do Azure Resource Manager, agora você pode definir a infraestrutura e a configuração de sua solução do Azure e implantar repetidamente várias VMs em um estado consistente. Esse recurso oferece os seguintes benefícios:

- Os modelos do Azure Resource Manager são carregados diretamente de seu repositório de controle do código-fonte (GitHub ou Git do Azure DevOps Services).
- Uma vez configurados, os usuários podem criar um ambiente selecionando um modelo do Azure Resource Manager no portal do Azure, assim como fazem com outros tipos de [VM básica](./devtest-lab-comparing-vm-base-image-types.md).
- Os recursos de PaaS do Azure podem ser provisionados em um ambiente usando um modelo do Azure Resource Manager, além das VMs de IaaS.
- O custo dos ambientes pode ser controlado no laboratório, além das VMs individuais criadas por outros tipos de bases.
- Recursos de PaaS são criados e aparecerão no acompanhamento de custo; no entanto, desligamento automático VM não se aplica a recursos de PaaS.

Saiba mais sobre os muitos [benefícios de usar os modelos do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) para implantar, atualizar ou excluir todos os seus recursos de laboratório em uma única operação.

> [!NOTE]
> Quando você usa um modelo do Resource Manager como base para criar mais VMs do laboratório, há algumas diferenças a ter em mente se você estiver criando várias VMs ou VMs únicas. [Usar o modelo do Azure Resource Manager de uma máquina virtual](devtest-lab-use-resource-manager-template.md) explica essas diferenças mais detalhadamente.
>

## <a name="devtest-labs-public-environments"></a>Ambientes públicos do DevTest Labs
O Azure DevTest Labs tem um [repositório público de modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que pode ser usado para criar ambientes sem a necessidade de conectar uma origem do GitHub externa por conta própria. Esse repositório inclui modelos usados com frequência como os Aplicativos Web do Azure, Cluster do Service Fabric e ambiente de desenvolvimento do Farm do SharePoint. Esse recurso é semelhante ao repositório público de artefatos incluído em todos os laboratórios que você cria. O repositório de ambiente permite que você comece rapidamente com modelos de ambiente criados previamente com parâmetros de entrada mínimos para fornecer uma experiência de introdução simples aos recursos de PaaS nos laboratórios. Para obter mais informações, veja [Configurar e usar ambientes públicos no DevTest Labs](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Configurar seus próprios repositórios de modelo
Como uma das práticas recomendadas com a infraestrutura como código e a configuração como código, os modelos de ambiente devem ser gerenciados no controle de origem. O Azure DevTest Labs segue essa prática e carrega todos os modelos do Azure Resource Manager diretamente de seus repositórios GitHub ou Git do Azure DevOps Services. Como resultado, os modelos do Resource Manager podem ser usados em todo o ciclo de versão de inteiro, do ambiente de teste ao ambiente de produção.

Verifique os modelos criados pela equipe do DevTest Labs no [repositório GitHub público](https://github.com/Azure/azure-devtestlab/tree/master/Environments). Nesse repositório público, é possível exibir os modelos compartilhados por outras pessoas que você pode usar diretamente ou personalizá-los para atender às suas necessidades. Depois de criar o modelo, armazene-o neste repositório para compartilhá-lo com outras pessoas. Também é possível configurar seu próprio repositório Git com modelos que podem ser usados para configurar ambientes na nuvem. 

Há algumas regras a seguir para organizar seus modelos do Azure Resource Manager em um repositório:

- O arquivo de modelo mestre deve ser nomeado `azuredeploy.json`. 

    ![Principais arquivos de modelo do Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Se você quiser usar os valores do parâmetro definidos em um arquivo de parâmetro, o arquivo de parâmetro deve ser nomeado `azuredeploy.parameters.json`.
- Você pode usar os parâmetros `_artifactsLocation` e `_artifactsLocationSasToken` para construir o valor do URI parametersLink, permitindo ao DevTest Labs gerenciar automaticamente os modelos aninhados. Para obter mais informações, consulte [Como o Azure DevTest Labs facilita as implantações aninhadas de modelo do Resource Manager para ambientes de teste](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Os metadados podem ser definidos para especificar o nome de exibição do modelo e a descrição. Esses metadados devem estar em um arquivo denominado `metadata.json`. O arquivo de metadados de exemplo a seguir ilustra como especificar o nome de exibição e a descrição: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

As etapas a seguir irão orientá-lo na adição de um repositório ao seu laboratório usando o portal do Azure. 

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado.   
1. No painel **Visão geral** do laboratório, selecione **Configuração e Políticas**.

    ![Configuração e políticas](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Na lista de configurações **Configuração e Políticas**, selecione **Repositórios**. O painel **Repositórios** lista os repositórios que foram adicionados ao laboratório. Um repositório denominado `Public Repo` é gerado automaticamente para todos os laboratórios e conecta o [repositório DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) que contém vários artefatos da VM para usar.

    ![Repositório público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Selecione **Adicionar+** para adicionar seu repositório de modelos do Azure Resource Manager.
1. Quando o segundo painel **Repositórios** abrir, insira as informações necessárias da seguinte maneira:
    - **Nome** - insira o nome do repositório usado no laboratório.
    - **URL de clone do Git** – Insira a URL de clone HTTPS do GIT por meio do GitHub ou do Azure DevOps Services.  
    - **Ramificação** - insira o nome da ramificação para acessar suas definições de modelo do Azure Resource Manager. 
    - **Token de acesso pessoal** - o token de acesso pessoal é usado para acessar o repositório com segurança. Para obter o token do Azure DevOps Services, selecione **&lt;SeuNome > > Meu perfil > Segurança > Token de acesso público**. Para obter o token no GitHub, selecione seu avatar, em seguida, selecione **Configurações > Token de acesso público**. 
    - **Caminhos da pasta** - usando um dos dois campos de entrada, digite o caminho da pasta que começa com uma barra invertida - / - e é relativo ao URI de clone do Git para suas definições de artefato (primeiro campo de entrada) ou suas definições de modelo do Azure Resource Manager.   
    
        ![Repositório público](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Assim que todos os campos obrigatórios forem inseridos e passarem na validação, selecione **Salvar**.

A próxima seção orientará a criação de ambientes a partir de um modelo do Azure Resource Manager.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Criar um ambiente a partir de um modelo do Resource Manager usando o portal do Asure

Assim que um repositório de modelos do Azure Resource Manager for configurado no laboratório, os usuários do laboratório poderão criar um ambiente usando o portal do Azure com as seguintes etapas:

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado.   
1. No painel do laboratório, selecione **Adicionar+**.
1. O painel **Escolher uma base** exibe as imagens básicas que você pode usar com os modelos do Azure Resource Manager listados primeiro. Selecione o modelo desejado do Azure Resource Manager.

    ![Escolher uma base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. No painel **Adicionar**, insira o valor **Nome do ambiente**. O nome do ambiente é o exibido para os usuários no laboratório. Os campos de entrada restantes são definidos no modelo do Azure Resource Manager. Se os valores padrão forem definidos no modelo ou o arquivo `azuredeploy.parameter.json` estiver presente, os valores padrão serão exibidos nesses campos de entrada. Para os parâmetros do tipo *proteger a cadeia de caracteres*, você pode usar os segredos armazenados no Azure Key Vault. Para saber como salvar segredos em um cofre de chaves e usá-los ao criar recursos de laboratório, consulte [Store secrets in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md) (Armazenar segredos no Azure Key Vault).  

    ![Adicionar painel](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Há diversos valores de parâmetro que, mesmo se especificados, são exibidos como valores vazios. Portanto, se os usuários atribuírem esses valores a parâmetros em um modelo do Azure Resource Manager, DevTest Labs não exibe os valores. Em vez disso, os campos de entrada em branco são mostrados onde os usuários do laboratório devem inserir um valor ao criar o ambiente.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Selecione **Adicionar** para criar o ambiente. O ambiente inicia o provisionamento imediatamente com a exibição do status na lista **Minhas máquinas virtuais**. Um novo grupo de recursos é criado automaticamente pelo laboratório para provisionar todos os recursos definidos no modelo do Azure Resource Manager.
1. Assim que o ambiente for criado, selecione-o na lista **Minhas máquinas virtuais** para abrir o painel do grupo de recursos e procurar todos os recursos provisionados no ambiente.
    
    ![Lista Minhas máquinas virtuais](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Você também pode expandir o ambiente para exibir apenas a lista de VMs que estão provisionadas no ambiente.
    
    ![Lista Minhas máquinas virtuais](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Clique em qualquer um dos ambientes para exibir as ações disponíveis - como aplicar artefatos, anexar discos de dados, mudar a hora de finalização automática e muito mais.

    ![Ações do ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>Automatizar a implantação de ambientes
O Azure DevTest Labs fornece a capacidade de usar um [modelo de gerenciamento do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para criar um ambiente com um conjunto de recursos do laboratório. Esses ambientes podem conter todos os recursos do Azure que podem ser criados usando modelos do Resource Manager. Ambientes do DevTest Labs permitem que os usuários prontamente implantar infraestruturas complexas de forma consistente dentro dos limites do laboratório. Atualmente, a adição de um ambiente a um laboratório usando o portal do Azure é viável ao criá-lo uma vez, mas em um desenvolvimento ou uma situação de teste, onde várias criações ocorrerem, uma implantação automatizada permite uma experiência aprimorada.

Conclua as seguintes etapas na [configurar seus próprios repositórios de modelo](#configure-your-own-template-repositories) seção antes de continuar: 

1. Crie o modelo do Resource Manager que define os recursos que está sendo criados. 
2. Configure o modelo do Resource Manager no Git um repositório. 
3. Conecte-se o repositório Git ao laboratório. 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>Script do PowerShell para implantar o modelo do Resource Manager
Salve o script do PowerShell na próxima seção para o disco rígido (por exemplo: deployenv.ps1) e execute o script depois de especificar valores para SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (pasta) no repositório do Git, EnvironmentName.

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>Script de exemplo
Aqui está o script de exemplo para criar um ambiente em seu laboratório. Os comentários no script ajudarão-lo a entender melhor o script. 

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzureRmAccount

# Select the subscription that has the lab.  
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzureRmADUser -UserPrincipalName (Get-AzureRmContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzureRmResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzureRmResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzureRmResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzureRmResource command. 
New-AzureRmResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

Você também pode usar a CLI do Azure para implantar recursos com modelos do Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Somente um usuário com permissões de proprietário de laboratório pode criar VMs de um modelo do Resource Manager usando o Azure PowerShell. Se você quiser automatizar a criação de VMs usando um modelo do Resource Manager e tiver apenas permissões de usuário, você poderá usar o comando [**az lab vm create** na CLI](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Limitações gerais 

Considere estas limitações ao usar um modelo do Resource Manager no DevTest Labs:

- Qualquer modelo do Resource Manager que você criar não poderá se referir aos recursos existentes; ele só pode se referir a novos recursos. Além disso, se você tiver um modelo existente do Resourse Manager que normalmente implanta fora do DevTest Labs e ele contiver referências aos recursos existentes, ele não poderá ser usado no laboratório.

   A única exceção para isso é que você **pode** fazer referência a uma rede virtual existente. 

- Fórmulas não podem ser criadas a partir de VMs do laboratório que foram criadas a partir de um modelo do Resource Manager. 

- Imagens personalizadas não podem ser criadas a partir de VMs do laboratório que foram criadas a partir de um modelo do Resource Manager. 

- A maioria das políticas não são avaliadas quando você implanta modelos do Resource Manager.

   Por exemplo, você pode ter uma política de laboratório especificando que um usuário pode criar apenas cinco VMs. No entanto, um usuário pode implantar um modelo do Resource Manager que crie dezenas de VMs. As políticas que não são avaliadas incluem:

   - Número de VMs por usuário
   - Número de VMs premium por usuário do laboratório
   - Número de discos premium por usuário do laboratório


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Configurar direitos de acesso de grupo de recursos de ambiente para os usuários do laboratório

Usuários de laboratório podem implantar um modelo do Resource Manager. Mas, por padrão, eles têm direitos de acesso de Leitor, o que significa que eles não podem alterar os recursos em um grupo de recursos do ambiente. Por exemplo, eles não podem interromper ou iniciar seus recursos.

Siga estas etapas para conceder direitos de acesso do Colaborador de seus usuários de laboratório. Em seguida, ao implantar um modelo do Resource Manager, eles poderão editar os recursos nesse ambiente. 


1. No painel **Visão geral** do seu laboratório, selecione **Configuração e políticas**.
1. Selecione **Configurações do laboratório**.
1. No painel de Configurações do Laboratório, selecione **Colaborador** para conceder permissões de gravação para os usuários do laboratório.

    ![Configurar direitos de acesso de usuário de laboratório](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
* Após a criação da VM, você pode conectar a VM selecionando **Conectar** no painel de gerenciamento da VM.
* Exiba e gerencie recursos em um ambiente selecionando-o na lista **Minhas máquinas virtuais** no seu laboratório. 
* Explore os [modelos do Azure Resource Manager na galeria de modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
