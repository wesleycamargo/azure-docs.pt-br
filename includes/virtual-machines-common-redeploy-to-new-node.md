
Se estiver tendo dificuldades em solucionar problemas de conexão RDP (Área de Trabalho Remota) em uma máquina virtual do Azure baseada em Windows ou em solucionar problemas de conexão SSH em uma máquina virtual do Azure baseada em Linux, este artigo o ajudará a atenuá-los por conta própria, sem recorrer ao suporte e sem redimensionar a máquina virtual. O Microsoft Azure reimplantará sua máquina virtual quando você invocar a operação de reimplantação por meio do Azure PowerShell.

Observe que, após a conclusão dessa operação, os dados de disco efêmero serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.


## Usando o PowerShell do Azure

Verifique se você tem o Azure PowerShell 1.x mais recente instalado em seu computador. Leia [Como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md) para obter mais informações.

Use este comando do Azure PowerShell para reimplantar a máquina virtual:

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


Durante a execução desse comando, verifique a máquina virtual no [portal do Azure](https://portal.azure.com). Observe se o **Status** da VM muda da seguinte forma:

1. O **Status** inicial é *Executando*

	![Reimplantar o status inicial](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. O **Status** muda para *Atualizando*

	![Reimplantar o status Atualizando](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. O **Status** muda para *Iniciando*

	![Reimplantar o status Iniciando](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. O **Status** muda de volta para *Executando*

	![Reimplantar o status final](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

Quando o **Status** voltar para *Executando*, a VM terá sido reimplantada com êxito.

<!---HONumber=AcomDC_0309_2016-->