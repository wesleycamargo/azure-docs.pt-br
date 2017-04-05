

## <a name="multi-and-single-instance-vms"></a>VMs de instância única e várias instâncias
Muitos clientes em execução no Azure consideram essencial poder agendar quando suas VMs terão manutenção planejada devido ao tempo de inatividade, cerca de 15 minutos, que ocorre durante a manutenção. Você pode usar os conjuntos de disponibilidade para ajudar a controlar quando as VMs provisionadas receberão a manutenção planejada.

Existem duas possíveis configurações para VMs em execução no Azure. As VMs são configuradas como várias instâncias ou instância única. Se as VMs estiverem em um conjunto de disponibilidade, elas serão configuradas como várias instâncias. Observe que mesmo as VMs únicas podem ser implantadas em um conjunto de disponibilidade, para que elas sejam tratadas como várias instâncias. Se as VMs NÃO estiverem em um conjunto de disponibilidade, elas serão configuradas como instância única.  Para obter detalhes sobre conjuntos de disponibilidade, confira [Gerenciar a disponibilidade das suas máquinas virtuais do Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Gerenciar a disponibilidade das suas máquinas virtuais do Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As atualizações da manutenção planejada para VMs de instância única e várias instâncias ocorrem separadamente. Ao reconfigurar as VMs para serem instância única (se elas forem várias instâncias) ou para serem várias instâncias (se elas forem instância única), você pode controlar quando as VMs receberão a manutenção planejada. Confira [Manutenção planejada de máquinas virtuais Linux do Azure](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Manutenção planejada de máquinas virtuais Windows do Azure](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter detalhes sobre a manutenção planejada para VMs do Azure.

## <a name="for-multi-instance-configuration"></a>Para configuração de várias instâncias
Você pode escolher a hora em que a manutenção planejada afetará suas VMs que estão implantadas em uma configuração de Conjunto de Disponibilidade removendo essas VMs dos conjuntos de disponibilidade.

1. Um email é enviado a você sete dias corridos antes da manutenção planejada nas VMs em uma configuração de várias instâncias. As IDs de assinatura e os nomes das VMs de várias instâncias afetadas são incluídos no corpo do email.
2. Durante esses sete dias, você pode escolher a hora em que as instâncias serão atualizadas removendo as VMs de várias instâncias dessa região do conjunto de disponibilidade. Essa alteração na configuração causa uma reinicialização, pois a máquina virtual está passando de um host físico, direcionado para manutenção, para outro host físico que não está direcionado para manutenção.
3. Você pode remover a VM de seu conjunto de disponibilidade no portal do Azure.

   1. No portal, selecione a VM a ser removida do conjunto de disponibilidade.  

   2. Em **configurações**, clique em **Conjunto de disponibilidade**.

      ![Seleção do conjunto de disponibilidade](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. No menu suspenso do conjunto de disponibilidade, selecione “Não é parte de um conjunto de disponibilidade”.

      ![Remover do conjunto](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. Na parte superior, clique em **Salvar**. Clique em **Sim** para confirmar que essa ação reinicia a VM.

   >[!TIP]
   >Você pode reconfigurar a máquina virtual com várias instâncias mais tarde, selecionando um dos conjuntos de disponibilidade listados.

4. As VMs removidas dos conjuntos de disponibilidade são movidas para hosts de instância única e não são atualizadas durante a manutenção planejada de acordo com as Configurações do Conjunto de Disponibilidade.
5. Assim que a atualização das VMs do Conjunto de Disponibilidade estiver concluída (de acordo com a agenda descrita no email original), você deverá adicionar as VMs de volta aos respectivos conjuntos de disponibilidade. Tornar-se parte de um conjunto de disponibilidade reconfigura as VMs como várias instâncias e resulta em uma reinicialização. Geralmente, assim que as atualizações de várias instâncias são concluídas em todo o ambiente do Azure, a manutenção de instância única continua.

A remoção de uma VM de um conjunto de disponibilidade também pode ser obtida usando o Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Para configuração de instância única
Você pode selecionar a hora que a manutenção planejada afeta suas VMs em uma configuração de instância única adicionando essas VMs aos conjuntos de disponibilidade.

Passo a passo

1. Um email é enviado a você sete dias corridos antes da manutenção planejada nas VMs em uma configuração de instância única. As IDs de assinatura e os nomes das VMs de instância única afetadas são incluídos no corpo do email.
2. Durante esses sete dias, você pode escolher a hora em que sua instância será reinicializada adicionando suas VMs de instância única a um conjunto de disponibilidade nessa mesma região. Essa alteração na configuração causa uma reinicialização, pois a máquina virtual está passando de um host físico, direcionado para manutenção, para outro host físico que não está direcionado para manutenção.
3. Siga as instruções para adicionar máquinas virtuais existentes a conjuntos de disponibilidade usando o portal do Azure e o Azure PowerShell. (Consulte o exemplo do Azure PowerShell que segue essas etapas.)
4. Depois que essas VMs são reconfiguradas como várias instâncias, elas são excluídas da manutenção planejada para VMs de instância única.
5. Uma vez concluída a atualização da VM de instância única (de acordo com a agenda no email original), você pode reverter as VMs para instância única removendo as VMs de seus conjuntos de disponibilidade.

Uma VM também pode ser adicionada a um conjunto de disponibilidade por meio do Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
