---
title: Criar um conjunto de disponibilidade de VM no Azure | Microsoft Docs
description: "Saiba como criar um conjunto de disponibilidade gerenciado ou não gerenciado para suas máquinas virtuais usando o Azure PowerShell ou o portal no modelo de implantação do Resource Manager."
keywords: conjunto de disponibilidade
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a3db8659-ace8-4e78-8b8c-1e75c04c042c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e1933c5bf65f3e28d952213317413d0f65309c5b
ms.lasthandoff: 03/31/2017

---
# <a name="increase-vm-availability-by-creating-an-azure-availability-set"></a>Aumentar a disponibilidade da VM criando um conjunto de disponibilidade do Azure 
Os conjuntos de disponibilidade fornecem redundância ao seu aplicativo. Recomendamos o agrupamento de uma ou mais máquinas virtuais em um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual estará disponível e atenderá os 99,95% SLA do Azure. Para saber mais, confira [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> A VM deve ser criada no mesmo grupo de recursos que o grupo de disponibilidade.
> 

Se você quiser que sua VM faça parte de um conjunto de disponibilidade, crie o conjunto de disponibilidade primeiro ou durante a criação da VM no conjunto. Se a VM for usar o Managed Disks, o conjunto de disponibilidade deverá ser criado como um conjunto de disponibilidade gerenciado.

Para obter mais informações sobre como criar e usar conjuntos de disponibilidade, confira [Gerenciar a disponibilidade de máquinas virtuais](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Usar o portal para criar um conjunto de disponibilidade antes de criar suas VMs
1. No menu hub, clique em **Procurar** e selecione **Conjuntos de disponibilidade**.
2. Na **folha Conjuntos de disponibilidade**, clique em **Adicionar**.
   
    ![Captura de tela que mostra o botão de adição para criar um novo conjunto de disponibilidade.](./media/create-availability-set/add-availability-set.png)
3. Na folha **Criar conjunto de disponibilidade** , preencha as informações para o conjunto.
   
    ![Captura de tela que mostra as informações que você precisa inserir para criar o conjunto de disponibilidade.](./media/create-availability-set/create-availability-set.png)
   
   * **Nome** : o nome deve ter de 1 a 80 caracteres compostos por números, letras, pontos, sublinhados e traços. O primeiro caractere deve ser uma letra ou um número. O último caractere deve ser uma letra, um número ou um sublinhado.
   * **Domínios de falha** : os domínios de falha definem o grupo de máquinas virtuais que compartilham uma fonte de energia e um comutador de rede comuns. Por padrão, as VMs são separadas em até três domínios de falha e podem ser mudadas para entre 1 e 3.
   * **Domínios de atualização** - cinco domínios de atualização são atribuídos por padrão e isso pode ser definido como entre 1 e 20. Os domínios de atualização indicam grupos de máquinas virtuais e hardware físico subjacente que podem ser reinicializados ao mesmo tempo. Por exemplo, se especificarmos cinco domínios de atualização, quando mais do que cinco máquinas virtuais forem configuradas em um único conjunto de disponibilidade, a sexta máquina virtual será colocada no mesmo domínio de atualização que a primeira máquina virtual, a sétima no mesmo UD que a segunda e assim por diante. A ordem da reinicialização poderá não ser sequencial, mas apenas um domínio de atualização será reinicializado por vez.
   * **Assinatura** – selecione a assinatura a ser usada, caso tenha mais de uma.
   * **Grupo de recursos** : selecione um grupo de recursos existente, clicando na seta e selecionando um grupo de recursos no menu suspenso. Você também pode criar um novo grupo de recursos digitando um nome. O nome pode conter qualquer um dos seguintes caracteres: letras, números, pontos, traços, sublinhados e parênteses de abertura ou fechamento. O nome não pode terminar em um ponto. Todas as VMs no grupo de disponibilidade precisam ser criadas no mesmo grupo de recursos do conjunto de disponibilidade.
   * **Localização** : selecione uma localização na lista suspensa.
   * **Gerenciado** - selecione *Sim* para criar um conjunto de disponibilidade gerenciado para usar com VMs que usam o Managed Disks para armazenamento. Selecione **Não** se as VMs que estarão no conjunto usam discos não gerenciados em uma conta de armazenamento.
   
4. Quando terminar de inserir as informações, clique em **Criar**. 

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Usar o portal para criar uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo
Se estiver criando uma nova VM usando o portal, você também poderá criar um novo conjunto de disponibilidade para a nova VM enquanto cria a primeira VM no conjunto. Se você optar por usar o Managed Disks para sua VM, um conjunto de disponibilidade gerenciado será criado.

![Captura de tela que mostra o processo para criar um novo conjunto de disponibilidade ao criar a VM.](./media/create-availability-set/new-vm-avail-set.png)

## <a name="add-a-new-vm-to-an-existing-availability-set-in-the-portal"></a>Adicionar uma nova VM a um conjunto de disponibilidade existente no portal
Para cada VM adicional que você criar e que deva pertencer ao conjunto, certifique-se de criá-la no mesmo **grupo de recursos** e selecione o conjunto de disponibilidade existente na Etapa 3. 

![Captura de tela mostrando como selecionar um conjunto de disponibilidade existente para ser usado com sua VM.](./media/create-availability-set/add-vm-to-set.png)

## <a name="use-powershell-to-create-an-availability-set"></a>Usar o PowerShell para criar um conjunto de disponibilidade
Este exemplo cria um conjunto de disponibilidade chamado **myAvailabilitySet** no grupo de recursos **myResourceGroup** na localização **Oeste dos EUA**. Isso precisa ser feito antes de criar a primeira VM que estará no conjunto.

Antes de começar, verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Controle de versão do Azure PowerShell).


Se você estiver usando Managed Disks para suas VMs, digite:

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" -managed
```

Se você estiver usando suas próprias contas de armazenamento para suas VMs, digite:

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" 
```

Para obter mais informações, consulte [New-AzureRmAvailabilitySet](/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermavailabilityset).

## <a name="troubleshooting"></a>Solucionar problemas
* Ao criar uma VM, se o conjunto de disponibilidade desejado não estiver na lista suspensa no portal, você poderá ter realizado a criação dele em um grupo de recursos diferente. Se você não souber o grupo de recursos para sua seu conjunto de disponibilidade, vá até o menu hub e clique em Procurar > Conjuntos de disponibilidade para ver uma lista dos seus conjuntos de disponibilidade e a quais grupos de recursos pertencem.

## <a name="next-steps"></a>Próximas etapas
Adicione armazenamento adicional à sua VM incluindo um [disco de dados](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)adicional.


