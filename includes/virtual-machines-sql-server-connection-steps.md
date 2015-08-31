As etapas a seguir demonstram como se conectar à instância do SQL Server pela Internet usando o SSMS (SQL Server Management Studio). No entanto, as mesmas etapas se aplicam para tornar sua máquina virtual de SQL Server acessível para seu aplicativos, em execução local e no Azure.

Para poder conectar-se à instância do SQL Server na Internet ou em outra VM, você deve concluir as seguintes tarefas, conforme descrito nas seções a seguir:

- [Criar um ponto de extremidade TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Abrir portas TCP no firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurar o SQL Server para escutar no protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurar o SQL Server para autenticação do modo misto](#configure-sql-server-for-mixed-mode-authentication)
- [Criar logons de autenticação do SQL Server](#create-sql-server-authentication-logins)
- [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
- [Conectar-se ao Mecanismo de Banco de Dados de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de conexão é resumido pelo diagrama a seguir:

![Conectando-se a uma máquina virtual do SQL Server](./media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

### Criar um ponto de extremidade TCP para a máquina virtual

Para acessar o SQL Server da Internet, a máquina virtual deve ter um ponto de extremidade para escutar comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

>[AZURE.NOTE]Se estiver se conectando dentro do mesmo serviço de nuvem ou rede virtual, você não precisa criar um ponto de extremidade acessível publicamente. Nesse caso, você pode continuar para a próxima etapa. Para saber mais, consulte [Considerações sobre conectividade para o SQL Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/dn133152.aspx).

1. No Portal de Gerenciamento do Azure, clique em **MAQUINAS VIRTUAIS**.
	
2. Clique na máquina virtual recém-criada. As informações sobre sua máquina virtual são apresentadas.
	
3. Próximo à parte superior da página, selecione a página **PONTOS DE EXTREMIDADE** e, na parte inferior da página, clique em **ADICIONAR**.
	
4. Na página **Adicionar Ponto de Extremidade à Máquina Virtual**, clique em **Adicionar Ponto de Extremidade Independente** e, em seguida, clique na seta de avanço para continuar.
	
5. Na página **Especificar os detalhes do ponto de extremidade**, forneça as seguintes informações.

	- Na caixa **NOME**, forneça um nome para o ponto de extremidade.
	- Na caixa **PROTOCOLO**, selecione **TCP**. Você pode digitar **57500** na caixa **PORTA PÚBLICA**. Da mesma forma, você pode digitar a porta de escuta padrão do SQL Server **1433** na caixa **Porta Particular**. Observe que muitas organizações selecionam números de porta diferentes para evitar ataques de segurança mal-intencionados. 

6. Clique na marca de seleção para continuar. O ponto de extremidade é criado.

### Abrir portas TCP no firewall do Windows para a instância padrão do Mecanismo de Banco de Dados

1. Conecte-se à máquina virtual por meio da Área de Trabalho Remota do Windows. Uma vez conectado na tela inicial, digite **WF. msc** e pressione ENTER. 

	![Iniciar o Programa de Firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. No **Firewall do Windows com Segurança Avançada**, no painel esquerdo, clique com o botão direito do mouse em **Regras de Entrada**e, em seguida, clique em **Nova Regra** no painel de ações.

	![Nova Regra](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. Na caixa de diálogo **Assistente para Nova Regra de Entrada**, em **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

4. Na caixa de diálogo **Protocolo e Portas**, use o **TCP** padrão. Na caixa **Portas locais específicas**, digite o número da porta da instância do Mecanismo de Banco de Dados (**1433** para a instância padrão ou sua opção para a porta privada na etapa de ponto de extremidade).

	![Porta TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. Clique em **Próximo**.

6. Na caixa de diálogo **Ação**, selecione **Permitir a conexão**, e clique em **Avançar**.

	**Observação sobre segurança:** a seleção de **Permitir a conexão se for segura** pode fornecer segurança adicional. Selecione essa opção se você desejar configurar opções de segurança adicionais em seu ambiente.

	![Permitir Conexões](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. Na caixa de diálogo **Perfil**, selecione **Público**, **Privado** e **Domínio**. Em seguida, clique em **Próximo**.

    **Observação sobre segurança:** a seleção de **Público** permite acesso pela internet. Sempre que possível, selecione um perfil mais restritivo.

	![Perfil Público](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. Na caixa de diálogo **Nome**, digite um nome e uma descrição para essa regra, e clique em **Concluir**.

	![Nome da Regra](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Abrir portas adicionais para outros componentes conforme necessário. Para obter mais informações, consulte [Configurando o Firewall do Windows para permitir acesso ao SQL Server (a página pode estar em inglês)](http://msdn.microsoft.com/library/cc646023.aspx).


### Configurar o SQL Server para escutar no protocolo TCP

1. Enquanto conectado à máquina virtual, na página inicial, digite **SQL Server Configuration Manager** e pressione ENTER.
	
	![Abrir SSCM](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. No SQL Server Configuration Manager, no painel do console, expanda **Configuração de Rede do SQL Server**.

3. No painel de console, clique em **Protocolos para MSSQLSERVER** (ele padroniza o nome da instância). No painel de detalhes, clique com o botão direito do mouse em TCP, por padrão, ele deve estar habilitado para as imagens da Galeria. Para suas imagens personalizadas, clique em **Habilitar** (se o status for Desabilitado.)

	![Habilitar TCP](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. No painel do console, clique em **Serviços do SQL Server**. No painel de detalhes, clique com o botão direito do mouse em **SQL Server (_instance name_)** (a instância padrão é **SQL Server (MSSQLSERVER)**) e, em seguida, clique em **Reiniciar**, para parar e reiniciar a instância do SQL Server.

	![Reiniciar o Mecanismo de Banco de Dados](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. Feche o SQL Server Configuration Manager.

Para obter mais informações sobre como habilitar protocolos para o Mecanismo de Banco de Dados do SQL Server, consulte [Habilitar ou desabilitar um protocolo de rede de servidor (a página pode estar em inglês)](http://msdn.microsoft.com/library/ms191294.aspx).

### Configurar o SQL Server para autenticação de modo misto

O Mecanismo de Banco de Dados do SQL Server não pode usar a Autenticação do Windows sem um ambiente de domínio. Para conectar-se ao Mecanismo de Banco de Dados de outro computador, configure o SQL Server para a autenticação de modo misto. A autenticação de modo misto permite a Autenticação do SQL Server e a Autenticação do Windows.

>[AZURE.NOTE]A configuração da autenticação de modo misto pode não ser necessária se você tiver configurado uma Rede Virtual do Azure com um ambiente de domínio configurado.

1. Enquanto conectado à máquina virtual, na página inicial, digite **SQL Server 2014 Management Studio** e clique no ícone selecionado.

	![Iniciar o SSMS](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

	Na primeira vez que você abrir o Management Studio ele deve criar o ambiente do Management Studio dos usuários. Isso pode demorar alguns instantes.

2. O Management Studio apresenta a caixa de diálogo **Conectar ao Servidor**. Na caixa **Nome do servidor**, digite o nome da máquina virtual para conectar-se ao Mecanismo de Banco de Dados com o Object Explorer. (Em vez do nome da máquina virtual, também é possível usar **(local)** ou um único ponto como o **Nome do Servidor**. Selecione **Autenticação do Windows** e deixe **_your\_VM\_name_\\your\_local\_administrator** na caixa **Nome de usuário**. Clique em **Conectar**.

	![Conectar-se ao servidor](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. No SQL Server Management Studio Object Explorer, clique com o botão direito do mouse no nome da instância do SQL Server (o nome da máquina virtual) e, em seguida, clique em **Propriedades**.

	![Propriedades do servidor](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. Na página **Segurança**, em **Autenticação do Servidor**, selecione **SQL Server e Modo de Autenticação do Windows** e, em seguida, clique em **OK**.

	![Selecionar modo de autenticação](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. Na caixa de diálogo do SQL Server Management Studio, clique em **OK** para confirmar a necessidade de reiniciar o SQL Server.

6. No Object Explorer, clique com o botão direito do mouse no seu servidor, e em seguida, clique em **Reiniciar**. (Se o SQL Server Agent estiver em execução, ele também deverá ser reinicializado.)

	![Reiniciar](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. Na caixa de diálogo do SQL Server Management Studio, clique em **Sim** para concordar que você quer reiniciar o SQL Server.

### Criar logons de autenticação do SQL Server

Para conectar-se ao Mecanismo de Banco de Dados de outro computador, você deve criar pelo menos um logon de autenticação do SQL Server.

1. No SQL Server Management Studio Object Explorer, expanda a pasta da instância do servidor na qual você deseja criar o novo logon.

2. Clique com o botão direito do mouse na pasta **Segurança**, aponte para **Novo** e selecione **Logon...**.

	![Novo Logon](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. Na caixa de diálogo **Logon - Novo**, na página **Geral**, digite o nome do novo usuário na caixa **Nome de Logon**.

4. Selecione **Autenticação do SQL Server**.

5. Na caixa **Senha**, digite uma senha para o novo usuário. Insira novamente essa senha na caixa **Confirmar Senha**.

6. Para impor as opções de complexidade e imposição da política de senha, selecione **Impor Política de Senha** (recomendado). Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

7. Para impor as opções de expiração da política de senha, selecione **Impor Expiração de Senha** (recomendado). A opção Impor a Política de Senha deve estar selecionada para ativar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

8. Para forçar o usuário a criar uma nova senha após a primeira vez que o logon é usado, selecione **O usuário deve alterar a senha no próximo logon** (recomendado se esse logon for para mais alguém usar. Se o logon for para seu próprio uso, não selecione essa opção.) A opção Impor a Expiração de Senha deve estar selecionada para ativar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

9. Na lista **Banco de Dados Padrão**, selecione um banco de dados padrão para o logon. **mestre** é o padrão para essa opção. Se você ainda não tiver criado um usuário de banco de dados, mantenha essa definição como **mestre**.

10. Na lista **Idioma padrão**, mantenha **padrão** como o valor.
    
	![Propriedades de Logon](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. Se esse for o primeiro logon que você está criando, talvez você queira designá-lo como um administrador do SQL Server. Em caso afirmativo, na página **Funções de Servidor**, marque **sysadmin**.

	**Observação sobre segurança:** os membros da função de servidor fixa sysadmin tem controle total sobre o Mecanismo de Banco de Dados. Você deve restringir cuidadosamente a associação nessa função.

	![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. Clique em OK.

Para obter mais informações sobre logons do SQL Server, consulte [Criar um logon (a página pode estar em inglês)](http://msdn.microsoft.com/library/aa337562.aspx).

### Determinar o nome DNS da máquina virtual

Para conectar-se ao Mecanismo de Banco de Dados do SQL Server em outro computador, você deve saber o nome DNS (Sistema de Nome de Domínio) da máquina virtual. (Esse é o nome que a Internet usa para identificar a máquina virtual. Você pode usar o endereço IP, mas o endereço IP pode ser alterado quando o Azure mover os recursos para redundância ou manutenção. O nome DNS será estável porque pode ser redirecionado para um novo endereço IP.)

1. No Portal de Gerenciamento do Azure (ou na etapa anterior), selecione **MÁQUINAS VIRTUAIS**. 

2. Na página **INSTÂNCIAS DA MÁQUINA VIRTUAL** na coluna **Visão rápida**, localize e copie o nome DNS para a máquina virtual.

	![Nome DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### Conectar-se ao Mecanismo de Banco de Dados de outro computador
 
1. Em um computador conectado à Internet, abra o SQL Server Management Studio.
2. Na caixa de diálogo **Conectar ao Servidor** ou **Conectar ao Mecanismo de Banco de Dados**, na caixa **Nome do servidor**, digite o nome DNS da máquina virtual (determinado na tarefa anterior) e um número da porta pública de ponto de extremidade no formato *NomeDNS,númerodaporta*, como **tutorialtestVM.cloudapp.net,57500**. Para obter o número da porta, faça logon no Portal de Gerenciamento do Azure e encontre a Máquina Virtual. No painel, clique em **PONTOS DE EXTREMIDAD**E e use a **PORTA PÚBLICA** atribuída ao **MSSQL**. ![Porta pública](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.
5. Na caixa **Logon**, digite o nome de um logon que você criou em uma tarefa anterior.
6. Na caixa **Senha**, digite a senha do logon que você criou em uma tarefa anterior.
7. Clique em **Conectar**.

	![Conectar-se usando SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=August15_HO8-->