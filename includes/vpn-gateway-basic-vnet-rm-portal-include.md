Para criar uma rede virtual usando o Portal do Azure, execute as etapas abaixo. Observe que as capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios. Para saber mais sobre como trabalhar com as redes virtuais, consulte a [Visão Geral da Rede Virtual](../articles/virtual-network/virtual-networks-overview.md)

1. Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.

2. Clique em **Nova** **>** **Rede** **>** **Rede Virtual**.

	![VNetBlade](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal650.png)

3. Perto da parte inferior da folha Rede Virtual, na lista **Selecionar um modelo de implantação**, selecione **Resource Manager** e clique em **Criar**.


	![Selecione Gerenciador de Recursos](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png)

4. Na folha **Criar rede virtual**, defina as configurações da VNet. Nesta folha, você adicionará o primeiro espaço de endereço e um único intervalo de endereços de sub-rede. Depois de terminar a criação da rede virtual, você poderá voltar e adicionar espaços de endereço e sub-redes adicionais. Esta é uma limitação atual do portal. Você sempre pode voltar para atualizar esses valores, editando as propriedades da rede virtual no portal ou usando o PowerShell. Os valores que você usa dependerão da configuração que você deseja criar. Lembre-se de consultar os valores de configuração planejados.

	![Criar folha de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/createavnet250.png)

5. Verifique se a **Assinatura** é a correta. Você pode alterar as assinaturas usando o menu suspenso.

6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo digitando um nome para seu novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre grupos de recursos, visite [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md#resource-groups).

7. Em seguida, selecione as configurações do **Local** para sua VNet. Observe que o local determinará onde ficarão os recursos que você implanta para essa rede virtual. Você não pode alterar isso posteriormente sem reimplantar seus recursos.

8. Selecione **Fixar no painel** se quiser ser capaz de encontrar sua VNet facilmente no painel, em seguida, clique em **Criar**.
	
	![Fixar no painel](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png)


9. Depois de clicar em Criar, você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco mudará à medida que a rede virtual é criada.

	![Criar bloco de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png)