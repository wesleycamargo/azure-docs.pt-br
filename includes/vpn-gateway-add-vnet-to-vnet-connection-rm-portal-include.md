Ao adicionar uma conexão de VNet para VNet, verifique se ambas as redes virtuais têm um gateway de rede virtual e se suas redes virtuais não têm nenhuma sobreposição de intervalos de endereços.

1. Na folha **Redes Virtuais**, localize sua rede virtual e clique para abrir a folha. Na folha, você verá seu gateway listado como um *Dispositivo Conectado*. Você também pode definir as configurações diretamente do seu gateway de rede virtual sem primeiro expandir o VNet.
2. Nas configurações de gateway de rede virtual, clique em **Conexões** e, então, em **Adicionar**.
3. **Nomeie** sua conexão. 
4. Para **Tipo de Conexão**, selecione **VNet para VNet**
5. Para **Gateway de Rede Virtual**, o valor é fixo porque você está se conectando por esse gateway.
6. Para **Segundo Gateway de Rede Virtual**, selecione o gateway para o qual deseja criar uma conexão desse gateway.
8. O restante dos valores para **Assinatura**, **Grupo de Recursos** e **Local** são fixos.
9. Clique em **OK** para criar sua conexão. Você verá *Criar Conexão* piscar na tela.
10. Quando a conexão for concluída, você a verá na folha **Conexões** do seu Gateway.

<!---HONumber=AcomDC_0107_2016-->