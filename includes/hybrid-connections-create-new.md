
1. No computador local, acesse o [Portal de Gerenciamento do Azure](http://manager.windowsazure.com) (esse é o portal antigo).

2. Na parte inferior do painel de navegação, selecione **+NOVO** > **Serviços de Aplicativos** > **Serviço BizTalk** > **Criação Personalizada**.

3. Forneça um **Nome de Serviço BizTalk** e selecione uma **Edição**.

	Este tutorial usa **mobile1**. Você precisará fornecer um nome exclusivo para seu novo Serviço BizTalk.

4. Uma vez que o Serviço BizTalk foi criado, selecione a guia **Conexões Híbridas** e clique em **Incluir**.

	![Incluir Conexão Híbrida](./media/hybrid-connections-create-new/3.png)

	Isso cria uma nova conexão híbrida.

5. Forneça um **Nome** e **Nome de Host** para sua conexão híbrida e configure a **Porta** como `1433`.
  
	![Configurar a Conexão Híbrida](./media/hybrid-connections-create-new/4.png)

	O nome do host é o nome do servidor local. Isso configura a conexão híbrida para acessar o SQL Server em execução na porta 1433. Se estiver usando uma instância do SQL Server nomeada, use a porta estática que você definiu anteriormente.

6. Depois que a nova conexão for criada, o status dela mostrará **Configuração local incompleta**.

7. Navegue de volta para o serviço móvel, clique em **Configurar**, role para baixo até **Conexões híbridas** e clique em **Adicionar conexão híbrida**. Em seguida, selecione a conexão híbrida que você acabou de criar e clique em **OK**.

    Isso permite que o serviço móvel use a nova conexão híbrida.

Em seguida, você precisará instalar o Gerenciador de Conexão Híbrida no computador local.

<!---HONumber=July15_HO4-->