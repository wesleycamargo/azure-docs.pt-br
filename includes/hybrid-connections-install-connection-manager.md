
O Gerenciador de Conexões Híbridas permite que seu computador local se conecte ao Azure e retransmita o tráfego TCP. Você deve instalar o gerenciador em um computador local que possa se conectar à sua instância do SQL Server.

1. A conexão que você acabou de criar deve ter um **Status** de **Instalação local incompleta**. Clique nesta conexão e clique em **Instalação Local**.

	![Instalação Local](./media/hybrid-connections-install-connection-manager/5-1.png)

2. Clique em **Instalar e Configurar**.

	Isso instala uma instância personalizada do Gerenciador de Conexão, que já está pré-configurado para trabalhar com a conexão híbrida que você acabou de criar.

3. Conclua o restante das etapas de configuração para o Gerenciador de Conexão.

	Depois que a instalação estiver concluída, o status da conexão híbrida mudará para **1 Instância Conectada**. Pode ser necessário atualizar o navegador e aguardar alguns minutos.

A configuração da conexão híbrida agora está concluída.

<!---HONumber=July15_HO2-->