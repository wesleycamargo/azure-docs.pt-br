<properties
   pageTitle="Usar chaves SSH com Hadoop no HDInsight baseado em Linux do Unix, Linux ou OS X | Azure"
   description="Saiba como criar e usar chaves SSH para autenticar em clusters HDInsight baseados em Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/20/2015"
   ms.author="larryfr"/>

#Usar SSH com Hadoop baseado em Linux no HDInsight por meio de Linux, Unix ou OS X (visualização)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Os clusters Azure HDInsight baseados em Linux oferecem a opção de usar acesso SSH por meio de uma senha ou chave SSH. Este documento fornece informações sobre como usar SSH com o HDInsight de clientes Linux, Unix ou OS X.

> [AZURE.NOTE]As etapas neste artigo presumem que você esteja usando um cliente Linux, Unix ou OS X. Embora essas etapas possam ser executadas em um cliente baseado em Windows, se você tiver instalado um pacote que forneça `ssh` e `ssh-keygen` (como o Git para Windows), nós recomendamos que os clientes baseados em Windows sigam as etapas em [Usar SSH com o HDInsight baseado em Linux (Hadoop) no Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##Pré-requisitos

* **ssh-keygen** e **ssh** para clientes Linux, Unix e OS X. Esse utilitários geralmente são fornecidos com o sistema operacional ou disponibilizados pelo sistema de gerenciamento de pacotes.

* Um navegador da Web moderno, com suporte a HTML5.

OU

* [CLI do Azure para Mac, Linux e Windows](xplat-cli.md).

##O que é o SSH?

SSH é um utilitário para efetuar login e executar remotamente comandos em um servidor remoto. Com o HDInsight baseado em Linux, o SSH estabelece uma conexão criptografada para o nó de cabeçalho do cluster e fornece uma linha de comando que você usa para digitar comandos. Os comandos são executados diretamente no servidor.

##Criar uma chave SSH (opcional)

Ao criar um cluster HDInsight baseado em Linux, você tem a opção de usar uma senha ou uma chave SSH para autenticação no servidor, quando utiliza SSH. Chaves SSH são consideradas mais seguras, porque elas são baseadas em certificado. Use as informações a seguir se você planeja usar chaves SSH com o cluster.

1. Abra uma sessão de terminal e use o comando a seguir para ver se há alguma chave SSH existente:

		ls -al ~/.ssh

	Procure os arquivos a seguir na listagem de diretórios. Esses são os nomes comuns de chaves públicas de SSH.

	* id_dsa.pub
	* id_ecdsa.pub
	* id_ed25519.pub
	* id_rsa.pub

2. Se não quiser usar um arquivo existente, ou se não existir nenhuma chave SSH, use este comando para gerar um novo arquivo:

		ssh-keygen -t rsa

	Você será solicitado pelas seguintes informações:

	* O local do arquivo: o local padrão é \~/.ssh/id_rsa.
	* Uma senha: será solicitado que você a digite novamente.

		> [AZURE.NOTE]É altamente recomendável que você use uma senha segura para a chave. No entanto, se você esquecer a senha, não é possível recuperá-la.

	Após a conclusão do comando, você terá dois novos arquivos: a chave privada (por exemplo, **id_rsa**) e a chave pública (por exemplo, **id_rsa.pub**).

##Criar um cluster HDInsight baseado em Linux

Ao criar um cluster HDInsight baseado em Linux, você deve fornecer a chave pública criada anteriormente. Para clientes Linux, Unix ou OS X, há duas maneiras de criar um cluster HDInsight:

* **Portal do Azure**: usa um portal baseado na Web para criar o cluster.

* **CLI do Azure para Mac, Linux e Windows**: usa comandos de linha de comando para criar o cluster.

Cada um desses métodos exigirá uma senha ou uma chave pública. Para obter informações completas sobre como criar um cluster HDInsight baseado em Linux, consulte <a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisionar clusters HDInsight baseados em Linux</a>.

###Portal do Azure

Ao usar o portal para criar um cluster HDInsight baseado em Linux, você deve inserir um **NOME DE USUÁRIO SSH** e optar por inserir uma **SENHA** ou **CHAVE PÚBLICA SSH**. Se você selecionar **CHAVE PÚBLICA SSH**, deverá colar a chave pública (contida no arquivo com a extensão **.pub**) no formulário abaixo:

![Imagem de formulário solicitando uma chave pública](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE]O arquivo de chave é simplesmente um arquivo de texto. O conteúdo deve se semelhar a isto: ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Isso cria um logon para o usuário especificado, usando a senha ou a chave pública que você fornecer.

###Interface de linha de comando do Azure para Mac, Linux e Windows

Você pode usar a [CLI do Azure para Mac, Linux e Windows](xplat.md) para criar um novo cluster usando o comando `azure hdinsight cluster create`.

Para obter mais informações sobre como usar esse comando, consulte <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisionar clusters Hadoop em Linux no HDInsight usando opções personalizadas</a>.

##Conectar-se a um cluster HDInsight baseado em Linux

Em uma sessão de terminal, use o comando SSH para se conectar ao nó de cabeçalho do cluster, fornecendo o endereço e o nome de usuário:

* **Endereço SSH**: o nome do cluster, seguido por **-ssh.azurehdinsight.net**. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

* **Nome de usuário**: o nome de usuário SSH que você forneceu ao criar o cluster.

O exemplo a seguir se conectará ao cluster **mycluster** como o usuário **me**:

	ssh me@mycluster-ssh.azurehdinsight.net

Se você usou uma senha para a conta de usuário, será solicitado a inserir a senha.

Se você usou uma chave SSH protegida por uma senha, você deverá inserir a senha. Caso contrário, o SSH tentará autenticar automaticamente usando uma das chaves privadas locais no cliente.

> [AZURE.NOTE]Se o SSH não autenticar automaticamente com a chave privada correta, use o parâmetro **-i** e especifique o caminho para a chave privada. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

###Conectar a nós de trabalho

Os nós de trabalho não são diretamente acessíveis de fora do datacenter do Azure, mas podem ser acessados do nó de cabeçalho do cluster via SSH.

Se usar uma chave SSH para autenticar a conta de usuário, você deverá concluir as seguintes etapas no cliente:

1. Usando um editor de texto, abra `~/.ssh/config`. Se esse arquivo não existir, você pode criá-lo digitando `touch ~/.ssh/config` no terminal.

2. Adicione o seguinte ao arquivo. Substitua *CLUSTERNAME* pelo nome do seu cluster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Isso configura o encaminhamento do agente SSH para o cluster HDInsight.

3. Teste o encaminhamento do agente SSH usando o seguinte comando no terminal:

        echo "$SSH_AUTH_SOCK"

    Isso deve retornar informações semelhantes a estas:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Se nada for retornado, isso indica que o **ssh-agent** não está em execução. Consulte a documentação do sistema operacional para ver as etapas específicas para instalar e configurar o **ssh-agent** ou consulte <a href="http://mah.everybody.org/docs/ssh" target="_blank">Usando ssh-agent com SSH</a>.

4. Após confirmar que o **ssh-agent** está em execução, use o seguinte para adicionar sua chave privada SSH ao agente:

        ssh-add ~/.ssh/id_rsa

    Se a chave privada estiver armazenada em um arquivo diferente, substitua `~/.ssh/id_rsa` pelo caminho para o arquivo.

Use as etapas a seguir para conectar-se a nós de trabalho de seu cluster.

> [AZURE.IMPORTANT]Se usar uma chave SSH para autenticar a conta, você deverá concluir as etapas anteriores para confirmar que o encaminhamento do agente está funcionando.

1. Conecte-se ao cluster HDInsight usando SSH, como descrito anteriormente.

2. Depois de conectado, use o seguinte para recuperar uma lista de nós no cluster. Substitua *ADMINPASSWORD* pela senha de sua conta de administrador do cluster. Substitua *CLUSTERNAME* pelo nome do cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Isso retornará informações em formato JSON sobre os nós no cluster, incluindo `host_name`, que contém o FQDN (nome de domínio totalmente qualificado) de cada nó. Veja a seguir um exemplo de uma entrada `host_name` retornada pelo comando **curl**:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Quando você tiver uma lista de nós de trabalho aos quais deseja se conectar, use o seguinte comando na sessão SSH para que o servidor abra uma conexão com um nó de trabalho:

        ssh USERNAME@FQDN

    Substitua *USERNAME* pelo nome de usuário SSH e *FQDN* pelo FQDN do nó de trabalho. Por exemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE]Se você usar uma senha para autenticação da sessão SSH, será solicitado que você digite a senha novamente. Se você usar uma chave SSH, a conexão deverá terminar sem prompts.

