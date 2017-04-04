1. Localizar seu gateway de rede virtual.
2. Clique em **Conexões**. Na parte superior da folha Conexões, clique em **+ Adicionar** para abrir a folha **Adicionar conexão**.
   
    ![Criar uma conexão site a site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection.png)
3. Na folha **Adicionar conexão**, dê um **Nome** para sua conexão. 
4. Para **Tipo de conexão**, selecione **Site a Site (IPsec)**.
5. Para **Gateway de Rede Virtual**, o valor é fixo porque você está se conectando por esse gateway.
6. Para **Gateway de rede local**, clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que você deseja usar. 
7. Para a **Chave Compartilhada**, o valor aqui deve coincidir com o valor usado para seu dispositivo VPN local. Neste exemplo, usamos ‘abc123’, mas você pode (e deve) usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.
8. Os valores restantes para **Assinatura**, **Grupo de Recursos** e **Local** são corrigidos.
9. Clique em **OK** para criar sua conexão. Você verá *Criar Conexão* piscar na tela.
10. Quando a conexão for concluída, ela aparecerá na folha **Conexões** do seu gateway de rede virtual.
    
    ![Criar uma conexão site a site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connectionstatus450.png)

