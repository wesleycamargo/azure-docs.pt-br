<properties
   pageTitle="Gerenciador de Recursos e Implantação clássica| Microsoft Azure"
   description="Descreve as diferenças entre o modelo de implantação do Gerenciador de Recursos e o modelo de implantação clássica (ou do Gerenciamento de Serviços)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager vs. Implantação clássica: compreenda os modelos de implantação e o estado dos seus recursos

Neste tópico, você aprenderá sobre o Azure Resource Manager e os modelos de implantação clássicos, o estado dos seus recursos e porque eles foram implantados com um modelo ou outro. O modelo de implantação do Gerenciador de Recursos contém diferenças importantes do modelo de implantação clássica e os dois modelos não são totalmente compatíveis entre si. Para simplificar a implantação e o gerenciamento de recursos, a Microsoft recomenda usar o Gerenciador de Recursos para novos recursos e, se possível, implantar novamente os recursos existentes por meio do Gerenciador de Recursos.

Se você for um usuário completamente novo do Resource Manager, convém primeiro examinar a terminologia definida no [Visão geral do Azure Resource Manager](resource-group-overview.md).

## História dos modelos de implantação

Originalmente, o Azure fornecia o modelo de implantação clássico. Nesse modelo, cada recurso existia independentemente; não havia uma maneira de agrupar recursos relacionados. Em vez disso, era necessário controlar manualmente quais recursos compunham sua solução ou aplicativo, e lembrar-se de gerenciá-los em uma abordagem coordenada. Para implantar uma solução, você precisava criar cada recurso individualmente por meio do portal de clássico, ou criar um script que implantava todos os recursos na ordem correta. Para excluir uma solução, você precisava excluir cada recurso individualmente. Não era possível aplicar e atualizar facilmente políticas de controle de acesso para recursos relacionados. Por fim, você não podia aplicar marcas aos recursos para rotulá-los com os termos que ajudariam a monitorar seus recursos e gerenciar a cobrança.

Em 2014, o Azure introduziu o Resource Manager, que adicionou o conceito de um grupo de recursos. Um grupo de recursos é um contêiner de recursos que compartilham um ciclo de vida comum. O modelo de implantação do Gerenciador de Recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os serviços da sua solução como um grupo, em vez de tratá-los individualmente.
- Você pode implantar a solução repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente.
- Você pode aplicar o controle de acesso a todos os recursos em seu grupo de recursos, e essas políticas são aplicadas automaticamente aos novos recursos adicionados ao grupo de recursos.
- Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.
- Você pode usar a notação JSON (JavaScript Object Notation) para definir a infraestrutura de sua solução. O arquivo JSON é conhecido como um modelo do Resource Manager.
- Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.

Quando o Gerenciador de Recursos foi adicionado, todos os recursos foram adicionados retroativamente aos grupos de recursos padrão. Se você criar um recurso por meio da implantação clássica agora, ele será criado automaticamente em um grupo de recursos padrão para esse serviço, mesmo que você não tenha especificado esse grupo de recursos na implantação. No entanto, existir apenas em um grupo de recursos não significa que o recurso tenha sido convertido no modelo do Gerenciador de Recursos. Vamos examinar como cada serviço lida com os modelos de implantação na próxima seção.

## Noções básicas do suporte para os modelos 

Ao decidir-se sobre qual modelo de implantação usar para seus recursos, há três cenários que você deve considerar:

1. O serviço oferece suporte ao Resource Manager e fornece um único tipo.
2. O serviço oferece suporte ao Resource Manager, mas fornece dois tipos - um para o Resource Manager e outro para o clássico. Isso se aplica somente a máquinas virtuais, contas de armazenamento e redes virtuais.
3. O serviço não oferece suporte ao Resource Manager.

Para descobrir se um serviço oferece ou não suporte ao Resource Manager, consulte [Provedores com suporte do Resource Manager](resource-manager-supported-services.md).

Se o serviço que você quer usar não der suporte ao Resource Manager, continue usando a implantação clássica.

