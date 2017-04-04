1. No portal, navegue até a rede virtual para a qual você deseja criar um gateway de rede virtual.
2. Na seção **Configurações** da folha de sua rede virtual, clique em **Sub-redes** para expandir a folha Sub-redes.
3. Na folha **Sub-redes**, clique em **+Sub-rede de gateway** na parte superior. Isso abrirá a folha **Adicionar sub-rede** . 
   
    ![Adicionar a sub-rede de gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsubnet.png "Adicionar a sub-rede de gateway")
4. O **nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Esse valor é necessário para que o Azure reconheça a sub-rede como a sub-rede de gateway. Ajuste os valores preenchidos automaticamente de **Intervalo de endereços** para corresponder aos seus requisitos de configuração.

    ![Adicionar a sub-rede](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnet.png "Adicionar sub-rede")
5. Clique em **OK** na parte inferior da folha para criar a sub-rede.