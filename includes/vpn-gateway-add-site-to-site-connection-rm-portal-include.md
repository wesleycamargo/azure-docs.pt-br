Ao adicionar uma conexão site a site para o gateway de rede virtual, você precisa primeiro criar um gateway de rede local para fazer referência a ele da sua configuração. Verifique se você tem um gateway de rede local configurado. Você pode procurar os gateways de rede local usando **Procurar** e filtrando **Gateways de Rede Local**.

1. Na folha **Redes Virtuais**, localize sua rede virtual e clique para abrir a folha. Na folha, você verá seu gateway listado como um *Dispositivo Conectado*.
2. Clique no ***nome do seu gateway de rede virtual*** -> **Gateway de Rede Virtual** -> **Configurações** -> **Conexões** e, em seguida, clique em **Adicionar**.
3. **Nomeie** sua conexão. Para esse exemplo, vamos usar *GW1S2S*
4. Para **Tipo de Conexão**, selecione **Site a Site (IPsec)**
5. Para **Gateway de Rede Virtual**, o valor é fixo porque você está se conectando por esse gateway.
6. Para **Gateway de Rede Local**, clique em **Escolher um Gateway de Rede Local** e selecione o gateway de rede local que você deseja usar. Para esse exemplo, vamos usar *GW1LocalNet*
7. Para **Chave Compartilhada**, os valores aqui devem coincidir com o que você tem para seu dispositivo VPN local. Se o seu dispositivo VPN na sua rede local não fornecer uma chave compartilhada, você pode compor uma e inseri-la aqui e em seus dispositivos locais. O importante é que ambos correspondam.
8. O restante dos valores para **Assinatura**, **Grupo de Recursos** e **Local** são fixos.
9. Clique em **OK** para criar sua conexão. Você verá *Criar Conexão* piscar na tela.
10. Quando a conexão for concluída, você a verá na folha **Conexões** do seu Gateway.

<!---HONumber=AcomDC_0107_2016-->