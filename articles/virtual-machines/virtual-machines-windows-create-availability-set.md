<properties
	pageTitle="Criar um conjunto de disponibilidade de VM | Microsoft Azure"
	description="Saiba como criar um conjunto de disponibilidade para suas máquinas virtuais usando o Portal do Azure ou o PowerShell usando o modelo de implantação do Resource Manager."
	keywords="conjunto de disponibilidade"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>


# Criar um conjunto de disponibilidade 

Ao usar o portal, se quiser que sua VM faça parte de um conjunto de disponibilidade, você precisará criar o conjunto de disponibilidade primeiro.

Para obter mais informações sobre como criar e usar conjuntos de disponibilidade, confira [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md).


## Usar o portal para criar um conjunto de disponibilidade antes de criar suas VMs

1. No menu hub, clique em **Procurar** e selecione **Conjuntos de disponibilidade**.

2. Na **folha Conjuntos de disponibilidade**, clique em **Adicionar**.

	![Captura de tela que mostra o botão de adição para criar um novo conjunto de disponibilidade.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. Na folha **Criar conjunto de disponibilidade**, preencha as informações para o conjunto.

	![Captura de tela que mostra as informações que você precisa inserir para criar o conjunto de disponibilidade.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

	- **Nome**: o nome deve ter de 1 a 80 caracteres compostos por números, letras, pontos, sublinhados e traços. O primeiro caractere deve ser uma letra ou um número. O último caractere deve ser uma letra, um número ou um sublinhado.
	- **Domínios de falha**: os domínios de falha definem o grupo de máquinas virtuais que compartilham uma fonte de energia e um comutador de rede comuns. Por padrão, as VMs são separadas em até três domínios de falha e podem ser mudadas para entre 1 e 3.
	- **Domínios de atualização**: cinco domínios de atualização são atribuídos por padrão e isso pode ser definido como entre 1 e 20. Os domínios de atualização indicam grupos de máquinas virtuais e hardware físico subjacente que podem ser reinicializados ao mesmo tempo. Por exemplo, se especificarmos cinco domínios de atualização, quando mais do que cinco máquinas virtuais forem configuradas em um único conjunto de disponibilidade, a sexta máquina virtual será colocada no mesmo domínio de atualização que a primeira máquina virtual, a sétima no mesmo UD que a segunda e assim por diante. A ordem da reinicialização poderá não ser sequencial, mas apenas um domínio de atualização será reinicializado por vez.
	- **Assinatura** – selecione a assinatura a ser usada, caso tenha mais de uma.
	- **Grupo de recursos**: selecione um grupo de recursos existente, clicando na seta e selecionando um grupo de recursos no menu suspenso. Você também pode criar um novo grupo de recursos digitando um nome. O nome pode conter qualquer um dos seguintes caracteres: letras, números, pontos, traços, sublinhados e parênteses de abertura ou fechamento. O nome não pode terminar em um ponto. Todas as VMs no grupo de disponibilidade precisam ser criadas no mesmo grupo de recursos do conjunto de disponibilidade.
	- **Localização**: selecione uma localização na lista suspensa.

4. Quando terminar de inserir as informações, clique em **Criar**. Após o grupo de disponibilidade ser criado, você poderá vê-lo na lista depois de atualizar o portal.

## Usar o portal para criar uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo

Se estiver criando uma nova VM usando o portal, você também poderá criar um novo conjunto de disponibilidade para a nova VM enquanto cria a primeira VM no conjunto.

![Captura de tela que mostra o processo para criar um novo conjunto de disponibilidade ao criar a VM.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## Adicionar uma nova VM a um conjunto de disponibilidade existente

Para cada VM adicional que você criar e que deva pertencer ao conjunto, certifique-se de criá-la no mesmo **grupo de recursos** e selecione o conjunto de disponibilidade existente na Etapa 3.

![Captura de tela mostrando como selecionar um conjunto de disponibilidade existente para ser usado com sua VM.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## Usar o PowerShell para criar um conjunto de disponibilidade

Este exemplo cria um conjunto de disponibilidade no grupo de recursos **RMResGroup** na localização **Oeste dos EUA**. Isso precisa ser feito antes de criar a primeira VM que estará no conjunto.

	New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
	
Para obter mais informações, consulte [New-AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx).


## Solucionar problemas

- Ao criar uma VM, se o conjunto de disponibilidade desejado não estiver na lista suspensa no portal, você poderá ter realizado a criação dele em um grupo de recursos diferente. Se você não souber o grupo de recursos para sua seu conjunto de disponibilidade, vá até o menu hub e clique em Procurar > Conjuntos de disponibilidade para ver uma lista dos seus conjuntos de disponibilidade e a quais grupos de recursos pertencem.


## Próximas etapas

Adicione armazenamento adicional à sua VM incluindo um [disco de dados](virtual-machines-windows-attach-disk-portal.md) adicional.

<!---HONumber=AcomDC_0928_2016-->