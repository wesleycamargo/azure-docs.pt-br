Siga estas etapas para instalar e executar o MongoDB em uma máquina virtual que executa o Windows Server.

> [AZURE.IMPORTANTE] recursos de segurança do MongoDB, como autenticação e o endereço IP associação, não são habilitados por padrão. Os recursos de segurança devem ser habilitad antes de implantar o MongoDB em um ambiente de produção.  Consulte [Segurança e autenticação](http://www.mongodb.org/display/DOCS/Security+and+Authentication) para obter mais informações.

1. Depois de se conectar à máquina virtual usando a Área de Trabalho Remota, abra o Internet Explorer no menu **Iniciar**.
2. Selecione o botão **Ferramentas** no canto superior direito.  Em **Opções da Internet**, selecione a guia **Segurança**, em seguida selecione o ícone **Sites confiáveis** e, por fim, clique no botão **Sites**. Adicione *http://\*.mongodb.org* à lista de sites confiáveis.
3. Acesse [Downloads- MongoDB] [MongoDownloads].
4. Localize a versão mais recente na seção **Versão de produção (recomendada)** e clique no link ***2008+** na coluna Windows 64 bits.  Clique em **Salvar como** e salve o arquivo zip no desktop.
5. Clique com botão direito no arquivo zip e selecione **Extrair tudo...**  Especifique "C:\" e clique em **Extrair**.  Depois que os arquivos forem extraídos, você poderá renomear a pasta de instalação para algo mais simples.  Como "MongoDB", por exemplo.
6. Crie dados MongoDB e diretórios de log no disco de dados (unidade **F:**, por exemplo), que você criou nas etapas acima. No menu **Iniciar**, selecione **Prompt de Comando** para abrir uma janela do prompt de comando.  Digite:

		C:\> F:
		F:\> mkdir \MongoData
		F:\> mkdir \MongoLogs

7. Para executar o banco de dados, execute: 

		F:\> C:
		C:\> cd \MongoDB\bin
		C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

	Todas as mensagens de log serão direcionadas para o arquivo *F:\MongoLogs\mongolog.log* quando o servidor mongod.exe for iniciado e pré-alocar arquivos de diário. Pode levar alguns minutos para que o MongoDB pré-aloque os arquivos de diário e comece a detectar conexões.

8. Para iniciar o shell administrativo MongoDB, abra outra janela de comando em **Iniciar** e digite o seguinte:

		C:\> cd \my_mongo_dir\bin  
		C:\my_mongo_dir\bin> mongo  
		>db  
		test  	  
		> db.foo.insert( { a : 1 } )  
		> db.foo.find()  
		{ _id : ..., a : 1 }  
		> show dbs  
		...  
		> show collections  
		...  
		> help  

	O banco de dados é criado pelo comando insert.
9. O mongod.exe (opcional) dá suporte para instalação e execução como um serviço Windows. Para instalar o mongod.exe como um serviço, execute o seguinte no prompt de comando:

		C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

	Isso cria um serviço chamado "Mongo DB" com uma descrição de "Mongo DB". A opção **--logpath** deve ser usada para especificar um arquivo de log, uma vez que o serviço em execução não terá uma janela de comando para exibir a saída.  A opção **--logpath** especifica que uma reinicialização do serviço fará com que a saída anexe o arquivo de log existente.  A opção **--dbpath** especifica o local do diretório de dados. Para obter mais opções de linha de comando relacionadas ao serviço, consulte [Opções da linha de comando relacionadas ao serviço] [MongoWindowsSvcOptions].
10. Agora que o MongoDB está instalado e em execução, você precisa abrir uma porta no Firewall do Windows para poder se conectar remotamente ao MongoDB.  No menu **Iniciar**, selecione **Ferramentas Administrativas** e depois **Firewall do Windows com segurança avançada**. 

11. No painel esquerdo, selecione **Regras de Entrada**.  À direita, no painel **Ações**, selecione **Nova Regra...**.
	
	![Windows Firewall][Image1]

	No **Assistente para Nova Regra de Entrada**, selecione **Porta** e clique em **Avançar**.
	
	![Windows Firewall][Image2]

	Selecione **TCP** e **Portas locais específicas**.  Especifique uma porta de "27017" (a porta MongoDB escuta) e clique em **Avançar**.

	![Windows Firewall][Image3]

	Selecione **Permitir a conexão** e clique em **Avançar**.

	![Windows Firewall][Image4]

	Clique em **Avançar** novamente.
	
	![Windows Firewall][Image5]

	Especifique um nome para a regra, como "MongoPort", e clique em **Concluir**.

	![Windows Firewall][Image6]
	
12. Se você não configurou um ponto de extremidade para o MongoDB quando criou a máquina virtual, pode fazer isso agora. Você precisa do ponto de extremidade e da regra de firewall para ser capaz de se conectar remotamente ao MongoDB. No Portal de gerenciamento, clique em **Máquinas virtuais**, clique no nome de sua nova máquina virtual e clique em **Pontos de extremidade**.

	![Endpoints][Image7]
13. Clique em **Adicionar Ponto de Extremidade** na parte inferior da página. Selecione **Adicionar Ponto de Extremidade** e clique em **Avançar**.
	
	![Endpoints][Image8]

14. Adicione um ponto de extremidade com o nome "Mongo", protocolo **TCP** e ambas as portas **Pública** e **Privada** configuradas para "27017". Isso permitirá que o MongoDB seja acessado remotamente.

	![Endpoints][Image9]

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png

<!--HONumber=45--> 
