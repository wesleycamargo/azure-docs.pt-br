<properties
   pageTitle="Usar chaves SSH com Hadoop no HDInsight baseado em Linux do Windows | Azure"
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

#Usar SSH com Hadoop baseado em Linux no HDInsight no Windows (visualização)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Os clusters Azure HDInsight baseados em Linux oferecem a opção de usar acesso SSH por meio de uma senha ou chave SSH. Este documento fornece informações sobre como se conectar ao HDInsight por meio de clientes baseados em Windows usando o cliente SSH PuTTY.

> [AZURE.NOTE]As etapas neste artigo presumem que você esteja usando um cliente baseado em Windows. Se você estiver usando um cliente Linux, Unix ou OS X, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight por meio de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

##Pré-requisitos

* **PuTTY** e **PuTTYGen** para clientes baseados em Windows. Esses utilitários estão disponíveis em <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html</a>.

* Um navegador da Web moderno, com suporte a HTML5.

OU

* [CLI do Azure para Mac, Linux e Windows](xplat-cli.md).

##O que é o SSH?

SSH é um utilitário para efetuar login e executar remotamente comandos em um servidor remoto. Com o HDInsight baseado em Linux, o SSH estabelece uma conexão criptografada para o nó de cabeçalho do cluster e fornece uma linha de comando que você usa para digitar comandos. Os comandos são executados diretamente no servidor.

##Criar uma chave SSH (opcional)

Ao criar um cluster HDInsight baseado em Linux, você tem a opção de usar uma senha ou uma chave SSH para autenticação no servidor, quando utiliza SSH. Chaves SSH são consideradas mais seguras, porque elas são baseadas em certificado. Use as informações a seguir se você planeja usar chaves SSH com o cluster.

1. Abra o PuTTYGen.

