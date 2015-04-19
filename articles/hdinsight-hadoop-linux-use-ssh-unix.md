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
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

##Use SSH com Hadoop baseado em Linux no HDInsight dos Linux, Unix ou OS X (Preview)

Clusters de HDInsight baseados em Linux oferecem a opção de proteção de acesso do SSH usando uma senha ou uma chave SSH. Este documento fornece informações sobre como usar SSH com o HDInsight de clientes Linux, Unix ou OS X.

> [AZURE.NOTE] As etapas neste artigo presumem que você esteja usando um cliente Linux, Unix ou OS X. Embora essas etapas possa ser executadas em um cliente Windows se você tiver instalado um pacote que fornece  `ssh` e `ssh-keygen` (como o Git para Windows), é recomendável que os clientes Windows sigam as etapas em [Usar SSH com HDInsight baseado em Linux (Hadoop) do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##Pré-requisitos

* **ssh-keygen** e **ssh** para clientes Linux, Unix e OS X. Esse utilitário é geralmente fornecido com o sistema operacional ou disponíveis através do sistema de gerenciamento de pacote

* Um navegador da Web moderno que ofereça suporte a HTML5

OU

* Ferramentas de Linha de Comando de Plataformas Cruzadas do Azure

##O que é o SSH?

SSH é um utilitário para efetuar login e executar remotamente comandos em um servidor remoto. Com o HDInsight baseado em Linux, o SSH estabelece uma conexão segura para o nó principal do cluster e fornece uma linha de comando que você usa para digitar comandos. Os comandos são executados diretamente no servidor.

##Criar uma chave SSH (opcional)

Ao criar um cluster HDInsight baseado em Linux, você tem a opção de usar uma senha ou chave SSH para autenticar o servidor ao usar o SSH. Chaves SSH são consideradas mais seguras, porque elas são baseadas em certificado. Use as informações a seguir se você planeja usar chaves SSH com o cluster.

1. Abra uma sessão de terminal e use o comando a seguir para ver se há quaisquer chaves SSH existentes

		ls -al ~/.ssh

	Procure os seguintes arquivos na lista de diretórios. Esses são os nomes comuns de chaves públicas de SSH.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Se você não deseja usar um arquivo existente ou não ter nenhuma chave SSH existente, use o seguinte para gerar um novo arquivo.

		ssh-keygen -t rsa

	Você será solicitado pelas seguintes informações:

	* O local do arquivo - padrão para ~/.ssh/id\_rsa.
	* Uma frase secreta - você será solicitado a inserir isso novamente
	
		> [AZURE.NOTE] É altamente recomendável que você use uma senha segura para a chave. No entanto, se você esquecer a senha, não é possível recuperá-la.

	Depois que o comando for concluído, você terá dois novos arquivos, a chave privada (por exemplo, **id\_rsa**) e a chave pública (por exemplo, **id\_rsa.pub**.)

##Criar um cluster HDInsight baseado em Linux

Ao criar um cluster HDInsight baseado em Linux, você deve fornecer a **chave pública** criada anteriormente. Para clientes Linux, Unix ou OS X, há duas maneiras de criar um cluster HDInsight:

* **Portal de Gerenciamento do Azure** - usa um portal baseado na Web para criar o cluster

* **Interface de linha de comando entre plataformas do Azure (xplat-cli)** - usa comandos da linha de comando para criar o cluster

Cada um desses métodos exigirá uma **senha** ou uma **chave pública**. Para obter mais informações sobre como criar um cluster HDInsight baseado em Linux, consulte <a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisionar clusters HDInsight baseados em Linux</a>.

###Portal de Gerenciamento do Azure

Ao usar o portal para criar um cluster HDInsight baseado em Linux, você deve inserir um **Nome de Usuário SSH** e selecionar para inserir uma **Senha** ou **Chave Pública SSH**. Se você selecionar **Chave Pública SSH**, deverá colar a chave pública (contida no arquivo com a extensão **. pub**) no seguinte formato.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] O arquivo de chave é simplesmente um arquivo de texto. O conteúdo deve ser semelhante ao seguinte.
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Isso cria um logon para o usuário especificado, usando a senha ou chave pública que você fornecer.

###Interface de Linha de Comando de Plataforma Cruzada do Azure

Você pode usar a <a href="../xplat-cli/" target="_brad">Interface de linha de comando entre plataformas do Azure</a> para criar um novo cluster usando o comando  `azure hdinsight cluzter create`.

Para saber mais sobre o uso deste comando, consulte <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisionar clusters Linux do Hadoop no HDInsight usando opções personalizadas</a>

##Conectar-se a um cluster HDInsight baseado em Linux

De uma sessão de terminal, use o comando SSH para se conectar ao cluster, fornecendo o endereço do cluster e de nome de usuário.

* **Endereço SSH** - o nome do cluster, seguido por **-ssh.azurehdinsight.net**. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

* **Nome de usuário** - o nome de usuário do SSH é fornecido quando cria o cluster

O exemplo a seguir se conectará ao cluster **mycluster** como o usuário **eu**.

	ssh me@mycluster-ssh.azurehdinsight.net

Se você usou uma **senha** para a conta de usuário, será solicitado a inserir a senha.

Se você usou uma **chave SSH** que é protegida com uma senha, será solicitado a inserir a senha; caso contrário, o SSH tentará autenticar automaticamente usando uma das chaves privadas locais no cliente.

> [AZURE.NOTE] Se o SSH não autentica automaticamente com a **chave privada** correta, use o parâmetro **-i** e especifique o caminho para a chave privada. O exemplo a seguir carregará a **chave privada** de `~/.ssh/id_rsa`.
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

##Adicionar contas

