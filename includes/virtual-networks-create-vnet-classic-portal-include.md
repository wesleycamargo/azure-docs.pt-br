## Como criar uma rede virtual no portal do Azure
Para criar uma rede virtual com base no cenário acima, siga as etapas abaixo.

1. Em um navegador, navegue até http://manage.windowsazure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **NOVO** > **SERVIÇOS DE REDE** > **REDE VIRTUAL** > **CRIAÇÃO PERSONALIZADA**, conforme mostrado na figura abaixo.
   
    ![Criar rede virtual no portal](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)
3. Na página **Detalhes da Rede Virtual**, digite o **NOME** da rede virtual, selecione seu **LOCAL** e clique na seta no canto inferior direito da página para avançar para a etapa 2. A figura a seguir mostra as configurações do nosso cenário.
   
    ![Página Detalhes da Rede Virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)
4. Na página **Servidores DNS e Conectividade VPN**, especifique o nome e o endereço IP de até 9 servidores DNS a serem usados. Se você não especificar um servidor DNS, sua rede virtual usará a resolução de nomes interna fornecida pelo Azure. Em nosso cenário, não configuraremos servidores DNS.
5. Se você deseja fornecer acesso VPN ponto a site para sua rede virtual, habilite a caixa de seleção **Configurar uma VPN ponto a site**. Se você não configurar uma VPN ponto a site, será possível adicioná-la à rede virtual a qualquer momento após a criação. Em nosso cenário, não configuraremos uma VPN ponto a site.
6. Se quiser fornecer conectividade VPN site a site entre sua rede virtual e outra rede virtual ou sua rede local, habilite a caixa de seleção **Configurar uma VPN site a site** e especifique se deseja usar **Rota Expressa** ou não, e o nome da rede à qual se conectar. Se você não configurar uma VPN site a site, será possível adicioná-la à rede virtual a qualquer momento após a criação. Em nosso cenário, não configuraremos uma VPN site a site.
7. Clique na seta no canto inferior direito da página para avançar para a etapa 3. A figura abaixo mostra as configurações do nosso cenário.
   
    ![Página Servidores DNS e conectividade VPN](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)
8. Na página **Espaços de Endereço de Rede Virtual**, em **IP INICIAL**, clique em *10.0.0.0* para alterar o espaço de endereço da rede virtual e digite o espaço de endereço inicial que deseja usar. Para nosso cenário, digite *192.168.0.0*.
9. Em **CIDR (CONTAGEM DE ENDEREÇOS)**, selecione o número de bits da máscara da sub-rede. Para nosso cenário, selecione *16 (65536)*.
10. Em **SUB-REDES**, clique em *Sub-rede-1* e renomeie a sub-rede, se necessário. Para o nosso cenário, renomeie-a para *FrontEnd*.
    
    > [!NOTE]
    > Se você clicar fora da caixa de texto de nome de uma sub-rede, não será possível editar o nome novamente. Para corrigir isso, é preciso remover a sub-rede clicando no botão X à direita e depois adicionar uma nova sub-rede, conforme descrito na etapa 13 abaixo.
    > 
    > 
11. Em **IP INICIAL** da primeira sub-rede, especifique o endereço IP inicial da sub-rede. Para nosso cenário, digite *192.168.1.0*.
12. Em **CIDR (CONTAGEM DE ENDEREÇOS)**, selecione o número de bits da máscara da primeira sub-rede. Para nosso cenário, selecione *24 (256)*.
13. Clique em **adicionar sub-rede** para adicionar uma nova sub-rede, se necessário. Para o nosso cenário, adicione uma sub-rede e repita as etapas de 10 a 12 para configurar a rede virtual, conforme mostrado na figura abaixo.
    
    ![Página Espaços de endereço de rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)
14. Clique no botão de marca de seleção no canto inferior direito da página para criar a rede virtual. Depois de alguns segundos, sua rede virtual será mostrada na lista de redes virtuais disponíveis, conforme mostrado na figura abaixo.
    
    ![Nova rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)

<!---HONumber=Oct15_HO3-->