2. Para **Tipo de chave a gerar**, selecione **SSH-2 RSA** e clique em **Gerar**.

	![interface PuTTYGen](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Mova o mouse na área abaixo da barra de progresso, até que a barra seja preenchida. Mover o mouse gera dados aleatórios são usados para gerar a chave.

	![movendo o mouse](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	Depois que a chave foi gerada, a chave pública será exibida.

4. Para maior segurança, você pode inserir uma senha no campo **Senha** campo e digitar o mesmo valor no campo **Confirmar senha**.

	![senha](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE]É altamente recomendável que você use uma senha segura para a chave. No entanto, se você esquecer a senha, não é possível recuperá-la.

5. Clique em **Salvar chave privada** para salvar a chave em um arquivo **.ppk**. Essa chave será usada para autenticar seu cluster HDInsight baseado em Linux.

	> [AZURE.NOTE]Você deve armazenar essa chave em um local seguro, porque ela pode ser usada para acessar seu cluster HDInsight baseado em Linux.

6. Clique em **Salvar chave pública** para salvar a chave em um arquivo **.txt**. Isso permite que você reutilize a chave pública no futuro quando criar outros clusters HDInsight baseados em Linux.

	> [AZURE.NOTE]A chave pública também é exibida na parte superior do PuTTYGen. Você pode clicar com o botão direito do mouse nesse campo, copiar o valor e colá-lo em um formulário, como o assistente do HDInsight no portal do Azure.

##Criar um cluster HDInsight baseado em Linux

Ao criar um cluster HDInsight baseado em Linux, você deve fornecer a chave pública criada anteriormente. Em clientes baseados em Windows, há duas maneiras de criar um cluster HDInsight baseado em Linux:

* **Portal do Azure**: usa um portal baseado na Web para criar o cluster.

* **CLI do Azure para Mac, Linux e Windows**: usa comandos de linha de comando para criar o cluster.

Cada um desses métodos exigirá a chave pública. Para obter informações completas sobre como criar um cluster HDInsight baseado em Linux, consulte <a href="./hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisionar clusters HDInsight baseados em Linux</a>.

###Portal do Azure

Ao usar o portal para criar um cluster HDInsight baseado em Linux, você deve inserir um nome de usuário e uma senha ou chave pública no formulário a seguir:

![Imagem de formulário solicitando uma chave pública](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Isso cria um logon para o usuário especificado e permite a autenticação de senha ou autenticação de chave SSH.

###Interface de linha de comando do Azure para Mac, Linux e Windows

Você pode usar a [CLI do Azure para Mac, Linux e Windows](xplat-cli.md) para criar um novo cluster usando o comando `azure hdinsight cluster create`.

Para obter mais informações sobre como usar esse comando, consulte <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisionar clusters Hadoop em Linux no HDInsight usando opções personalizadas</a>.

##Conectar-se a um cluster HDInsight baseado em Linux

1. Abra o PuTTY.

	![interface do putty](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Se você forneceu uma chave SSH quando criou sua conta de usuário, execute a etapa a seguir para selecionar a chave privada a ser usada para autenticar para o cluster:

	Em **Categoria**, expanda **Conexão**, expanda **SSH** e selecione **Autenticação**. Por fim, clique em **Procurar** e selecione o arquivo .ppk que contém a chave privada.

	![interface do putty, selecionar chave privada](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. Em **Categoria**, selecione **Sessão**. Na tela **Opções básicas para sua sessão PuTTY**, insira o endereço SSH do seu servidor HDInsight no campo **Nome de host (ou endereço IP)**. O endereço SSH é o nome do cluster, seguido de **-ssh.azurehdinsight.net**. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

	![interface do putty com endereço ssh inserido](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Para salvar as informações de conexão para uso futuro, insira um nome para essa conexão em **Sessões Salvas** e clique em **Salvar**. A conexão será adicionada à lista de sessões salvas.

5. Clique em **Abrir** para se conectar ao cluster.

	> [AZURE.NOTE]Se esta for a primeira vez que você se conectou ao cluster, você receberá um alerta de segurança. Isso é normal. Selecione **Sim** para armazenar em cache a chave RSA2 do servidor para continuar.

6. Quando solicitado, insira o usuário que você inseriu ao criar o cluster. Se você tiver fornecido um senha para o usuário, você deverá inseri-la também.

###Conectar a nós de trabalho

Os nós de trabalho não são diretamente acessíveis de fora do datacenter do Azure, mas podem ser acessados do nó de cabeçalho do cluster via SSH.

Se você forneceu uma chave SSH quando criou sua conta de usuário, execute as seguintes etapas para usar a chave privada ao autenticar no cluster, se quiser conectar-se aos nós de trabalho.

1. Instale o Pageant de <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html</a>. Esse utilitário é usado para armazenar em cache as chaves SSH para PuTTY.

2. Execute o Pageant. Ele será minimizado em um ícone na bandeja de status. Clique com o botão direito do mouse no ícone e selecione **Adicionar Chave**.

    ![adicionando chave](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Quando a caixa de diálogo Procurar aparecer, selecione o arquivo .ppk que contém a chave e clique em **Abrir**. Isso adiciona a chave ao Pageant, que a fornecerá para PuTTY ao conectar-se ao cluster.

    > [AZURE.IMPORTANT]Se você usou uma chave SSH para proteger sua conta, execute as etapas anteriores para poder se conectar a nós de trabalho.

4. Abra o PuTTY.

5. Se você usar uma chave SSH para autenticar, na seção **Categoria**, expanda **Conexão**, expanda **SSH** e selecione **Autenticação**.

    Na seção **Parâmetros de autenticação**, habilite **Permitir encaminhamento do agente**. Isso permite que o PuTTY transmita automaticamente a autenticação de certificado através da conexão com o nó de cabeçalho do cluster ao conectar-se a nós de trabalho.

    ![permitir encaminhar de agente](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Conecte-se ao cluster conforme documentado anteriormente. Se você usar uma chave SSH para autenticação, você não precisará selecionar a chave; a chave SSH adicionada ao Pageant será usada para autenticação no cluster.

7. Estabelecida a conexão, use o seguinte para recuperar uma lista de nós no cluster. Substitua *ADMINPASSWORD* pela senha de sua conta de administrador do cluster. Substitua *CLUSTERNAME* pelo nome do cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Isso retornará informações em formato JSON sobre os nós no cluster, incluindo `host_name`, que contém o FQDN (nome de domínio totalmente qualificado) de cada nó. Veja a seguir um exemplo de uma entrada `host_name` retornada pelo comando **curl**:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Quando você tiver uma lista de nós de trabalho aos quais deseja se conectar, use o seguinte comando na sessão PuTTY para abrir uma conexão com um nó de trabalho:

        ssh USERNAME@FQDN

    Substitua *USERNAME* pelo nome de usuário SSH e *FQDN* pelo FQDN do nó de trabalho. Por exemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE]Se você usar uma senha para autenticação da sessão SSH, será solicitado que você digite a senha novamente. Se você usar uma chave SSH, a conexão deverá terminar sem prompts.

9. Estabelecida a sessão, o prompt de sua sessão PuTTY mudará `username@headnode` para `username@workernode` para indicar que você está conectado ao nó de trabalho. Todo comando executado neste momento será executado no nó de trabalho.

10. Quando terminar de executar ações no nó de trabalho, use o comando `exit` para fechar a sessão para o nó de trabalho. Você voltará para o prompt `username@headnode`.

##Adicionar mais contas

Se precisar adicionar mais contas ao seu cluster, execute as seguintes etapas:

1. Gere uma nova chave pública e chave privada para a nova conta de usuário conforme descrito anteriormente.

2. Em uma sessão SSH para o cluster, adicione o novo usuário com o seguinte comando:

		sudo adduser --disabled-password <username>

	Isso criará uma nova conta de usuário, mas desativará a autenticação de senha.

3. Crie o diretório e os arquivos que armazenarão a chave, usando os seguintes comandos:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Quando o editor nano for aberto, copie e cole nos conteúdo da chave pública para a nova conta de usuário. Por fim, use **Ctrl-X** para salvar o arquivo e sair do editor.

	![imagem do editor nano com um exemplo de chave](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Use o comando a seguir para alterar a propriedade da pasta .ssh e o conteúdo da nova conta de usuário:

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Agora você deve ser capaz de autenticar para o servidor com a nova conta de usuário e chave privada.

##<a id="tunnel"></a>Túnel SSH

O SSH também pode ser usado para criar um túnel de solicitações locais, como solicitações da Web, para o cluster HDInsight. A solicitação será encaminhada para o recurso solicitado como se tivesse sido originada no nó principal do cluster HDInsight.

Isso é mais útil ao acessar serviços baseados na Web no cluster HDInsight que usam nomes de domínio interno para os nós de cabeçalho ou de trabalho no cluster. Por exemplo, algumas seções da página do Ambari na Web usam nomes de domínio internos, como **headnode0.mycluster.d1.internal.cloudapp.net**. Esses nomes não podem ser resolvidos de fora do cluster, mas solicitações transmitidas por túnel SSH se originam dentro do cluster e serão resolvidas corretamente.

Use as seguintes etapas para criar um túnel SSH e configurar seu navegador para usá-lo para se conectar ao cluster:

1. Abra o PuTTY e insira suas informações de conexão, conforme documentado anteriormente na seção [Conectar a um cluster baseado em Linux](#connect-to-a-linux-based-hdinsight-cluster).

2. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e selecione **Túneis**.

3. Forneça as seguintes informações no formulário **Opções de controle do encaminhamento de porta SSH**:

	* **Porta de Origem**: a porta no cliente que você deseja encaminhar. Por exemplo, **9876**.

	* **Destino**: o endereço SSH para o cluster HDInsight baseado em Linux. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

	* **Dinâmico**: habilita roteamento de proxy SOCKS dinâmico.

	![imagem de opções de túnel](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.

5. Quando solicitado, faça logon no servidor. Isso estabelecerá uma sessão SSH e habilitará o túnel.

6. Configure o programa cliente, como o Firefox, para usar **localhost:9876** como proxy **SOCKS v5**. As configurações do Firefox serão semelhantes a estas:

	![imagem das configurações do Firefox](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE]Selecionar **DNS Remoto** resolverá as solicitações de DNS usando o cluster HDInsight. Se essa opção estiver desmarcada, o DNS será resolvido localmente.

	Você pode confirmar que o tráfego está sendo roteado pelo túnel visitando um site como <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> com as configurações de proxy habilitadas e desabilitadas no Firefox. Enquanto estiverem habilitadas, o endereço IP será de um computador no datacenter do Microsoft Azure.

###Extensões do navegador

Embora configurar o navegador para usar o túnel funcione, geralmente não deseja encaminhar todo o tráfego através do túnel. Extensões de navegador, como <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>, aceitam correspondência de padrões para solicitações de URL (somente FoxyProxy Standard ou Plus); logo, apenas solicitações de URLs específicas serão enviadas pelo túnel.

Se você tiver instalado o FoxyProxy Standard, use as seguintes etapas para configurá-lo para encaminhar o tráfego apenas para HDInsight pelo túnel.

1. Abra a extensão FoxyProxy no seu navegador. Por exemplo, no Firefox, selecione o ícone FoxyProxy ao lado do campo de endereço.

	![ícone do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. Selecione **Adicionar Novo Proxy**, clique na guia **Geral** e insira um nome de proxy de **HDInsightProxy**.

	![foxyproxy geral](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. Selecione a guia **Detalhes de Proxy** e preencha os campos a seguir:

	* **Host ou Endereço IP**: é localhost, já que estamos usando um túnel SSH no computador local.

	* **Porta**: é a porta usada para o túnel SSH.

	* **Proxy SOCKS**: selecione esta opção para habilitar o navegador a usar o túnel como proxy.

	* **SOCKS v5**: selecione esta opção para definir a versão necessária do proxy.

	![proxy do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. Clique na guia **Padrões de URL** e selecione **Adicionar Novo Padrão**. Use o seguinte para definir o padrão e clique em **OK**:

	* **Nome do Padrão** - **headnode**: é apenas um nome amigável para o padrão.

	* **Padrão de URL** - ***headnode***: define um padrão que corresponde a qualquer URL contendo a palavra **headnode**.

	![padrão do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. Clique em **OK** para adicionar o proxy e fechar **Configurações de Proxy**.

5. Na parte superior da caixa de diálogo FoxyProxy, altere **Modo de Seleção** para **Usar proxies com base em seus padrões e prioridades predefinidos** e clique em **Fechar**.

	![modo de seleção do foxyproxy](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

Após a execução destas etapas, somente solicitações de URLs que contêm a cadeia de caracteres **headnode** serão roteadas pelo túnel SSL.

##Próximas etapas

Agora que você entende como autenticar usando uma chave SSH, aprenda a usar MapReduce com Hadoop no HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54-->