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

<!---HONumber=AcomDC_0107_2016-->