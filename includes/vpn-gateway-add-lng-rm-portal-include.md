1. No portal, de **Todos os recursos**, clique em **+ Adicionar**.
2. Na caixa de pesquisa da página **Tudo**, digite **Gateway de rede local** e clique para retornar uma lista de recursos. Clique em **Gateway de rede local** para abrir a página, clique em **Criar** para abrir a página **Criar gateway de rede local**.

  ![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

  - **Nome:** especifique um nome para seu objeto de gateway de rede local. Se possível, use algo intuitivo, como **ClassicVNetLocal** ou **TestVNet1Local**. Isso facilita identificar o gateway de rede local no portal.
  - **Endereço IP:** especifique um **endereço IP** público válido para o dispositivo VPN ou o gateway de rede virtual ao qual você deseja se conectar.

    * **Se essa rede local representar um caminho local:** especifique o endereço IP público do dispositivo VPN ao qual você deseja conectar-se. Ele não pode estar por trás do NAT e deve poder ser acessado pelo Azure.
    * **Se essa rede local representar outra VNet:** especifique o endereço IP público atribuído ao gateway de rede virtual para essa VNet.
    * **Se você ainda não tiver o endereço IP:** poderá criar um endereço IP de espaço reservado válido, em seguida, voltar e modificar essa configuração antes de se conectar.
  - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos especificados aqui não se sobrepõem aos intervalos de outras redes com as quais você deseja conectar-se.
  - **Definir as configurações do BGP:** use apenas quando configurar o BGP. Caso contrário, não selecione essa opção.
  - **Assinatura:** verifique se a assinatura correta está sendo exibida.
  - **Grupo de Recursos:** selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
  - **Local:** selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.
4. Clique em **Criar** para criar o gateway de rede local.