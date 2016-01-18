Para criar um VNet com base no cenário anterior, usando o Portal do Azure, siga as etapas abaixo.

1. Em um navegador, vá até http://portal.azure.com e, se necessário, entre com sua conta do Azure.

2. Clique em **NOVO** > **Rede** > **Rede Virtual** e, em seguida, clique em **Gerenciador de Recursos** na lista **Selecionar um Modelo de Implantação** e, em seguida, clique em **Criar**.

3. Na folha **Criar rede virtual**, defina as configurações de Rede Virtual, como mostrado na figura abaixo.

	![Criar folha de rede virtual](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Clique em **Grupo de recursos** e selecione um grupo de recursos ao qual adicionar a Rede Virtual ou clique em **Criar novo** para adicionar a Rede Virtual a um novo grupo de recursos. A figura a seguir mostra as configurações de grupo de recurso para um novo grupo de recursos denominado **TestRG**. Para obter mais informações sobre grupos de recursos, visite [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md/#resource-groups).

	![Grupo de recursos](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Se necessário, altere as configurações de **Assinatura** e **Local** da sua rede virtual.

6. Clique em **Criar** e observe o bloco chamado **Criando a rede virtual** como mostrado na figura abaixo.

	![Criar bloco de rede virtual](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

7. Aguarde a criação do VNET e, na folha **Rede Virtual**, clique em **Todas as Configurações** > **Sub-redes** > **Adicionar**.

8. Especifique as configurações de sub-rede para a sub-rede *back-end*, como mostrado abaixo e clique **OK**.

	![Configurações de sub-rede](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

9. Exibir lista de sub-redes.

	![Lista de sub-redes na Rede Virtual](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0107_2016-->