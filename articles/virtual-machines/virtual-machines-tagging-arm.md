<properties
   pageTitle="Como Marcar uma VM | Microsoft Azure"
   description="Aprenda a Marcar uma Máquina Virtual do Azure criada no modelo de implantação do Gerenciador de Recursos."
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Como marcar uma Máquina Virtual no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Este artigo descreve as diferentes maneiras de marcar uma máquina virtual no Azure por meio do Gerenciador de Recursos do Azure. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure oferece suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas tem suporte apenas a recursos criados por meio do Gerenciador de Recursos do Azure.

## Marcando uma máquina virtual por meio de modelos

Primeiramente, vamos observar uma marcação por meio de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) coloca marcas nos seguintes recursos: Computação (Máquina Virtual), Armazenamento (Conta de Armazenamento) e Rede (Endereço IP Público, Rede Virtual e Interface de Rede).

Clique no botão **Implantar no Azure** no [link do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm). Você será direcionado para o [Portal do Azure](http://portal.azure.com/), onde poderá implantar esse modelo.

![Implantação simples com marcas](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

Esse modelo inclui as seguintes marcas: *Departamento*, *Aplicativo* e *Criado por*. Você pode adicionar/editar essas marcas diretamente no modelo se desejar diferentes nomes de marca.

![Marcas do Azure em um modelo](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

Como pode ser visto, as marcas são pares de chave/valor definidos, separados por dois-pontos (:). As marcas devem ser definidas neste formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Salve o arquivo de modelo quando terminar de editá-lo com as marcas de sua escolha.

Em seguida, na seção **Editar Parâmetros**, você pode preencher os valores de suas marcas.

![Editar marcas no Portal do Azure](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

Clique em **Criar** para implantar esse modelo com seus valores de marca.


## Marcando por meio do portal

Depois de criar seus recursos com marcas, você pode exibir, adicionar e excluir as marcas no portal.

Selecione o ícone de marcas para exibir suas marcas:

![Ícone de marcas no Portal do Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

Adicione uma nova marca por meio do portal definindo seu próprio par de chave/valor e salve-a.

![Adicionar nova marca no Portal do Azure](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

Agora, a nova marca deve aparecer na lista de marcas do seu recurso.

![Nova marca salva no Portal do Azure](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## Marcação com o PowerShell

Para criar, adicionar e excluir marcas pelo PowerShell, primeiramente, você precisa configurar o [ambiente do PowerShell com o Gerenciador de Recursos do Azure][]. Depois de concluir a instalação, você pode colocar marcas em recursos de Computação, Rede e Armazenamento na criação ou depois que o recurso for criado via PowerShell. Este artigo se concentra na exibição/edição de marcas colocadas nas Máquinas Virtuais.

Primeiramente, navegue para uma Máquina Virtual usando o cmdlet `Get-AzureVM`.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Se sua Máquina Virtual já contiver marcas, você verá todas elas no seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se desejar adicionar marcas por meio do PowerShell, você poderá usar o comando `Set-AzureResource`. Observe que, ao atualizar marcas pelo PowerShell, as marcas são atualizadas como um todo. Desse modo, se estiver adicionando uma marca a um recurso que já tenha marcas, você precisará incluir todas as marcas que deseja que sejam colocadas no recurso. Veja abaixo um exemplo de como adicionar mais marcas a um recurso usando cmdlets do PowerShell.

Este primeiro cmdlet define todas as marcas colocadas em *MyWindowsVM* para a variável *tags* usando as funções `Get-AzureResource` e `Tags`. Observe que o parâmetro `ApiVersion` é opcional; se não for especificado, a última versão de API do provedor de recursos é usada.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

O segundo comando exibe as marcas para a variável fornecida.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

O terceiro comando adiciona uma marca extra à variável *tags*. Observe o uso de **+=** para acrescentar o novo par de Chave/Valor à lista *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

O quarto comando define todas as marcas definidas na variável *tags* para o recurso determinado. Nesse caso, é MyWindowsVM.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

O quinto comando exibe todas as marcas no recurso. Como você pode ver, *Location* agora está definido como uma marca com *MyLocation* como valor.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Para saber mais sobre marcação usando o PowerShell, confira [Cmdlets de recursos do Azure][].


## Marcando com a CLI do Azure

A marcação também tem suporte de recursos que já foram criados por meio da CLI do Azure. Para começar, configure seu [ambiente da CLI do Azure][]. Faça logon na sua assinatura usando a CLI do Azure e alterne para o modo ARM. É possível exibir todas as propriedades de uma determinada Máquina Virtual, incluindo as marcas, usando este comando:

        azure vm show -g MyResourceGroup -n MyVM

Ao contrário do PowerShell, se você estiver adicionando marcas a um recurso que já contenha marcas, não será necessário especificar todas as marcas, antigas ou novas, antes de usar o comando `azure vm set`. Em vez disso, esse comando permite acrescentar uma marca ao recurso. Para adicionar uma nova marca de VM usando a CLI do Azure, use o comando `azure vm set` juntamente com o parâmetro de marca **-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para remover todas as marcas, use o parâmetro **–T** no comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Agora que aplicamos marcas aos nossos recursos por meio do PowerShell, da CLI do Azure e do Portal, vamos examinar os detalhes de uso para ver as marcas no portal de cobrança.


## Exibindo suas marcas nos detalhes de uso

As marcas colocadas nos recursos de Computação, Rede e Armazenamento por meio do Gerenciador de Recursos do Azure serão preenchidas nos detalhes de uso no [portal de cobrança](https://account.windowsazure.com/).

Clique em **Baixar detalhes de uso** para exibir os detalhes de uso da assinatura.

![Detalhes de uso no Portal do Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

Selecione o demonstrativo e os detalhes de uso da **Versão 2**:

![Detalhes de uso da versão de visualização 2 no Portal do Azure](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

Nos detalhes de uso, você pode ver todas as marcas na coluna **Marcas**:

![Coluna Marcas no Portal do Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

Ao analisar essas marcas juntamente com o uso, as organizações poderão adquirir novas percepções sobre os respectivos dados de consumo.


## Recursos adicionais

* [Visão Geral do Gerenciador de Recursos do Azure][]
* [Usando marcas para organizar os recursos do Azure][]
* [Noções básicas de sua fatura do Azure][]
* [Obtenha informações sobre o consumo de recursos do Microsoft Azure][]




[ambiente do PowerShell com o Gerenciador de Recursos do Azure]: ../powershell-azure-resource-manager.md
[Cmdlets de recursos do Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[ambiente da CLI do Azure]: ./xplat-cli-azure-resource-manager.md
[Visão Geral do Gerenciador de Recursos do Azure]: ../resource-group-overview.md
[Usando marcas para organizar os recursos do Azure]: ../resource-group-using-tags.md
[Noções básicas de sua fatura do Azure]: ../billing-understand-your-bill.md
[Obtenha informações sobre o consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_1210_2015-->