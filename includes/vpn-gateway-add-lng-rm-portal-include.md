1. No Portal do Azure, navegue até **Novo** **>** **Rede** **>** **Gateway da rede local**.

	![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. Na **folha Criar gateway da rede local**, especifique um **Nome** para seu objeto de gateway da rede local.
 
3. Especifique um **endereço IP** para seu gateway. Esse é o endereço IP do dispositivo VPN externo ao qual você deseja se conectar. Ele não pode estar por trás do NAT e deve ser acessível pelo Azure.

4. **Espaço do Endereço** refere-se aos intervalos de endereço em sua rede (normalmente local). Você pode adicionar vários intervalos de espaço de endereço. Os intervalos que você digitar aqui não podem sobrepor nenhum dos intervalos de espaço de endereço que você está usando para qualquer uma das redes virtuais que se comunicarão através do gateway. Será necessário coordenar com sua configuração local, bem como com os espaços de endereço de rede virtual do Azure.
 
5. Para **Assinatura**, verifique se a assinatura correta está sendo exibida.

6. Para **Grupo de Recursos**, selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou. Para criar um novo grupo de recursos, digite o nome na caixa. Para selecionar um grupo de recursos que você já criou, clique em **Grupo de Recursos** para abrir a folha **Grupo de Recursos** e selecione o grupo de recursos que você deseja usar.

7. Para **Local**, se você estiver criando um novo gateway de rede local, poderá usar o mesmo local do gateway da rede virtual. Mas, isso não é necessário. O gateway de rede local pode estar em um local diferente.

8. Deixe "Fixar ao Painel" selecionado se desejar localizar esse gateway de rede local facilmente no painel.

9. Clique em **Criar** para criar o gateway da rede local. Você verá "Implantando Gateway de Rede Local" no seu painel.

10. Após a criação do gateway de rede local, ele será aberto no portal permitindo que você o visualize.

	