Se o serviço oferecer suporte ao Resource Manager e **não** for uma máquina virtual, conta de armazenamento ou rede virtual, você poderá usar o Resource Manager sem qualquer complicação.

Para máquinas virtuais, contas de armazenamento e redes virtuais, se o recurso tiver sido criado por meio da implantação clássica, você deverá continuar operando nele no modo clássico. Se a máquina virtual, conta de armazenamento ou rede virtual tiver sido criada por meio da implantação do Resource Manager, você deve continuar usando operações do Resource Manager. Essa distinção pode ficar especialmente confusa quando sua assinatura contiver uma mistura de recursos criada por meio do Resource Manager e da implantação clássica. Essa combinação de recursos pode gerar resultados inesperados, pois os recursos não oferecem suporte às mesmas operações.

Em alguns casos, um comando do Gerenciador de Recursos pode recuperar informações sobre um recurso criado por meio da implantação clássica ou pode executar tarefas administrativas, como mover um recurso clássico para outro grupo de recursos, mas esses casos não devem dar a impressão de que o tipo oferece suporte às operações do Gerenciador de Recursos. Por exemplo, suponhamos que você tenha um grupo de recursos que contenha uma máquina virtual que foi criada com implantação clássica. Se você executar o seguinte comando do PowerShell no Resource Manager:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Ele retornará a máquina virtual:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

No entanto, o cmdlet do Resource Manager **Get-AzureRmVM** retorna apenas as máquinas virtuais implantadas por meio do Gerenciador de Recursos. O comando a seguir não retorna a máquina virtual criada por meio da implantação clássica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Há outras considerações importantes ao trabalhar com máquinas virtuais.

- As máquinas virtuais implantadas com o modelo de implantação clássica não podem ser incluídas em uma rede virtual implantada com o Gerenciador de Recursos.
- As máquinas virtuais implantadas com o modelo de implantação do Gerenciador de Recursos devem ser incluídas em uma rede virtual.
- As máquinas virtuais implantadas com o modelo de implantação clássica não precisam ser incluídas em uma rede virtual.

Para saber como conectar redes virtuais de diferentes modelos de implantação, veja [Conectando Redes Virtuais clássicas a Redes Virtuais novas](./virtual-network/virtual-networks-arm-asm-s2s.md).

Somente os recursos criados por meio do Gerenciador de Recursos oferecem suporte a marcas. Não é possível aplicar marcas a recursos clássicos. Para saber mais sobre como usar marcas no Gerenciador de Recursos, veja a seção [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).

## Características do Gerenciador de Recursos

Para ajudá-lo a entender os dois modelos, vamos analisar as características dos tipos de Resource Manager:

