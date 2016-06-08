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
   ms.date="05/13/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager vs. Implantação clássica: compreenda os modelos de implantação e o estado dos seus recursos

Neste tópico, você aprenderá sobre o Azure Resource Manager e os modelos de implantação clássicos, o estado dos seus recursos e porque eles foram implantados com um modelo ou outro. O modelo de implantação do Gerenciador de Recursos contém diferenças importantes do modelo de implantação clássica e os dois modelos não são totalmente compatíveis entre si. Para simplificar a implantação e o gerenciamento de recursos, a Microsoft recomenda usar o Gerenciador de Recursos para novos recursos e, se possível, implantar novamente os recursos existentes por meio do Gerenciador de Recursos.

Para a maioria dos recursos, você pode fazer a transição para o Gerenciador de Recursos sem problemas. No entanto, alguns provedores de recursos oferecem duas versões do recurso (uma para o clássico e outra para o Gerenciador de Recursos) devido às diferenças de arquitetura entre os modelos. Os provedores de recursos que fazem distinção entre os dois modelos são:

- **Computação** - Dá suporte às instâncias de máquinas virtuais e conjuntos de disponibilidade opcionais.
- **Armazenamento** - Dá suporte às contas de armazenamento necessárias que armazenam os VHDs para máquinas virtuais, incluindo o sistema operacional e os discos de dados adicionais.
- **Rede** - Dá suporte às NICs necessárias, endereços IP de máquinas virtuais e sub-redes em redes virtuais e balanceadores de carga opcionais, endereços IP de balanceador de carga e grupos de segurança de rede.

Para esses tipos de recurso, você deve saber qual versão está usando, pois as operações com suporte variam. Se você estiver pronto para migrar seus recursos da implantação clássica para a implantação do Gerenciador de Recursos, veja [Platform supported migration of IaaS resources from Classic to Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Migração de recursos de IaaS com suporte da plataforma do Clássico para o Azure Resource Manager).

Para entender o modelo que foi usado para implantar seus recursos, vamos examinar os dois modelos.

## Características do Gerenciador de Recursos

Os recursos criados com o Gerenciador de Recursos compartilham as seguintes características:

