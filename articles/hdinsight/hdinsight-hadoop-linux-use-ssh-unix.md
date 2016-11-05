---
title: Use chaves SSH com Hadoop baseado em Linux do Linux, Unix ou OS X | Microsoft Docs
description: " Você pode acessar o HDInsight baseado em Linux usando o Secure Shell (SSH). Este documento fornece informações sobre como usar SSH com o HDInsight de clientes Linux, Unix ou OS X."
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr

---
# Use SSH com Hadoop baseado em Linux no HDInsight dos Linux, Unix ou OS X
> [!div class="op_single_selector"]
> * [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
> 
> 

O [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite executar remotamente operações em seus clusters HDInsight baseados em Linux usando uma interface de linha de comando. Este documento fornece informações sobre como usar SSH com o HDInsight de clientes Linux, Unix ou OS X.

> [!NOTE]
> As etapas neste artigo presumem que você esteja usando um cliente Linux, Unix ou OS X. Essas etapas poderão ser executadas em um cliente baseado no Windows, se você tiver instalado um pacote que fornece `ssh` e `ssh-keygen`, como o [Bash no Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).
> 
> Se você não tiver SSH instalado no cliente baseado no Windows, use as etapas em [Usar SSH com HDInsight baseado em Linux (Hadoop) do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como instalar e usar o PuTTY.
> 
> 

## Pré-requisitos
* **ssh-keygen** e **ssh** para clientes Linux, Unix e OS X. Esse utilitários geralmente são fornecidos com o sistema operacional ou disponibilizados pelo sistema de gerenciamento de pacotes.
* Um navegador da Web moderno, com suporte a HTML5.

OU

* [CLI do Azure](../xplat-cli-install.md)
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## O que é o SSH?
SSH é um utilitário para efetuar login e executar remotamente comandos em um servidor remoto. Com o HDInsight baseado em Linux, o SSH estabelece uma conexão criptografada para o nó de cabeçalho do cluster e fornece uma linha de comando que você usa para digitar comandos. Os comandos são executados diretamente no servidor.

### Nome de usuário do SSH
Um nome de usuário SSH é o nome usado para autenticação no cluster HDInsight. Quando você especifica um nome de usuário SSH durante a criação do cluster, esse usuário é criado em todos os nós no cluster. Depois que o cluster for criado, você poderá usar esse nome de usuário para se conectar aos nós de cabeçalho do cluster HDInsight. Nos nós de cabeçalho, você poderá se conectar aos nós de trabalho individuais.

### Senha ou chave pública SSH
Um usuário SSH pode usar uma senha ou uma chave pública para autenticação. Uma senha é apenas uma cadeia de caracteres de texto composta por você, enquanto uma chave pública faz parte de um par de chaves criptográficas gerado para identificar você exclusivamente.

Uma chave é mais segura do que uma senha, mas requer etapas adicionais para gerar a chave e você deve manter os arquivos com a chave em um local seguro. Se alguém obtiver acesso aos arquivos de chave, obterá acesso à sua conta. Ou, se você perder os arquivos de chave, não será capaz de fazer logon em sua conta.

Um par de chaves consiste em uma chave pública (que é enviada para o servidor do HDInsight) e em uma chave privada (que é mantida no computador cliente). Quando você se conectar ao servidor do HDInsight usando o SSH, o cliente SSH usará a chave privada em seu computador para autenticar com o servidor.

## Criar uma chave SSH
Use as informações a seguir se você planeja usar chaves SSH com o cluster. Se você planeja usar uma senha, poderá ignorar esta seção.

1. Abra uma sessão de terminal e use o comando a seguir para ver se há alguma chave SSH existente:
   
        ls -al ~/.ssh
   
    Procure os arquivos a seguir na listagem de diretórios. Esses são os nomes comuns de chaves públicas de SSH.
   
   * id\_dsa.pub
   * id\_ecdsa.pub
   * id\_ed25519.pub
   * id\_rsa.pub
2. Se não quiser usar um arquivo existente, ou se não existir nenhuma chave SSH, use este comando para gerar um novo arquivo:
   
        ssh-keygen -t rsa
   
    Você será solicitado pelas seguintes informações:
   
   * O local do arquivo: o local padrão é ~/.ssh/id\_rsa.
   * Uma senha: será solicitado que você a digite novamente.
     
     > [!NOTE]
     > É altamente recomendável que você use uma senha segura para a chave. No entanto, se você esquecer a senha, não é possível recuperá-la.
     > 
     > 
     
     Após a conclusão do comando, você terá dois novos arquivos: a chave privada (por exemplo, **id\_rsa**) e a chave pública (por exemplo, **id\_rsa.pub**).

## Criar um cluster HDInsight baseado em Linux
Ao criar um cluster HDInsight baseado em Linux, você deve fornecer a chave pública criada anteriormente. Para clientes Linux, Unix ou OS X, há duas maneiras de criar um cluster HDInsight:

* **Portal do Azure** - Usa um portal baseado na Web para criar o cluster.
* **CLI do Azure para Mac, Linux e Windows**: usa comandos de linha de comando para criar o cluster.

Cada um desses métodos exigirá uma senha ou uma chave pública. Para obter informações completas sobre como criar um cluster HDInsight baseado em Linux, veja [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md).

### Portal do Azure
Ao usar o [Portal do Azure][preview-portal] para criar um cluster HDInsight baseado em Linux, você deve inserir um **nome de usuário SSH** e optar por inserir uma **SENHA** ou uma **CHAVE PÚBLICA SSH**.

Se você selecionar **CHAVE PÚBLICA SSH**, você poderá colar a chave pública (contida no arquivo com a extensão **.pub**) no campo **Chave Pública SSH** ou escolher **Selecionar um arquivo** para procurar e selecionar o arquivo de chave pública.

![Imagem de formulário solicitando uma chave pública](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [!NOTE]
> O arquivo de chave é simplesmente um arquivo de texto. O conteúdo deve ser semelhante ao seguinte:
> 
> ```
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```
> 
> 

Isso cria um logon para o usuário especificado, usando a senha ou a chave pública que você fornecer.

### Interface de linha de comando do Azure para Mac, Linux e Windows
Você pode usar a [CLI do Azure para Mac, Linux e Windows](../xplat-cli-install.md) para criar um novo cluster usando o comando `azure hdinsight cluster create`.

Para obter mais informações sobre como usar esse comando, consulte [Provisionar clusters Hadoop em Linux no HDInsight usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

## Conectar-se a um cluster HDInsight baseado em Linux
Em uma sessão de terminal, use o comando SSH para se conectar ao nó de cabeçalho do cluster, fornecendo o endereço e o nome de usuário:

* **Endereço SSH** - há dois endereços que podem ser usados para se conectar a um cluster usando SSH:
  
  * **Conectar-se ao nó principal**: o nome do cluster, seguido de **-ssh.azurehdinsight.net**. Por exemplo, **mycluster-ssh.azurehdinsight.net**.
  * **Conectar-se ao nó de borda**: se o cluster for o Servidor R no HDInsight, o cluster conterá também um nó de borda que pode ser acessado usando **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, em que **CLUSTERNAME** é o nome do cluster.
* **Nome de usuário**: o nome de usuário SSH que você forneceu ao criar o cluster.

O exemplo a seguir se conectará ao nó de cabeçalho principal do **mycluster** como o usuário **me**:

    ssh me@mycluster-ssh.azurehdinsight.net

Se você usou uma senha para a conta de usuário, será solicitado a inserir a senha.

Se você usou uma chave SSH protegida por uma senha, você deverá inserir a senha. Caso contrário, o SSH tentará autenticar automaticamente usando uma das chaves privadas locais no cliente.

> [!NOTE]
> Se o SSH não autenticar automaticamente com a chave privada correta, use o parâmetro **-i** e especifique o caminho para a chave privada. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`
> 
> 

Se você se conectar usando o endereço para o nó principal e nenhuma porta for especificada, o SSH usará como padrão a porta 22, que se conectará ao nó de cabeçalho principal no cluster HDInsight. Ao usar a porta 23, você se conectará ao secundário. Para saber mais sobre nós de cabeçalho, confira [Disponibilidade e confiabilidade de clusters Hadoop no HDInsight](hdinsight-high-availability-linux.md).

### Conectar a nós de trabalho
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
   
    Se nada for retornado, isso indica que o **ssh-agent** não está em execução. Consulte a documentação do sistema operacional para ver as etapas específicas de instalação e de configuração do **ssh-agent** ou confira [Usando ssh-agent com ssh](http://mah.everybody.org/docs/ssh).
4. Após confirmar que o **ssh-agent** está em execução, use o seguinte para adicionar sua chave privada SSH ao agente:
   
        ssh-add ~/.ssh/id_rsa
   
    Se a chave privada estiver armazenada em um arquivo diferente, substitua `~/.ssh/id_rsa` pelo caminho para o arquivo.

Use as etapas a seguir para conectar-se a nós de trabalho de seu cluster.

> [!IMPORTANT]
> Se usar uma chave SSH para autenticar a conta, você deverá concluir as etapas anteriores para confirmar que o encaminhamento do agente está funcionando.
> 
> 

1. Conecte-se ao cluster HDInsight usando SSH, como descrito anteriormente.
2. Depois de conectado, use o seguinte para recuperar uma lista de nós no cluster. Substitua *ADMINPASSWORD* pela senha de sua conta de administrador do cluster. Substitua *CLUSTERNAME* pelo nome do cluster.
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    Isso retornará informações em formato JSON sobre os nós no cluster, incluindo `host_name`, que contém o FQDN (nome de domínio totalmente qualificado) de cada nó. Veja a seguir um exemplo de uma entrada `host_name` retornada pelo comando **curl**:
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"
3. Quando você tiver uma lista de nós de trabalho aos quais deseja se conectar, use o seguinte comando na sessão SSH para que o servidor abra uma conexão com um nó de trabalho:
   
        ssh USERNAME@FQDN
   
    Substitua *USERNAME* pelo nome de usuário SSH e *FQDN* pelo FQDN do nó de trabalho. Por exemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.
   
   > [!NOTE]
   > Se você usar uma senha para autenticação da sessão SSH, será solicitado que você digite a senha novamente. Se você usar uma chave SSH, a conexão deverá terminar sem prompts.
   > 
   > 
4. Quando a sessão for estabelecida, o prompt do terminal será alterado de `username@hn#-clustername` para `username@wk#-clustername` para indicar que você está conectado ao nó de trabalho. Todo comando executado neste momento será executado no nó de trabalho.
5. Quando terminar de executar ações no nó de trabalho, use o comando `exit` para fechar a sessão para o nó de trabalho. Você voltará para o prompt `username@hn#-clustername`.

## Adicionar mais contas
1. Gere uma nova chave pública e uma chave privada para a nova conta de usuário, como descrito na seção [Criar uma chave SSH](#create-an-ssh-key-optional).
   
   > [!NOTE]
   > A chave privada deve ser gerada em um cliente que o usuário utilizará para se conectar ao cluster ou transferida com segurança para esse cliente após a criação.
   > 
   > 
2. Em uma sessão SSH para o cluster, adicione o novo usuário com o seguinte comando:
   
        sudo adduser --disabled-password <username>
   
    Isso criará uma nova conta de usuário, mas desativará a autenticação de senha.
3. Crie o diretório e os arquivos que armazenarão a chave, usando os seguintes comandos:
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys
4. Quando o editor nano for aberto, copie e cole nos conteúdo da chave pública para a nova conta de usuário. Por fim, use **Ctrl-X** para salvar o arquivo e sair do editor.
   
    ![imagem do editor nano com um exemplo de chave](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)
5. Use o comando a seguir para alterar a propriedade da pasta .ssh e o conteúdo da nova conta de usuário:
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh
6. Agora você deve ser capaz de autenticar para o servidor com a nova conta de usuário e chave privada.

## <a id="tunnel"></a>Túnel SSH
O SSH pode ser usado para criar um túnel de solicitações locais, como solicitações Web, para o cluster HDInsight. A solicitação será encaminhada para o recurso solicitado como se tivesse sido originada no nó principal do cluster HDInsight.

> [!IMPORTANT]
> Um túnel SSH é um requisito para acessar a interface do usuário da Web para alguns serviços do Hadoop. Por exemplo, a interface do usuário de Histórico de trabalho ou a interface do usuário do Gerenciador de Recursos só podem ser acessadas usando um túnel SSH.
> 
> 

Para obter mais informações sobre como criar e usar um túnel SSH, veja [Usar um túnel SSH para acessar a interface do usuário da Web Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md).

## Próximas etapas
Agora que você entende como autenticar usando uma chave SSH, aprenda a usar MapReduce com Hadoop no HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0921_2016-->