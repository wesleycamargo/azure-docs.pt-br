## Usando o Portal do Azure

1. Selecione a VM que você deseja reimplantar e clique no botão 'Reimplantar' na folha 'Configurações':

	![Folha VM do Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)

2. Clique no botão 'Reimplantar' para confirmar a operação:

	![Folha Reimplantar uma VM](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)

3. Você verá o **Status** da VM mudar para *Atualizando* à medida que a VM é preparada para a reimplantação:

	![VM atualizando](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)

4. O **Status** mudará para *Iniciando* enquanto a VM é inicializada em um novo host do Azure:

	![VM iniciando](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)

5. Após a conclusão do processo de inicialização da VM, o **Status** retornará a *Em execução*, indicando que a VM foi reimplantada com êxito:

	![VM em execução](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

<!---HONumber=AcomDC_0706_2016-->