4. Quando a sessão for estabelecida, o prompt do terminal será alterado de `username@headnode` para `username@workernode` para indicar que você está conectado ao nó de trabalho. Todo comando executado neste momento será executado no nó de trabalho.

4. Quando terminar de executar ações no nó de trabalho, use o comando `exit` para fechar a sessão para o nó de trabalho. Você voltará para o prompt `username@headnode`.

##Adicionar mais contas

1. Gere uma nova chave pública e uma chave privada para a nova conta de usuário, como descrito na seção [Criar uma chave SSH](#create-an-ssh-key-optional).

	> [AZURE.NOTE]A chave privada deve ser gerada em um cliente que o usuário utilizará para se conectar ao cluster ou transferida com segurança para esse cliente após a criação.

1. Em uma sessão SSH para o cluster, adicione o novo usuário com o seguinte comando:

		sudo adduser --disabled-password <username>

	Isso criará uma nova conta de usuário, mas desativará a autenticação de senha.

2. Crie o diretório e os arquivos que armazenarão a chave, usando os seguintes comandos:

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Quando o editor nano for aberto, copie e cole nos conteúdo da chave pública para a nova conta de usuário. Por fim, use **Ctrl-X** para salvar o arquivo e sair do editor.

	![imagem do editor nano com um exemplo de chave](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Use o comando a seguir para alterar a propriedade da pasta .ssh e o conteúdo da nova conta de usuário:

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Agora você deve ser capaz de autenticar para o servidor com a nova conta de usuário e chave privada.

##<a id="tunnel"></a>Túnel SSH

O SSH também pode ser usado para criar um túnel de solicitações locais, como solicitações da Web, para o cluster HDInsight. A solicitação será encaminhada para o recurso solicitado como se tivesse sido originada no nó principal do cluster HDInsight.

Isso é mais útil para acessar serviços baseados na Web no cluster HDInsight que usa nomes de domínio internos para os nós de cabeçalho ou de trabalho no cluster. Por exemplo, algumas seções da página do Ambari na Web usam nomes de domínio internos, como **headnode0.mycluster.d1.internal.cloudapp.net**. Esses nomes não podem ser resolvidos de fora do cluster, mas solicitações transmitidas por túnel SSH se originam dentro do cluster e serão resolvidas corretamente.

Use as seguintes etapas para criar um túnel SSH e configurar seu navegador para usá-lo para se conectar ao cluster.

1. O comando a seguir pode ser usado para criar um túnel SSH para o nó de cabeçalho do cluster:

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	Isso cria uma conexão que encaminha o tráfego para a porta local 9876 do cluster via SSH. As opções são:

	* **D 8080**: a porta local que roteará o tráfego através do túnel.

	* **C**: compactar todos os dados, porque o tráfego da Web é texto, em sua maioria.

	* **2**: forçar o SSH para tentar somente a versão 2 do protocolo.

	* **q**: modo silencioso.

	* **T**: desabilitar alocação pseudo-tty, já que estamos apenas encaminhando uma porta.

	* **n**: impede a leitura de STDIN, já que estamos apenas encaminhando uma porta.

	* **N**: não executar um comando remoto, pois estamos apenas encaminhando uma porta.

	* **f**: executar em segundo plano.

	Se você tiver configurado o cluster com uma chave SSH, talvez seja necessário usar o parâmetro `-i` e especificar o caminho para a chave privada de SSH.

	Quando o comando terminar, o tráfego enviado para a porta 9876 no computador local será roteado pelo protocolo SSL para o nó de cabeçalho do cluster e parecerá originar-se de lá .

2. Configure o programa cliente, como o Firefox, para usar **localhost:9876** como proxy **SOCKS v5**. As configurações do Firefox serão semelhantes a estas:

	![imagem das configurações do Firefox](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE]Selecionar **DNS Remoto** resolverá as solicitações de DNS (Sistema de Nomes de Domínio) usando o cluster HDInsight. Se essa opção estiver desmarcada, o DNS será resolvido localmente.

	Você pode confirmar que o tráfego está sendo roteado pelo túnel visitando um site como <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> com as configurações de proxy habilitadas e desabilitadas no Firefox. Embora as configurações estejam habilitadas, o endereço IP será de um computador no datacenter do Microsoft Azure.

###Extensões do navegador

Embora configurar o navegador para usar o túnel funcione, geralmente não deseja encaminhar todo o tráfego através do túnel. Extensões de navegador, como <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>, aceitam correspondência de padrões para solicitações de URL (somente FoxyProxy Standard ou Plus); logo, apenas solicitações de URLs específicas serão enviadas pelo túnel.

Se você tiver instalado o FoxyProxy padrão, use as etapas a seguir para configurá-lo para encaminhar somente tráfego do HDInsight pelo túnel:

1. Abra a extensão FoxyProxy no seu navegador. Por exemplo, no Firefox, selecione o ícone FoxyProxy ao lado do campo de endereço.

	![ícone do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. Selecione **Adicionar Novo Proxy**, clique na guia **Geral** e insira um nome de proxy de **HDInsightProxy**.

	![foxyproxy geral](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. Selecione a guia **Detalhes de Proxy** e preencha os campos a seguir:

	* **Host ou Endereço IP**: é localhost, já que estamos usando um túnel SSH no computador local.

	* **Porta**: é a porta usada para o túnel SSH.

	* **Proxy SOCKS**: selecione esta opção para habilitar o navegador a usar o túnel como proxy.

	* **SOCKS v5**: selecione esta opção para definir a versão necessária do proxy.

	![proxy do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. Clique na guia **Padrões de URL** e selecione **Adicionar Novo Padrão**. Use o seguinte para definir o padrão e clique em **OK**:

	* **Nome do Padrão** - **headnode**: é apenas um nome amigável para o padrão.

	* **Padrão de URL** - ***headnode***: define um padrão que corresponde a qualquer URL contendo a palavra **headnode**.

	![padrão do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. Clique em **OK** para adicionar o proxy e fechar **Configurações de Proxy**.

5. Na parte superior da caixa de diálogo FoxyProxy, altere **Modo de Seleção** para **Usar proxies com base em seus padrões e prioridades predefinidos** e clique em **Fechar**.

	![modo de seleção do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

Após a execução destas etapas, somente solicitações de URLs que contêm a cadeia de caracteres **headnode** serão roteadas pelo túnel SSL.

##Próximas etapas

Agora que você entende como autenticar usando uma chave SSH, aprenda a usar MapReduce com Hadoop no HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54-->