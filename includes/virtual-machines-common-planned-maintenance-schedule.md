

## VMs de instância única e várias instâncias
Para muitos clientes que usam o Azure, é essencial que eles possam agendar quando suas VMs passarão pela manutenção planejada, uma vez que isso resulta em aproximadamente 15 minutos de tempo de inatividade. Você pode aproveitar os conjuntos de disponibilidade para ajudar a controlar quando as VMs provisionadas receberão a manutenção planejada.

Existem duas possíveis configurações para VMs em execução no Azure. As VMs são configuradas como várias instâncias ou instância única. Se as VMs estiverem em um conjunto de disponibilidade, elas serão configuradas como várias instâncias. Observe que mesmo as VMs únicas podem ser implantadas em um conjunto de disponibilidade, sendo tratadas como várias instâncias. Se as VMs NÃO estiverem em um conjunto de disponibilidade, elas serão configuradas como instância única. Para obter detalhes sobre conjuntos de disponibilidade, confira [Manage the Availability of your Windows Virtual Machines (Gerenciar a disponibilidade das suas máquinas virtuais do Windows)](../articles/virtual-machines/virtual-machines-windows-manage-availability.md) ou [Manage the Availability of your Linux Virtual Machines (Gerenciar a disponibilidade das suas máquinas virtuais do Linux)](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

As atualizações da manutenção planejada para VMs de instância única e várias instâncias ocorrem separadamente. Ao reconfigurar as VMs para serem instância única (se elas forem várias instâncias) ou para serem várias instâncias (se elas forem instância única), você pode controlar quando as VMs receberão a manutenção planejada. Confira [Planned maintenance for Azure Linux virtual machines (Manutenção planejada de máquinas virtuais Linux do Azure)](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md) ou [Planned maintenance for Azure Windows virtual machines (Manutenção planejada de máquinas virtuais Windows do Azure)](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md) para obter detalhes sobre a manutenção planejada para VMs do Azure.

## Para configuração de várias instâncias
Você pode escolher a hora em que a manutenção planejada afetará suas VMs que estão implantadas em uma configuração de Conjunto de Disponibilidade removendo essas VMs dos conjuntos de disponibilidade.

1.	Um email será enviado a você sete dias corridos antes da manutenção planejada nas VMs em uma configuração de várias instâncias. As IDs de assinatura e os nomes das VMs de várias instâncias afetadas serão incluídos no corpo do email.

2.	Durante esses sete dias, você pode escolher a hora em que as instâncias serão atualizadas removendo as VMs de várias instâncias dessa região do conjunto de disponibilidade. Essa alteração na configuração resulta em uma reinicialização, pois a máquina virtual está passando de um host físico, direcionado para manutenção, para outro host físico que não está direcionado para manutenção.

3.	Você pode remover a VM de seu conjunto de disponibilidade no portal clássico.
   
    1.	No portal do clássico, clique na VM e selecione “configurar”.

    2.	Em "configurações", você pode ver em qual conjunto de disponibilidade a VM está.

        ![Seleção do conjunto de disponibilidade](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

    3.	No menu suspenso do conjunto de disponibilidade, selecione “remover do conjunto de disponibilidade”.

        ![Remover do conjunto](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)

    4.	Na parte inferior, selecione “salvar”. Selecione "sim" para confirmar que esta ação reiniciará a VM.

4.	Essas VMs serão movidas para hosts de instância única e não poderão ser atualizadas durante a manutenção planejada para Configurações do Conjunto de Disponibilidade.

5.	Assim que a atualização das VMs do Conjunto de Disponibilidade estiver concluída (de acordo com a agenda descrita no email original), você deverá adicionar as VMs de volta aos respectivos conjuntos de disponibilidade, e elas serão reconfiguradas como VMs de várias instâncias. Mover as VMs de instância única de volta para várias instâncias resultará em uma reinicialização. Geralmente, assim que as atualizações de várias instâncias são concluídas em todo o ambiente do Azure, a manutenção de instância única continua.

Isso também pode ser feito usando o Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## Para configuração de instância única
Você pode selecionar a hora que a manutenção planejada afeta suas VMs em uma configuração de instância única adicionando essas VMs aos conjuntos de disponibilidade.

Passo a passo

1.	Um email será enviado a você sete dias corridos antes da manutenção planejada nas VMs em uma configuração de instância única. As IDs de assinatura e os nomes das VMs de instância única afetadas serão incluídos no corpo do email.

2.	Durante esses sete dias, você pode escolher a hora em que sua instância será reinicializada, movendo suas VMs de instância única para um conjunto de disponibilidade nessa mesma região. Essa alteração na configuração resulta em uma reinicialização, pois a máquina virtual está passando de um host físico, direcionado para manutenção, para outro host físico que não está direcionado para manutenção.

3.	Siga as instruções aqui para adicionar VMs existentes aos conjuntos de disponibilidade usando o Portal Clássico e o Azure PowerShell (confira o exemplo do Azure PowerShell na observação abaixo).

4.	Depois que essas VMs forem reconfiguradas como várias instâncias, elas serão excluídas da manutenção planejada para VMs de instância única.

5.	Assim que a atualização para VMs de instância única estiver concluída (de acordo com a agenda descrita no email original), você poderá remover as VMs dos conjuntos de disponibilidade, e elas serão reconfiguradas como VMs de instância única.

Isso também pode ser feito usando o Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0803_2016-->