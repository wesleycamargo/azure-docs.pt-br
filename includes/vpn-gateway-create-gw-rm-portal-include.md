1. No portal, vá para **Novo** e, em seguida, **Procurar**. Selecione **Gateways de rede virtual** da lista.
2. Clique em **Adicionar**.
3. Nomeie o seu gateway. Isso não é igual a nomear uma sub-rede de gateway. Esse é o nome do objeto de gateway. 
4. Em **Rede virtual**, selecione a rede virtual que você deseja conectar a esse gateway.
5. Nas configurações da Rede Virtual, para o valor **Endereço IP público**, crie um nome para seu endereço IP público. Observe que isso não é o mesmo que pedir por um endereço IP. O endereço IP será atribuído dinamicamente. Em vez disso, esse é o nome do objeto de endereço IP ao qual o endereço será atribuído. 
6. Para **Tipo de VPN**, as opções são baseadas em política e baseadas em rota. Certifique-se de selecionar o tipo de gateway de VPN que tem suporte tanto pelo cenário de configuração quanto, se necessário para sua configuração, pelo dispositivo de gateway de VPN que você planeja usar.
7. Para **Grupo de Recursos**, escolha **selecionar existente** e escolha o grupo de recursos em que reside sua rede virtual, a menos que sua configuração exija uma opção diferente.
8. Para **Local**, verifique se ele está mostrando o local em que existem ambos o seu Grupo de Recursos e sua Rede Virtual.
9. Clique em **Criar**. Você verá o bloco *Implantando gateway de rede virtual* no painel. Criar um gateway leva algum tempo. Há muitas coisas acontecendo em segundo plano. Planeje que isso leve 15 minutos ou mais. Talvez seja necessário atualizar a página do portal para ver o status concluído.
10. Depois de criar o gateway você pode exibir, observando a Rede Virtual no portal, o endereço IP que foi atribuído a esse gateway. O gateway será exibido como um dispositivo conectado. Você pode exibir o nome e o endereço IP atribuído ao gateway.

<!---HONumber=AcomDC_0114_2016-->