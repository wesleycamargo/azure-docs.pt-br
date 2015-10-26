<properties
   pageTitle="Entender as diferenças entre os modelos do Gerenciador de Recursos e da implantação clássica"
   description="Descreve as diferenças entre o modelo de implantação do Gerenciador de Recursos e o modelo de implantação clássica (ou do Gerenciamento de Serviços)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="tomfitz"/>

# Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica

O modelo de implantação do Gerenciador de Recursos fornece uma nova maneira de implantar e gerenciar os serviços que compõem seu aplicativo. Esse novo modelo contém diferenças importantes do modelo de implantação clássica, e os dois modelos não são totalmente compatíveis entre si. Para simplificar a implantação e o gerenciamento de recursos, a Microsoft recomenda usar o Gerenciador de Recursos para novos recursos e, se possível, implantar novamente os recursos existentes por meio do Gerenciador de Recursos.

Você também pode conhecer o modelo de implantação clássica como o modelo do Gerenciamento de Serviços.

Este tópico descreve as diferenças entre os dois modelos e alguns dos problemas que você pode encontrar ao fazer a transição do modelo clássico para o Gerenciador de Recursos. Ele fornece uma visão geral dos modelos, mas não aborda em detalhes as diferenças nos serviços individuais. Para obter mais detalhes sobre como fazer a transição dos recursos Computação, Armazenamento e Rede, consulte [Provedores de Computação, de Rede e de Armazenamento do Azure no Gerenciador de Recursos do Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Muitos recursos funcionam sem problemas no modelo clássico e no Gerenciador de Recursos. Esses recursos oferecem suporte completo ao Gerenciador de Recursos mesmo se criados no modelo clássico. Você pode fazer a transição para o Gerenciador de Recursos sem precisar de esforço extra e sem se preocupar.

No entanto, alguns provedores de recursos oferecem duas versões do recurso (uma para o clássico e outra para o Gerenciador de Recursos) devido às diferenças de arquitetura entre os modelos. Os provedores de recursos que fazem distinção entre os dois modelos são:

- Computação
- Armazenamento
- Rede

Para esses tipos de recurso, você deve saber qual versão está usando, pois as operações com suporte variam.

Para entender as diferenças de arquitetura entre os dois modelos, consulte [Arquitetura do Gerenciador de Recursos do Azure](virtual-machines/virtual-machines-azure-resource-manager-architecture.md).

## Características do Gerenciador de Recursos

Os recursos criados com o Gerenciador de Recursos compartilham as seguintes características:

- Criados por meio de um dos seguintes métodos:

  - O [portal de visualização](https://portal.azure.com/).

        ![preview portal](./media/resource-manager-deployment-model/preview-portal.png)

        Para os recursos Computação, Armazenamento e Rede, você tem a opção de usar a implantação Gerenciador de Recursos ou Clássica. Selecione **Gerenciador de Recursos**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Para versões do Azure PowerShell anteriores à visualização 1.0, os comandos são executados no modo **AzureResourceManager**.

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - Para visualização do Azure PowerShell 1.0, use a versão de comandos do Gerenciador de Recursos. Esses comandos têm o formato *verb-AzureRm*, conforme mostrado abaixo.

            PS C:\> Get-AzureRmResourceGroupDeployment

  - [API REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) para operações REST.
  - Comandos do CLI do Azure executados no modo **arm**.

            azure config mode arm

- O tipo de recurso não inclui **(classic)** no nome. A imagem abaixo mostra o tipo como **Conta de armazenamento**.

    ![aplicativo web](./media/resource-manager-deployment-model/resource-manager-type.png)

## Características da implantação clássica

Os recursos criados no modelo de implantação clássica compartilham as seguintes características:

- Criados por meio de um dos seguintes métodos:

  - [Portal do Azure](https://manage.windowsazure.com)

        ![Azure portal](./media/resource-manager-deployment-model/azure-portal.png)

        Or, the preview portal and you specify **Classic** deployment (for Compute, Storage, and Networking).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Para versões do Azure PowerShell anteriores à visualização 1.0, os comandos são executados no modo **AzureServiceManagement** (que é o modo padrão e, portanto, se você não alternar especificamente para AzureResourceManager, estará executando no modo AzureServiceManagement).

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - Para visualização do Azure PowerShell 1.0, use a versão de comandos do Gerenciamento de Serviços. Esses nomes de comandos **não têm** o formato *verb-AzureRm*, conforme mostrado abaixo.

            PS C:\> Get-AzureDeployment

  - [API REST do Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx) para operações REST.
  - Comandos CLI do Azure executados no modo **asm** ou padrão.
- O tipo de recurso inclui **(classic)** no nome. A imagem abaixo mostra o tipo como **Conta de armazenamento (classic)**.

    ![tipo clássico](./media/resource-manager-deployment-model/classic-type.png)

Você ainda pode usar o portal de visualização para gerenciar recursos que foram criados por meio da implantação clássica.

## Benefícios de usar o Gerenciador de Recursos e os grupos de recursos

O Gerenciador de Recursos adicionou o conceito do grupo de recursos. Cada recurso criado por meio do Gerenciador de Recursos existe dentro de um grupo de recursos. O modelo de implantação do Gerenciador de Recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os serviços da sua solução como um grupo, em vez de tratá-los individualmente.
- Você pode implantar o aplicativo repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente.
- Você pode usar modelos declarativos para definir sua implantação. 
- Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.
- Você pode aplicar o controle de acesso a todos os serviços no grupo de recursos, pois o RBAC (Controle de Acesso Baseado em Função) é integrado nativamente à plataforma de gerenciamento.
- Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.


Antes do Gerenciador de Recursos, cada recurso criado por meio da implantação clássica não existia dentro de um grupo de recursos. Quando o Gerenciador de Recursos foi adicionado, todos os recursos foram adicionados retroativamente aos grupos de recursos padrão. Se você criar um recurso por meio da implantação clássica agora, ele será criado automaticamente em um grupo de recursos padrão para esse serviço, mesmo que você não tenha especificado esse grupo de recursos na implantação. No entanto, existir apenas em um grupo de recursos não significa que o recurso tenha sido convertido no modelo do Gerenciador de Recursos. Para Máquinas Virtuais, Armazenamento e Redes Virtuais, se o recurso tiver sido criado por meio da implantação clássica, você deverá continuar operando nele de modo clássico.

É possível mover recursos para outro grupo de recursos, bem como adicionar novos recursos a um grupo existente. Desse modo, seu grupo de recursos pode conter uma mistura de recursos criados por meio da implantação clássica e do Gerenciador de Recursos. Essa combinação de recursos pode gerar resultados inesperados, pois os recursos não oferecem suporte às mesmas operações.

Ao usar modelos declarativos, você pode ser capaz de simplificar scripts para implantação. Em vez de tentar converter scripts existentes do Gerenciamento de Serviços para o Gerenciador de Recursos, considere a possibilidade de trabalhar novamente a estratégia de implantação para aproveitar a definição de infraestrutura e configuração no modelo.

## Usando marcas

As marcas permitem que você organize recursos de modo lógico. Somente os recursos criados por meio do Gerenciador de Recursos oferecem suporte a marcas. Não é possível aplicar marcas a recursos clássicos.

Para saber mais sobre como usar marcas no Gerenciador de Recursos, veja a seção [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).

## Operações com suporte para os modelos de implantação

Os recursos que você criou no modelo de implantação clássica não oferecem suporte a operações do Gerenciador de Recursos. Em alguns casos, um comando do Gerenciador de Recursos pode recuperar informações sobre um recurso criado por meio da implantação clássica ou pode executar tarefas administrativas, como mover um recurso clássico para outro grupo de recursos, mas esses casos não devem dar a impressão de que o tipo oferece suporte às operações do Gerenciador de Recursos. Por exemplo, suponha que você tenha um grupo de recursos que contenha máquinas virtuais que foram criadas com o Gerenciador de Recursos e o modelo clássico. Ao executar o seguinte comando do PowerShell, você verá todas as máquinas virtuais:

    PS C:\> Get-AzureRmResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

No entanto, se executar o comando Get-AzureVM, você obterá apenas as máquinas virtuais que foram criadas com o Gerenciador de Recursos.

    PS C:\> Get-AzureVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

De modo geral, você não deve esperar que os recursos criados por meio da implantação clássica funcione com os comandos do Gerenciador de Recursos.

Ao trabalhar com recursos criados por meio do Gerenciador de Recursos, você deverá usar as operações do Gerenciador de Recursos e não alternar de volta para as operações de Gerenciamento de Serviços.

## Considerações sobre máquinas virtuais

Há algumas considerações importantes ao trabalhar com máquinas virtuais.

- As máquinas virtuais implantadas com o modelo de implantação clássica não podem ser incluídas em uma rede virtual implantada com o Gerenciador de Recursos.
- As máquinas virtuais implantadas com o modelo de implantação do Gerenciador de Recursos devem ser incluídas em uma rede virtual.
- As máquinas virtuais implantadas com o modelo de implantação clássica não precisam ser incluídas em uma rede virtual.

Para obter uma lista de comandos equivalentes do CLI do Azure durante a transição da implantação clássica para o Gerenciador de Recursos, veja a seção [Comandos equivalentes do Gerenciador de Recursos e do Gerenciamento de Serviços para operações de VM](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md).

Para obter mais detalhes sobre como fazer a transição dos recursos Computação, Armazenamento e Rede, consulte [Provedores de Computação, de Rede e de Armazenamento do Azure no Gerenciador de Recursos do Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Para saber como conectar redes virtuais por meio de modelos diferentes de implantação, veja a seção [Conectando VNets clássicas a VNets novas](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Próximas etapas

- Para saber como criar modelos de implantação declarativa, veja a seção [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
- Para visualizar os comandos para implantar um modelo, veja [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

<!---HONumber=Oct15_HO3-->