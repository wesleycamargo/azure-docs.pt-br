
1. Para escalonar privilégios, execute:

		sudo -s
	
	Digite sua senha.

2. Execute o seguinte comando para instalar o MySQL Community Server edition:

		# zypper install mysql-community-server

	Aguarde enquanto o MySQL é baixado e instalado.
3. Para definir o MySQL para iniciar quando o sistema for inicializado, execute o seguinte comando:

		# insserv mysql
4. Agora você pode iniciar manualmente o daemon do MySQL (mysqld) com o seguinte comando:

		# rcmysql start

	Para verificar o status do daemon do MySQL, execute:

		# rcmysql status

	Se você deseja parar o daemon do MySQL, execute:

		# rcmysql stop

5. Aviso! Após a instalação, a senha da raiz do MySQL é vazia por padrão. É recomendável executar o **mysql_secure_installation**, um script que ajuda a proteger o MySQL. Ao executar o **mysql_secure_installation**, você será solicitado a alterar a senha raiz do MySQL, remover as contas anônimas de usuários, desabilitar os logins de raiz remota, remover bancos de dados de teste e recarregar a tabela de privilégios. É recomendável que você responda Sim para todas essas opções e altere a senha raiz. Execute o comando a seguir para executar o script:

		$ mysql_secure_installation

6. Após a execução, você poderá fazer logon no MySQL:

		$ mysql -u root -p

	Digite a senha raiz de MySQL (que você alterou na etapa anterior) e será exibido um prompt onde você poderá executar instruções SQL para interagir com o banco de dados.

7. Para criar um novo usuário do MySQL, execute o seguinte no prompt **mysql>**:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Observe que o ponto e vírgula (;) no final das linhas são cruciais para encerrar os comandos.

8. Para criar um banco de dados e conceder as `mysqluser` permissões de usuário a ele, emita os seguintes comandos:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Observe que as senhas e nomes de usuário do banco de dados só são usadas pelos scripts de conexão com o banco de dados. Nomes de conta de usuário do banco de dados não representam, necessariamente, contas de usuário reais no sistema.

9. Para fazer logon em outro computador, execute o seguinte:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	em que `ip-address` é o endereço IP do computador do qual você se conectará ao MySQL.
	
10. Para sair do utilitário de administração de banco de dados MySQL, emita o seguinte comando:

		quit

11. Após o MySQL estar instalado, será necessário configurar um ponto de extremidade para que o MySQL possa ser acessado remotamente. Faça logon no [Portal de Gerenciamento do Azure][AzurePreviewPortal]. No portal do Azure, clique em **Máquinas Virtuais**, clique no nome de sua nova VM e clique em **Pontos de extremidade**.

	![Endpoints][Image7]

12. Clique em **Adicionar** na parte inferior da página. ![Endpoints][Image8]

13. Adicione um ponto de extremidade chamado "MySQL" com protocolo **TCP** e portas **Pública** e **Privada** configuradas como "3306". Isso permite o acesso remoto ao MySQL. ![Pontos de extremidade][Image9]

14. Para se conectar remotamente ao MySQL em execução na máquina virtual OpenSUSE no Azure, execute o seguinte comando no computador local:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Por exemplo, usando a máquina virtual criada neste tutorial, o comando seria:

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. Você configurou com êxito o MySQL, criou um banco de dados e um novo usuário. Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!---HONumber=July15_HO2-->