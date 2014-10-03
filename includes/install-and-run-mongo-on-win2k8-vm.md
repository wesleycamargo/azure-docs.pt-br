Siga estas etapas para instalar e executar o MongoDB em uma máquina virtual que executa o Windows Server.

<div class="dev-callout">
<b>Importante</b>
<p>Os recursos de seguran&ccedil;a do MongoDB, como autentica&ccedil;&atilde;o e associa&ccedil;&atilde;o com o endere&ccedil;o IP, n&atilde;o s&atilde;o habilitados por padr&atilde;o. Os recursos de seguran&ccedil;a devem ser ativados antes de implantar o MongoDB em um ambiente de produ&ccedil;&atilde;o.  Consulte <a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">Seguran&ccedil;a e autentica&ccedil;&atilde;o</a> para obter mais informa&ccedil;&otilde;es.</p>
</div>

1. Depois de se conectar à máquina virtual usando a Área de Trabalho Remota, abra o Internet Explorer a partir do menu **Iniciar**.
2. Selecione o botão **Ferramentas** no canto superior direito. Em **Opções da Internet**, selecione a guia **Segurança**, em seguida selecione o ícone **Sites confiáveis** e, por fim, clique no botão **Sites**. Adicione *http://\*.mongodb.org* à lista de sites confiáveis.
3. Acesse [Downloads- MongoDB][].
4. Localize a versão mais recente na seção **Production Release (Recommended) (Versão de produção [recomendada])** e clique no link ***2008+** na coluna Windows 64-bit (Windows 64 bits). Clique em **Salvar como** e salve o arquivo zip no desktop.
5.  Clique com botão direito no arquivo zip e selecione **Extrair tudo...** Especifique "C:" e clique em **Extrair**. Depois que os arquivos forem extraídos, você poderá renomear a pasta de instalação para algo mais simples. Como "MongoDB", por exemplo.
6.  Crie diretórios de dados e de log do MongoDB no disco de dados (unidade **F:**, por exemplo) criado nas etapas acima. No menu **Iniciar**, selecione **Prompt de Comando** para abrir uma janela do prompt de comando. Tipo:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs

7.  Para executar o banco de dados, execute:

        F:\> C:
        C:\> cd \MongoDB\bin
        C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Todas as mensagens de log serão direcionadas ao arquivo *F:\\MongoLogs\\mongolog.log* quando o servidor mongod.exe for iniciado e pré-alocar arquivos de diário. Pode levar alguns minutos para que o MongoDB pré-aloque os arquivos de diário e comece a detectar conexões.

8.  Para iniciar o shell administrativo do MongoDB, abra outra janela Comando no menu **Iniciar** e digite:

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

9.  O mongod.exe (opcional) oferece suporte para instalação e execução como um serviço do Windows. Para instalar o mongod.exe como um serviço, execute o seguinte no prompt de comando:

        C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

    Isso cria um serviço chamado "Mongo DB" com uma descrição de "Mongo DB". A opção **--logpath** deve ser usada para especificar um arquivo de log, uma vez que o serviço em execução não terá uma janela Comando para exibir a saída. A opção **--logpath** especifica que uma reinicialização do serviço fará com que a saída seja acrescentada ao arquivo de log existente. A opção **--dbpath** especifica o local do diretório de dados. Para obter mais opções de linha de comando relacionadas ao serviço, consulte [Opções de linha de comando relacionadas ao serviço][].

10. Agora que o MongoDB está instalado e em execução, você precisa abrir uma porta no Firewall do Windows para poder se conectar remotamente ao MongoDB. No menu **Iniciar**, selecione **Ferramentas Administrativas** e depois **Firewall do Windows com segurança avançada**.

11. No painel esquerdo, selecione **Regras de Entrada**. À direita, no painel **Ações**, selecione **Nova Regra...**.

    ![Firewall do Windows][]

    No **Assistente para Nova Regra de Entrada**, selecione **Porta** e clique em **Avançar**.

    ![Firewall do Windows][1]

    Selecione **TCP** e **Portas locais específicas**. Especifique uma porta de "27107" (a porta que o MongoDB escuta) e clique em **Avançar**.

    ![Firewall do Windows][2]

    Selecione **Permitir a conexão** e clique em **Avançar**.

    ![Firewall do Windows][3]

    Clique em **Avançar** novamente.

    ![Firewall do Windows][4]

    Especifique um nome para a regra, como "MongoPort", e clique em **Concluir**.

    ![Firewall do Windows][5]

12. Se você não configurou um ponto de extremidade para o MongoDB quando criou a máquina virtual, pode fazer isso agora. Você precisa do ponto de extremidade e da regra de firewall para ser capaz de se conectar remotamente ao MongoDB. No Portal de Gerenciamento, clique em **Máquinas Virtuais**, em seguida clique no nome da sua nova máquina virtual e em **Pontos de Extremidade**.

    ![Pontos de extremidade][]

13. Clique em **Adicionar Ponto de Extremidade** na parte inferior da página. Selecione **Adicionar Ponto de Extremidade** e clique em **Avançar**.

    ![Pontos de extremidade][6]

14. Adicione um ponto de extremidade com o nome "Mongo", protocolo **TCP** e defina as portas **Pública** e **Privada** como "27017". Isso permitirá que o MongoDB seja acessado remotamente.

    ![Pontos de extremidade][7]

  [Segurança e autenticação]: http://www.mongodb.org/display/DOCS/Security+and+Authentication
  [Downloads- MongoDB]: http://www.mongodb.org/downloads
  [Opções de linha de comando relacionadas ao serviço]: http://www.mongodb.org/display/DOCS/Windows+Service
  [Firewall do Windows]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
  [1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
  [2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
  [3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
  [4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
  [5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
  [Pontos de extremidade]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
  [6]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
  [7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png