- Criados por meio de um dos seguintes métodos:

  - O [portal do Azure](https://portal.azure.com/)

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        Para os recursos Computação, Armazenamento e Rede, você tem a opção de usar a implantação Gerenciador de Recursos ou Clássica. Selecione **Gerenciador de Recursos**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Para o Azure PowerShell, use a versão de comandos do Gerenciador de Recursos. Esses comandos têm o formato *Verb-AzureRmNoun*, conforme mostrado abaixo.

            Get-AzureRmResourceGroupDeployment

  - [API REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) para operações REST.
  - Comandos do CLI do Azure executados no modo **arm**.

            azure config mode arm

- O tipo de recurso não inclui **(classic)** no nome. A imagem abaixo mostra o tipo como **Conta de armazenamento**.

    ![aplicativo web](./media/resource-manager-deployment-model/resource-manager-type.png)

O aplicativo exibido no diagrama a seguir mostra como os recursos implantados por meio do Gerenciador de Recursos estão contidos em um único grupo de recursos.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Além disso, há relações entre os recursos dentro de provedores de recursos:

- Uma máquina virtual depende de uma conta de armazenamento específica definida no SRP para armazenar seus discos no armazenamento de blob (obrigatório).
- Uma máquina virtual faz referência a uma NIC específica definida no NRP (obrigatório) e um conjunto de disponibilidade definido no CRP (opcional).
- Uma NIC faz referência ao endereço IP atribuído à máquina virtual (obrigatório), à sub-rede da rede virtual para a máquina virtual (obrigatório) e a um grupo de segurança de rede (opcional).
- Uma sub-rede em uma rede virtual faz referência a um grupo de segurança de rede (opcional).
- Uma instância do balanceador de carga faz referência ao pool de back-end dos endereços IP que incluem a NIC de uma máquina virtual (opcional) e faz referência a um endereço IP público ou privado de um balanceador de carga (opcional).

## Características da implantação clássica

Você também pode conhecer o modelo de implantação clássica como o modelo do Gerenciamento de Serviços.

No Gerenciamento de Serviços do Azure, os recursos de computação, armazenamento ou rede para hospedar máquinas virtuais são fornecidos por:

- Um serviço de nuvem necessário que atua como contêiner para hospedar máquinas virtuais (computação). Máquinas virtuais são fornecidas automaticamente com uma placa de interface de rede (NIC) e um endereço IP atribuído pelo Azure. Além disso, o serviço de nuvem contém uma instância do balanceador externo de carga, um endereço IP público e pontos de extremidade padrão para permitir o tráfego do PowerShell remoto e de área de trabalho remota para máquinas virtuais baseadas em Windows e tráfego Secure Shell (SSH) para máquinas virtuais baseadas em Linux.
- Uma conta de armazenamento necessária que armazena os VHDs em uma máquina virtual, incluindo sistema operacional e discos de dados temporários e adicionais (armazenamento).
- Uma rede virtual opcional que atua como um contêiner adicional no qual você pode criar uma estrutura de sub-redes e designar a sub-rede na qual a máquina virtual está localizada (rede).

Os recursos criados no modelo de implantação clássica compartilham as seguintes características:

- Criados por meio de um dos seguintes métodos:

  - [Portal clássico](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        Ou pelo portal de visualização, e depois você deve especificar a implantação **Clássica** (para Computação, Armazenamento e Rede).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Para o Azure PowerShell, use a versão de comandos do Gerenciamento de Serviço. Esses nomes de comandos têm o formato *Verb-AzureNoun*, conforme mostrado abaixo.

            Get-AzureDeployment

  - [API REST do Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx) para operações REST.
  - Comandos CLI do Azure executados no modo **asm** ou padrão.
- O tipo de recurso inclui **(classic)** no nome. A imagem abaixo mostra o tipo como **Conta de armazenamento (classic)**.

    ![tipo clássico](./media/resource-manager-deployment-model/classic-type.png)

Você ainda pode usar o portal do Azure para gerenciar recursos que foram criados por meio da implantação clássica.

Aqui estão os componentes e suas relações para o Gerenciamento de Serviços do Azure.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Benefícios de usar o Gerenciador de Recursos e os grupos de recursos

O Gerenciador de Recursos adicionou o conceito do grupo de recursos. Cada recurso criado por meio do Gerenciador de Recursos existe dentro de um grupo de recursos. O modelo de implantação do Gerenciador de Recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os serviços da sua solução como um grupo, em vez de tratá-los individualmente.
- Você pode implantar o aplicativo repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente.
- Você pode usar modelos declarativos para definir sua implantação.
- Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.
- Você pode aplicar o controle de acesso a todos os recursos no grupo de recursos, pois o RBAC (Controle de Acesso Baseado em Função) é integrado nativamente à plataforma de gerenciamento.
- Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.


Antes do Gerenciador de Recursos, cada recurso criado por meio da implantação clássica não existia dentro de um grupo de recursos. Quando o Gerenciador de Recursos foi adicionado, todos os recursos foram adicionados retroativamente aos grupos de recursos padrão. Se você criar um recurso por meio da implantação clássica agora, ele será criado automaticamente em um grupo de recursos padrão para esse serviço, mesmo que você não tenha especificado esse grupo de recursos na implantação. No entanto, existir apenas em um grupo de recursos não significa que o recurso tenha sido convertido no modelo do Gerenciador de Recursos. Para Máquinas Virtuais, Armazenamento e Redes Virtuais, se o recurso tiver sido criado por meio da implantação clássica, você deverá continuar operando nele de modo clássico.

É possível mover recursos para outro grupo de recursos, bem como adicionar novos recursos a um grupo existente. Desse modo, seu grupo de recursos pode conter uma mistura de recursos criados por meio da implantação clássica e do Gerenciador de Recursos. Essa combinação de recursos pode gerar resultados inesperados, pois os recursos não oferecem suporte às mesmas operações.

Ao usar modelos declarativos, você pode ser capaz de simplificar scripts para implantação. Em vez de tentar converter scripts existentes do Gerenciamento de Serviços para o Gerenciador de Recursos, considere a possibilidade de trabalhar novamente a estratégia de implantação para aproveitar a definição de infraestrutura e configuração no modelo.

## Usando marcas

As marcas permitem que você organize recursos de modo lógico. Somente os recursos criados por meio do Gerenciador de Recursos oferecem suporte a marcas. Não é possível aplicar marcas a recursos clássicos.

Para saber mais sobre como usar marcas no Gerenciador de Recursos, veja a seção [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).

## Operações com suporte para os modelos de implantação

Os recursos que você criou no modelo de implantação clássica não oferecem suporte a operações do Gerenciador de Recursos. Em alguns casos, um comando do Gerenciador de Recursos pode recuperar informações sobre um recurso criado por meio da implantação clássica ou pode executar tarefas administrativas, como mover um recurso clássico para outro grupo de recursos, mas esses casos não devem dar a impressão de que o tipo oferece suporte às operações do Gerenciador de Recursos. Por exemplo, suponhamos que você tenha um grupo de recursos que contenha uma Máquina Virtual que foi criada com implantação clássica. Se você executar o seguinte comando do PowerShell:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Ele retornará todas a Máquina Virtual:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

No entanto, o cmdlet **Get-AzureRmVM** retorna apenas as Máquinas Virtuais implantadas por meio do Gerenciador de Recursos. O comando a seguir não retorna a Máquina Virtual criada por meio da implantação clássica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

De modo geral, você não deve esperar que os recursos criados por meio da implantação clássica funcione com os comandos do Gerenciador de Recursos.

Ao trabalhar com recursos criados por meio do Gerenciador de Recursos, você deverá usar as operações do Gerenciador de Recursos e não alternar de volta para as operações de Gerenciamento de Serviços.

## Considerações sobre máquinas virtuais

Há algumas considerações importantes ao trabalhar com máquinas virtuais.

- As máquinas virtuais implantadas com o modelo de implantação clássica não podem ser incluídas em uma rede virtual implantada com o Gerenciador de Recursos.
- As máquinas virtuais implantadas com o modelo de implantação do Gerenciador de Recursos devem ser incluídas em uma rede virtual.
- As máquinas virtuais implantadas com o modelo de implantação clássica não precisam ser incluídas em uma rede virtual.

Se você estiver pronto para migrar seus recursos da implantação clássica para a implantação do Gerenciador de Recursos, veja [Platform supported migration of IaaS resources from Classic to Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Migração de recursos de IaaS com suporte da plataforma do Clássico para o Azure Resource Manager).

Para obter mais detalhes sobre como fazer a transição dos recursos Computação, Armazenamento e Rede, consulte [Provedores de Computação, de Rede e de Armazenamento do Azure no Gerenciador de Recursos do Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md).

Para saber como conectar redes virtuais de diferentes modelos de implantação, veja [Conectando Redes Virtuais clássicas a Redes Virtuais novas](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Próximas etapas

- Para ver um passo a passo da criação do modelo que define uma máquina virtual, uma conta de armazenamento e uma rede virtual, confira [Passo a passo do modelo do Resource Manager](resource-manager-template-walkthrough.md).
- Para saber mais sobre a estrutura de modelos do Resource Manager, confira [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
- Para ver os comandos para implantar um modelo, veja [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0525_2016-->