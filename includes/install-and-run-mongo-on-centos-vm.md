Siga estas etapas para instalar e executar o MongoDB em uma máquina virtual executando o CentOS Linux.

> [AZURE.WARNING]Os recursos de segurança do MongoDB, como autenticação e associação com o endereço IP, não são habilitados por padrão. Os recursos de segurança devem ser ativados antes de implantar o MongoDB em um ambiente de produção. Consulte [Segurança e autenticação](http://www.mongodb.org/display/DOCS/Security+and+Authentication) para obter mais informações.

1. Configure o Sistema de Gerenciamento de Pacote (YUM) para que você possa instalar o MongoDB. Crie um */etc/yum.repos.d/10gen.repo* arquivo para armazenar informações sobre o repositório e adicione o seguinte:

		[10gen]
		name=10gen Repository
		baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
		gpgcheck=0
		enabled=1

2. Salve o arquivo repo e, em seguida, execute o seguinte comando para atualizar o banco de dados do pacote local:

		$ sudo yum update

3. Para instalar o pacote, execute o comando a seguir para instalar a última versão estável do MongoDB e as ferramentas associadas:

		$ sudo yum install mongo-10gen mongo-10gen-server

	Aguarde enquanto o MongoDB é baixado e instalado.

4. Crie um diretório de dados. Por padrão, o MongoDB armazena dados no diretório */data/db*, mas você deve criar esse diretório. Para criá-lo, execute:

		$ sudo mkdir -p /srv/datadrive/data
		$ sudo chown `id -u` /srv/datadrive/data

	Para obter mais informações sobre como instalar o MongoDB no Linux, consulte [Quickstart Unix][QuickstartUnix].

5. Para iniciar o banco de dados, execute:

		$ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

	Todas as mensagens de log serão direcionadas para o arquivo */srv/datadrive/data/mongod.log*, quando o servidor MongoDB for iniciado e pré-alocar arquivos de diário. Pode levar alguns minutos para que o MongoDB pré-aloque os arquivos de diário e comece a detectar conexões.

6. Para iniciar o shell administrativo do MongoDB, abra uma janela separada do SSH ou PuTTY e execute:

		$ mongo
		> db.foo.save ( { a:1 } )
		> db.foo.find()
		{ _id : ..., a : 1 }
		> show dbs  
		...
		> show collections  
		...  
		> help  

	O banco de dados é criado pelo comando insert.

7. Depois que o MongoDB for instalado, você deverá configurar um ponto de extremidade para que o MongoDB possa ser acessado remotamente. No Portal de Gerenciamento, clique em **Máquinas Virtuais**, em seguida clique no nome da sua nova máquina virtual e depois clique em **Pontos de extremidade**.
	
	![Pontos de extremidade][Image7]

8. Clique em **Adicionar Ponto de Extremidade** na parte inferior da página.
	
	![Pontos de extremidade][Image8]

9. Adicione um ponto de extremidade com as configurações a seguir:

 - **Nome**: Mongo
 - **Protocolo**: TCP
 - **Porta pública**: 27017
 - **Porta privada**: 27017
 
 Isso permitirá que o MongoDB seja acessado remotamente.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png

<!--HONumber=52-->
