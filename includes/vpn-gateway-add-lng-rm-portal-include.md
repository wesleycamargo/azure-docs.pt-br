Para criar um gateway de rede local, siga as etapas abaixo:

1. Use **Procurar** e fildter para localizar a folha **Gateways de Rede Local** e clique em **Adicionar**.
2. Na **folha Criar Gateways de Rede Local**, **nomeie** seu objeto de gateway de rede local. Para esse exemplo, chamaremos o gateway de rede local de *GW1LocalNet*.
3. Configure um **Endereço IP** para seu gateway. Esse é o endereço IP do dispositivo VPN externo ao qual você deseja se conectar. Ele não pode estar por trás do NAT e deve ser acessível pelo Azure. Esse é o endereço IP do dispositivo ao qual seu gateway do Azure será conectado.
4. **Espaço de Endereço** refere-se aos intervalos de endereços na sua rede local (normalmente local). Você pode adicionar vários intervalos de espaço de endereço. Os intervalos que você digitar aqui não podem sobrepor nenhum dos intervalos de espaço de endereço que você está usando para qualquer uma das redes virtuais que se comunicarão através do gateway. Será necessário coordenar com sua configuração local, bem como com os espaços de endereço de rede virtual do Azure. 
5. Para **assinatura**, verifique se a assinatura correta está sendo exibida.
6. Para **Grupo de Recursos**, selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou. Para criar um novo grupo de recursos, digite o nome na caixa. Para selecionar um grupo de recursos que você criou, clique em **Grupo de Recursos** para abrir a folha **Grupo de Recursos** e selecione o grupo de recursos que você deseja usar.
7. Para **local**, verifique se o local é o mesmo que o gateway de rede virtual com o qual você fará a associação.
8. Deixe "Fixar ao Painel" selecionado se desejar localizar esse gateway de rede local facilmente no painel.
9. Clique em **Criar** para criar o gateway de rede local. Você verá "Implantando Gateway de Rede Local" no seu painel. A criação não demora muito.

<!---HONumber=AcomDC_0107_2016-->