- Criado por meio do [Portal do Azure](https://portal.azure.com/).

     ![Portal do Azure](./media/resource-manager-deployment-model/portal.png)

     Para os recursos Computação, Armazenamento e Rede, você tem a opção de usar a implantação Gerenciador de Recursos ou Clássica. Selecione **Gerenciador de Recursos**.

     ![Implantação do Gerenciador de Recursos](./media/resource-manager-deployment-model/select-resource-manager.png)

- Criado com a versão para Resource Manager dos cmdlets do Azure PowerShell. Esses comandos têm o formato *Verb-AzureRmNoun*, conforme mostrado abaixo.

        New-AzureRmResourceGroupDeployment

- Criado por meio da [API REST do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx) para operações REST.

- Criado por meio de comandos da CLI do Azure executados no modo **arm**.

        azure config mode arm
        azure group deployment create 

- O tipo de recurso não inclui **(classic)** no nome. A imagem abaixo mostra o tipo como **Conta de armazenamento**.

    ![aplicativo web](./media/resource-manager-deployment-model/resource-manager-type.png)

O aplicativo exibido no diagrama a seguir mostra como os recursos implantados por meio do Gerenciador de Recursos estão contidos em um único grupo de recursos.

  ![Arquitetura do Resource Manager](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Além disso, há relações entre os recursos dentro de provedores de recursos:

- Uma máquina virtual depende de uma conta de armazenamento específica definida no SRP para armazenar seus discos no armazenamento de blob (obrigatório).
- Uma máquina virtual faz referência a uma NIC específica definida no NRP (obrigatório) e um conjunto de disponibilidade definido no CRP (opcional).
- Uma NIC faz referência ao endereço IP atribuído à máquina virtual (obrigatório), à sub-rede da rede virtual para a máquina virtual (obrigatório) e a um grupo de segurança de rede (opcional).
- Uma sub-rede em uma rede virtual faz referência a um grupo de segurança de rede (opcional).
- Uma instância do balanceador de carga faz referência ao pool de back-end dos endereços IP que incluem a NIC de uma máquina virtual (opcional) e faz referência a um endereço IP público ou privado de um balanceador de carga (opcional).

## Características da implantação clássica

Você também pode conhecer o modelo de implantação clássica como o modelo do Gerenciamento de Serviços.

Os recursos criados no modelo de implantação clássica compartilham as seguintes características:

- Criado por meio do [Portal Clássico](https://manage.windowsazure.com)

     ![Portal clássico](./media/resource-manager-deployment-model/classic-portal.png)

     Ou pelo portal do Azure, e depois você deve especificar a implantação **Clássica** (para Computação, Armazenamento e Rede).

     ![Implantação Clássica](./media/resource-manager-deployment-model/select-classic.png)

- Criado por meio da versão de Gerenciamento de Serviço dos cmdlets do Azure PowerShell. Esses nomes de comandos têm o formato *Verb-AzureNoun*, conforme mostrado abaixo.

        New-AzureVM 

- Criado por meio da [API REST do Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx) para operações REST.
- Criado por meio de comandos da CLI do Azure executados no modo **asm**.

        azure config mode asm
        azure vm create 

- O tipo de recurso inclui **(classic)** no nome. A imagem abaixo mostra o tipo como **Conta de armazenamento (classic)**.

    ![tipo clássico](./media/resource-manager-deployment-model/classic-type.png)

Você ainda pode usar o portal do Azure para gerenciar recursos que foram criados por meio da implantação clássica.

No Gerenciamento de Serviços do Azure, os recursos de computação, armazenamento ou rede para hospedar máquinas virtuais são fornecidos por:

- Um serviço de nuvem necessário que atua como contêiner para hospedar máquinas virtuais (computação). Máquinas virtuais são fornecidas automaticamente com uma placa de interface de rede (NIC) e um endereço IP atribuído pelo Azure. Além disso, o serviço de nuvem contém uma instância do balanceador externo de carga, um endereço IP público e pontos de extremidade padrão para permitir o tráfego do PowerShell remoto e de área de trabalho remota para máquinas virtuais baseadas em Windows e tráfego Secure Shell (SSH) para máquinas virtuais baseadas em Linux.
- Uma conta de armazenamento necessária que armazena os VHDs em uma máquina virtual, incluindo sistema operacional e discos de dados temporários e adicionais (armazenamento).
- Uma rede virtual opcional que atua como um contêiner adicional no qual você pode criar uma estrutura de sub-redes e designar a sub-rede na qual a máquina virtual está localizada (rede).

Aqui estão os componentes e suas relações para o Gerenciamento de Serviços do Azure.

  ![arquitetura clássica](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Migrar do clássico para o Resource Manager

Se você estiver pronto para migrar seus recursos da implantação clássica para a implantação do Resource Manager, consulte:

1. [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Platform supported migration of IaaS resources from Classic to Azure Resource Manager (Migração de recursos de IaaS com suporte da plataforma do Clássico para o Azure Resource Manager)](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migrar recursos de IaaS do modelo clássico para o Azure Resource Manager usando a CLI do Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## Próximas etapas

- Para ver um passo a passo da criação do modelo que define uma máquina virtual, uma conta de armazenamento e uma rede virtual, confira [Passo a passo do modelo do Resource Manager](resource-manager-template-walkthrough.md).
- Para saber mais sobre a estrutura de modelos do Resource Manager, confira [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
- Para ver os comandos para implantar um modelo, veja [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0803_2016-->