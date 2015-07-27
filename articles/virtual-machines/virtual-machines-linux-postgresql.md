<properties
	pageTitle="Instalar e configurar PostgreSQL em uma máquina virtual do Microsoft Azure executando Linux"
	description="Saiba como instalar e configurar PostgreSQL em uma máquina virtual Ubuntu ou CentOS (VM) no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
  tags=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="linux"
	ms.workload="infrastructure-services"
	ms.date="06/04/2015"
	ms.author="mingzhan"/>


#Instalar e configurar PostgreSQL no Microsoft Azure

PostgreSQL é um banco de dados avançado de código aberto semelhante ao Oracle e DB2. Ele inclui recursos corporativos como conformidade total com ACID, processamento transacional confiável e controle de simultaneidade de várias versões. Também oferece suporte a padrões como ANSI SQL e SQL/MED (inclusive wrappers de dados externos para Oracle, MySQL, MongoDB e muitos outros). Ele é altamente extensível com suporte para mais de 12 idiomas procedurais, índices GIN e GIST, dados espaciais e vários recursos como NoSQL para aplicativos JSON ou de chave-valor.

Neste artigo, você aprenderá a instalar e configurar PostgreSQL em uma máquina virtual Azure que executa o Linux.

> [Azure.NOTE]Você já deve ter uma máquina virtual do Microsoft Azure executando o Linux para concluir este tutorial. Consulte o [tutorial de VM Linux do Azure](virtual-machines-linux-tutorial.md) para criar e configurar uma VM Linux antes de continuar.

[Neste caso, use a porta 1999 como a porta PostgreSQL].

## Instalar o PostgreSQL

Conecte-se à VM Linux criada via putty. Se esta for a primeira vez que você usa a VM Linux Azure, consulte como usar putty para se conectar a uma VM Linux [aqui](virtual-machines-linux-use-ssh-key.md).

1. Execute o seguinte comando para alternar para a raiz (admin):

		$ sudo su -

