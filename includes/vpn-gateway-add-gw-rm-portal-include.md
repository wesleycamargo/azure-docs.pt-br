1. No portal, vá para **Novo** > **Rede** > **Gateway de rede virtual**. Isso abrirá a folha **Criar gateway de rede virtual**.

	![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. Na folha **Criar gateway de rede virtual**, no campo **Nome**, nomeie seu gateway. Isso não é igual a nomear uma sub-rede de gateway. É o nome do objeto de gateway que será criado.

3. Ajuste o campo **Local** para apontar para o local onde se encontra sua rede virtual. Se você não fizer isso, a lista de rede virtual não mostrará a sua rede virtual.
 
4. Em seguida, escolha a rede virtual à qual você deseja adicionar este gateway. Clique em **Rede virtual** para abrir a folha **Escolher uma rede virtual**. Selecione a rede virtual. Para que a rede virtual apareça na lista, ela já deve ter uma sub-rede de gateway válida.

5. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir a folha **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir a folha **Criar endereço IP público**. Dê um nome para o seu endereço IP público. Isso criará um objeto de endereço IP público para o qual um endereço IP público será atribuído dinamicamente. <br>Clique em **OK** para salvar as alterações.

5. Para **Tipo de gateway**, selecione o Tipo de gateway especificado para sua configuração.

6. Para **Tipo de VPN**, selecione o Tipo de VPN especificado para sua configuração.

7. Para **Assinatura**, verifique se a assinatura correta foi selecionada.

8. O **Grupo de recursos** é determinado pela rede virtual selecionada.

9. Não ajuste o **local** depois de especificar as configurações acima.

10. Neste ponto, sua folha se parecerá com o gráfico na etapa 1. Verifique se as configurações correspondem às definições da sua própria configuração. Você pode selecionar **Fixar no painel** na parte inferior da folha se quer que seu gateway apareça no painel.

11. Clique em **Criar** para começar a criar o gateway. As configurações serão validadas e você verá o bloco "Gateway de rede virtual implantado" no painel. A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

	![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Depois de criar o gateway você pode exibir, observando a Rede Virtual no portal, o endereço IP que foi atribuído a esse gateway. O gateway será exibido como um dispositivo conectado. Você pode clicar no dispositivo conectado (seu gateway de rede virtual) para exibir mais informações.

<!---HONumber=AcomDC_0810_2016-->