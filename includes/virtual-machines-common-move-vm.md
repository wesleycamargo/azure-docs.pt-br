
Este artigo explica como mover uma VM entre as assinaturas. Isso pode ser útil se você tiver criado uma VM em uma assinatura pessoal e agora deseja movê-la para a assinatura da sua empresa a fim de continuar seu trabalho.

> [AZURE.NOTE] Novas IDs de recurso serão criadas como parte da mudança. Após a mudança da VM, você precisará atualizar suas ferramentas e scripts para usar as novas IDs de recurso.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Clique em **Procurar** > **Máquinas virtuais** e selecione a VM que deseja mover na lista.
	
	![Captura de tela da seção Essentials na qual você clica no ícone de lápis para abrir a folha Mover recursos.](./media/virtual-machines-common-move-vm/move-button.png)
	
3. Na seção **Essentials**, clique no ícone de lápis **Alterar assinatura** ao lado do nome da assinatura. A folha **Mover recursos** será aberta.
	
	![Captura de tela da folha Mover recursos.](./media/virtual-machines-common-move-vm/move.png)
	
4. Selecione cada um dos recursos para movimentação. Na maioria dos casos, você deve mover todos os recursos opcionais listados.
5. Selecione a **Assinatura** para a qual você deseja mover a VM.
6. Escolha um **Grupo de recursos** existente ou digite um nome para criar um novo.
7. Quando terminar, selecione que você entende que novas IDs de recurso serão criadas e que elas precisam ser usadas com a VM após a mudança, e clique em **OK**.



## Próximas etapas

Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../articles/resource-group-move-resources.md).

<!---HONumber=AcomDC_0706_2016-->