2. Algumas distribuições têm dependências que devem ser instaladas antes de instalar o PostgreSQL. Verifique sua distribuição nesta lista e execute o comando apropriado:

	- Redhat:

			# yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

	- Debian:

 			# apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

	- Suse:

			# zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Baixe o PostgreSQL no diretório raiz e descompacte o pacote:

		# wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

		# tar jxvf  postgresql-9.3.5.tar.bz2

	Um exemplo foi mostrado acima. Encontre o endereço de download mais detalhado [aqui](https://ftp.postgresql.org/pub/source/).

4. Para iniciar a compilação, execute estes comandos:

		# cd postgresql-9.3.5

		# ./configure --prefix=/opt/postgresql-9.3.5

5. Se você quiser criar tudo o que for possível, incluindo a documentação (páginas HTML e man) e módulos adicionais (contribuidor), execute o seguinte comando:

		# gmake install-world

	Você deverá receber a seguinte mensagem de confirmação:

		PostgreSQL, contrib, and documentation successfully made. Ready to install.

## Configurar PostgreSQL

1. (Opcional) Crie um link simbólico para encurtar a referência ao PostgreSQL a fim de não incluir o número de versão:

		# ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Crie um diretório para o banco de dados:

		# mkdir -p /opt/pgsql_data

3. Crie um usuário não raiz e modifique o perfil do usuário. Em seguida, alterne para esse novo usuário (chamado *postgres* em nosso exemplo):

		# useradd postgres

		# chown -R postgres.postgres /opt/pgsql_data

		# su - postgres

    >[Azure.NOTE]Por motivos de segurança, o PostgreSQL usa um usuário não raiz para inicializar, iniciar ou desligar o banco de dados.


4. Edite o *perfil_bash* inserindo os comandos a seguir. Essas linhas serão adicionadas ao final do arquivo *perfil_bash*:

		cat >> ~/.bash_profile <<EOF
		export PGPORT=1999
		export PGDATA=/opt/pgsql_data
		export LANG=en_US.utf8
		export PGHOME=/opt/pgsql
		export PATH=\$PATH:\$PGHOME/bin
		export MANPATH=\$MANPATH:\$PGHOME/share/man
		export DATA=`date +"%Y%m%d%H%M"`
		export PGUSER=postgres
		alias rm='rm -i'
		alias ll='ls -lh'
		EOF

5. Execute o arquivo *perfil_bash*:

		$ source .bash_profile

6. Valide a instalação com o seguinte comando:

		$ which psql

	Se a instalação for bem-sucedida, você verá a seguinte resposta:

		/opt/pgsql/bin/psql

7. Você também pode verificar a versão do PostgreSQL:

		$ psql -V

8. Inicialize o banco de dados:

		$ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

	Você deverá receber o seguinte resultado:

![imagem](./media/virtual-machines-linux-postgresql/no1.png)

## Configurar o PostgreSQL

<!--	[postgres@ test ~]$ exit -->

Execute os seguintes comandos:

	# cd /root/postgresql-9.3.5/contrib/start-scripts

	# cp linux /etc/init.d/postgresql

Modifique duas variáveis no arquivo /etc/init.d/postgresql. O prefixo é definido como o caminho de instalação do PostgreSQL: **/opt/pgsql**. PGDATA é definido como o caminho de armazenamento de dados do PostgreSQL: **/opt/pgsql_data**.

	# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

	# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![imagem](./media/virtual-machines-linux-postgresql/no2.png)

Altere o arquivo para torná-lo executável:

	# chmod +x /etc/init.d/postgresql

Inicie o PostgreSQL:

	# /etc/init.d/postgresql start

Verifique se o ponto de extremidade do PostgreSQL está em:

	# netstat -tunlp|grep 1999

Você deve ver o seguinte resultado:

![imagem](./media/virtual-machines-linux-postgresql/no3.png)

## Conectar-se ao banco de dados Postgres

Vá em frente e alterne para o usuário do postgres novamente:

	# su - postgres

Crie um banco de dados Postgres:

	$ createdb events

Conecte-se ao banco de dados de eventos recém-criado:

	$ psql -d events

## Como criar e excluir uma tabela Postgres

Agora que estamos conectados ao banco de dados, podemos criar tabelas nele.

Por exemplo, crie um novo exemplo de tabela Postgres com o seguinte comando:

	CREATE TABLE potluck (name VARCHAR(20),	food VARCHAR(30),	confirmed CHAR(1), signup_date DATE);

Configuramos uma tabela com 4 colunas com os seguintes nomes de coluna e restrições:

1. A coluna "nome" foi limitada pelo comando VARCHAR para ter menos de 20 caracteres.
2. A coluna "comida" indica a comida que cada pessoa trará. VARCHAR limita esse texto a menos de 30 caracteres.
3. A coluna "confirmada" registra se a pessoa confirmou a presença no jantar. Os valores aceitáveis são "S" e "N".
4. A coluna "data" mostrará quando as pessoas se inscreveram no evento. Postgres exige que as datas sejam gravadas como aaaa-mm-dd.

Se a tabela a seguir tiver sido criada com êxito, você deverá ver o seguinte:

![imagem](./media/virtual-machines-linux-postgresql/no4.png)

Você também pode verificar a estrutura da tabela com o seguinte comando:

![imagem](./media/virtual-machines-linux-postgresql/no5.png)

### Adicionar dados a uma tabela

Primeiro, insira as informações em uma linha:

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Você deverá ver este resultado:

![imagem](./media/virtual-machines-linux-postgresql/no6.png)

É possível adicionar algumas pessoas à tabela. Aqui estão algumas opções, ou você pode criar as suas próprias:

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### Mostrar tabelas

Use o seguinte comando para mostrar uma tabela:

	select * from potluck;

A saída é:

![imagem](./media/virtual-machines-linux-postgresql/no7.png)

### Excluir dados de uma tabela

Use o seguinte comando para excluir dados de uma tabela:

	delete from potluck where name=’John’;

Isso excluirá todas as informações da linha "Pedro". A saída é:

![imagem](./media/virtual-machines-linux-postgresql/no8.png)

### Atualizar dados em uma tabela

Use o seguinte comando para atualizar dados em uma tabela. Para este, Brenda confirmou que participará, portanto, alteraremos a confirmação dela de "N" para "S":

 	UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##Mais sobre PostgreSQL
Você concluiu a instalação do PostgreSQL na VM Linux do Microsoft Azure. Agora, aproveite sua jornada para usá-lo no Microsoft Azure. Mais sobre o PostgreSQL, clique [aqui](http://www.postgresql.org/).

<!---HONumber=July15_HO2-->