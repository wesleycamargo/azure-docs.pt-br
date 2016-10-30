<properties
    pageTitle="Desanexar um disco de dados de uma VM do Windows | Microsoft Azure"
    description="Saiba como desanexar um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Resource Manager."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>




# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Windows


Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento. 

> [AZURE.WARNING] Se você desanexar um disco, ele não será excluído automaticamente. Se você se inscreveu para o armazenamento Premium, você continuará incorrendo em encargos de armazenamento para o disco. Para obter mais informações, consulte [Preços e cobrança ao usar o Armazenamento Premium](../storage/storage-premium-storage.md#pricing-and-billing). 

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.  


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal

1. No hub do portal, selecione **máquinas virtuais**.

2. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar e então clique em **Todas as configurações**.

3. Na folha **Configurações**, selecione **Discos**.

4. Na folha **Discos** , selecione os dados do disco que você deseja desanexar.

5. Na folha do disco de dados, clique em **Desanexar**.


    ![Captura de tela mostrando o botão Desanexar.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.


## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell

Neste exemplo, o primeiro comando obtém a máquina virtual chamada **MyVM07** no grupo de recursos **RG11** usando o cmdlet Get-AzureRmVM. O comando armazena a máquina virtual na variável **$VirtualMachine** . 

O segundo comando remove da máquina virtual o disco de dados denominado DataDisk3. 

O comando final atualiza o estado da máquina virtual para concluir o processo de remoção do disco de dados.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Para obter mais informações, consulte [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Próximas etapas

Se você quiser reutilizar o disco de dados, você poderá simplesmente [anexá-lo a outra VM](virtual-machines-windows-attach-disk-portal.md)



<!--HONumber=Oct16_HO2-->