2. Gera uma nova **chave pública** e **chave privada** para a nova conta de usuário, conforme descrito em [Criar uma chave SSH](#create) .

	> [AZURE.NOTE] A chave privada deve ser gerada em um cliente que o usuário utilizará para se conectar ao cluster ou transferida com segurança para esse cliente após a criação.

1. De uma sessão SSH para o cluster, adicione o novo usuário com o comando a seguir.

		sudo adduser --disabled-password <username> 

	Isso criará uma nova conta de usuário, mas desativará a autenticação de senha.

2. Crie a pasta e os arquivos para manter a chave usando os seguintes comandos.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Quando o editor nano for aberto, copie e cole nos conteúdo da **chave pública** para a nova conta de usuário. Por fim, use **Ctrl-X** para salvar o arquivo e saia do editor.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Use o seguinte comando para alterar a propriedade da pasta .ssh e os conteúdos para a nova conta de usuário.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Agora você deve ser capaz de autenticar para o servidor com a nova conta de usuário e **chave privada**.

##<a id="tunnel"></a>Túnel SSH

O SSH também pode ser usado para criar um túnel de solicitações locais, como solicitações da Web, para o cluster HDInsight. A solicitação será encaminhada para o recurso solicitado como se tivesse sido originada no nó principal do cluster HDInsight.

Isso é mais útil ao acessar serviços baseados na Web no cluster HDInsight que usam nomes de domínio interno para os nós de cabeçalho ou de trabalho no cluster. Por exemplo, algumas seções da página da Web Ambari usam nomes de domínio internos como **headnode0.mycluster.d1.internal.cloudapp.net**. Esses nomes não podem ser resolvidos por fora do cluster, porém, as solicitações transmitidas via SSH se originam dentro do cluster e serão resolvidas corretamente.

Use as seguintes etapas para criar um túnel SSH e configurar seu navegador para usá-lo para se conectar ao cluster.

1. O comando a seguir pode ser usado para criar um túnel SSH para o nó principal do cluster.

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	Isso cria uma conexão que encaminha o tráfego para a porta local 9876 do cluster via SSH. As opções são:

	* **D 8080** - a porta local que roteará o tráfego através do túnel

	* **C** - compacta todos os dados, porque o tráfego da Web é principalmente texto

	* **2** - força o SSH a tentar somente a versão 2 do protocolo

	* **p** - modo silencioso

	* **T** - desabilita a alocação pseudo-tty, já que estamos encaminhando apenas uma porta
	
	* **n** - impede a leitura do STDIN, já que estamos apenas encaminhando uma porta

	* **N** - não executa um comando remoto, pois estamos apenas encaminhando uma porta

	* **f** - execução em segundo plano

	Se você tiver configurado o cluster com uma **chave SSH**, talvez seja necessário usar o parâmetro `-i` e especifique o caminho para a chave privada do SSH.

	Quando o comando for concluído, o tráfego enviado para a porta 9876 no computador local será roteado por SSL para o nó principal do cluster e pareça originar-se lá.

2. Configure o programa cliente, como o Firefox, use **localhost:9876** como um proxy **SOCKS v5**. Aqui está a aparência das configurações do Firefox.

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE] Selecionar **DNS Remoto** resolverá solicitações DNS usando o cluster HDInsight. Se não selecionado, o DNS será resolvido localmente.

	Você pode garantir que o tráfego esteja sendo encaminhado por meio do túnel visitando um site como <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a>, com as configurações de proxy habilitadas e desabilitadas no Firefox. Quando habilitada, o endereço IP será para uma máquina no datacenter do Microsoft Azure.

###Extensões do navegador

Embora configurar o navegador para usar o túnel funcione, geralmente não deseja encaminhar todo o tráfego através do túnel. Extensões de navegador como <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a> dão suporte padrão de correspondência para solicitações de URL (apenas FoxyProxy Standard ou Plus) para que somente as solicitações de URLs específicas sejam enviadas através do túnel.

Se você tiver instalado o **FoxyProxy Standard**, use as seguintes etapas para configurá-lo para encaminhar o tráfego apenas para HDInsight pelo túnel.

1. Abra a extensão FoxyProxy no seu navegador. Por exemplo, no Firefox, selecione o ícone FoxyProxy ao lado do campo de endereço.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. Selecione **Adicionar Novo Proxy**, a guia **Geral** e insira um nome de proxy do **HDInsight**.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. Selecione a **guia Detalhes de Proxy** e preencha os campos a seguir.

	* **Host ou Endereço IP** - localhost, como estamos usando um túnel SSH no computador local

	* **Porta** - a porta usada para o túnel SSH

	* **Proxy SOCKS** - selecione para habilitar o navegador a usar o túnel como um proxy

	* **SOCKS v5** - selecione para definir a versão necessária do proxy

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. Selecione a guia **Padrões de URL** e selecione **Adicionar Novo Padrão**. Use o seguinte para definir o padrão e clique em **OK**.

	* **Nome do Padrão** - **headnode** - isso é apenas um nome amigável para o padrão

	* **Padrão de URL** - **\*headnode\*** - define um padrão que corresponde a qualquer URL com a palavra **headnode** nela.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. Selecione **Ok** para adicionar o proxy e fechar as **Configurações de Proxy**

5. Na parte superior da caixa de diálogo FoxyProxy, altere **Selecionar Modo** para **Usar proxies com base em seus padrões predefinidos e prioridades** e selecione **Fechar**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

Após seguir essas etapas, somente as solicitações de URLs que contêm a cadeia de caracteres **headnode** serão roteadas através do túnel SSL.

##Próximas etapas

Agora que você entende como autenticar usando uma chave SSH, aprenda a usar o MapReduce com Hadoop no HDInsight.

* [Use o hive com o HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

* [Usar trabalhos MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
 
<!--HONumber=47-->
