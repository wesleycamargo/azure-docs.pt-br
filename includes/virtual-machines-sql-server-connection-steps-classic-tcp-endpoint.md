### Criar um ponto de extremidade TCP para a máquina virtual

Para acessar o SQL Server da Internet, a máquina virtual deve ter um ponto de extremidade para escutar comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

>[AZURE.NOTE]Se estiver se conectando dentro do mesmo serviço de nuvem ou rede virtual, você não precisa criar um ponto de extremidade acessível publicamente. Nesse caso, você pode continuar para a próxima etapa. Para obter mais informações, consulte [Cenários de conexão](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. No Portal de Gerenciamento do Azure, clique em **MAQUINAS VIRTUAIS**.
	
2. Clique na máquina virtual recém-criada. As informações sobre sua máquina virtual são apresentadas.
	
3. Próximo à parte superior da página, selecione a página **PONTOS DE EXTREMIDADE** e, na parte inferior da página, clique em **ADICIONAR**.
	
4. Na página **Adicionar Ponto de Extremidade à Máquina Virtual**, clique em **Adicionar Ponto de Extremidade Independente** e, em seguida, clique na seta de avanço para continuar.
	
5. Na página **Especificar os detalhes do ponto de extremidade**, forneça as seguintes informações.

	- Na caixa **NOME**, forneça um nome para o ponto de extremidade.
	- Na caixa **PROTOCOLO**, selecione **TCP**. Você pode digitar **57500** na caixa **PORTA PÚBLICA**. Da mesma forma, você pode digitar a porta de escuta padrão do SQL Server **1433** na caixa **Porta Particular**. Observe que muitas organizações selecionam números de porta diferentes para evitar ataques de segurança mal-intencionados. 

6. Clique na marca de seleção para continuar. O ponto de extremidade é criado.

<!---HONumber=AcomDC_0107_2016-->