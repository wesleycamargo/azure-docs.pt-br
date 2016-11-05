## Como criar emparelhamento VNet no Portal do Azure
Para criar um emparelhamento VNet com base no cenário acima, usando o Portal do Azure, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Para estabelecer o emparelhamento VNET, você precisa criar dois links, um para cada direção, entre duas VNets. Primeiro, você pode criar links de emparelhamento de VNET para VNET1 à VNET2. No portal, clique em **Procurar** > **escolha Redes Virtuais**
   
    ![Criar emparelhamento VNet no Portal do Azure](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)
3. Na folha Redes Virtuais, escolha VNET1, clique em Emparelhamentos e clique em Adicionar
   
    ![Escolher emparelhamento](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)
4. Na folha Adicionar Emparelhamento, dê o nome LinkToVnet2 ao link de emparelhamento, escolha a assinatura e o par Rede Virtual VNET2, e clique em OK.
   
    ![Link para VNet](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)
5. Após a criação deste link de emparelhamento VNET. Você pode ver o estado do link da seguinte maneira:
   
    ![Estado do link](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)
6. Em seguida, crie o link de emparelhamento VNET da VNET2 à VNET1. Na folha Redes Virtuais, escolha VNET2, clique em Emparelhamentos e clique em Adicionar
   
    ![Par de outra VNet](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)
7. Na folha Adicionar Emparelhamento, dê o nome LinkToVnet1 ao link de emparelhamento, escolha a assinatura e o par Rede Virtual, e clique em OK.
   
    ![Criar bloco de rede virtual](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)
8. Após a criação deste link de emparelhamento VNET. Você pode ver o estado do link da seguinte maneira:
   
    ![Estado final do link](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)
9. Verifique o estado de LinkToVnet2, que também foi alterado para Conectado.
   
    ![Estado final do link 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)
10. OBSERVAÇÃO: o emparelhamento VNET somente será estabelecido se ambos os links estiverem conectados.

<!---HONumber=AcomDC_0803_2016-->