---
title: Usar SSH com HDInsight (Hadoop) no Windows, Linux, Unix ou OS X | Microsoft Docs
description: " Você pode acessar o HDInsight usando o Secure Shell (SSH). Este documento fornece informações sobre como usar SSH com o HDInsight de clientes Windows, Linux, Unix ou OS X."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 37409ad3f50cdd4a7a384c96a57a35ef8c83fb8f


---
# <a name="use-ssh-with-hdinsight-hadoop-from-windows-linux-unix-or-os-x"></a>Usar SSH com HDInsight (Hadoop) no Windows, Linux, Unix ou OS X

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite que você faça logon um cluster HDInsight baseado em Linux e execute comandos usando uma interface de linha de comando. Este documento fornece informações básicas sobre SSH e informações específicas sobre como usar SSH com HDInsight.

## <a name="what-is-ssh"></a>O que é o SSH?

SSH é um protocolo de rede criptográfico que permite a comunicação segura com um servidor remoto em uma rede desprotegida. O SSH é usado para fornecer logon seguro de linha de comando para um servidor remoto. Nesse caso, nós principais ou nó de borda de um cluster HDInsight.

Você também pode usar SSH para encapsular o tráfego de rede do cliente para o cluster HDInsight. O uso de um túnel permite que você acesse serviços no cluster HDInsight que não são expostos diretamente à Internet. Para obter mais informações sobre como usar túnel SSH com o HDInsight, confira [Usar túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ssh-clients"></a>Clientes SSH

Muitos sistemas operacionais fornecem funcionalidade de cliente SSH por meio dos utilitários de linha de comando `ssh` e `scp`.

* __SSH__: um cliente SSH geral que pode ser usado para estabelecer uma sessão de linha de comando remota e criar túneis.
* __SCP__: um utilitário que copia arquivos entre sistemas locais e remotos usando o protocolo SSH.

Historicamente, o Windows não forneceu um cliente SSH até o Windows 10 Anniversary Edition. Esta versão do Windows inclui o recurso Bash no Windows 10 para desenvolvedores, que fornece `ssh`, `scp` e outros comandos do Linux. Para obter mais informações sobre como usar o Bash no Windows 10, confira [Bash no Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).

Se você usa o Windows e não tem acesso para Bash no Windows 10, recomendamos os seguintes clientes SSH:

* [Git para Windows](https://git-for-windows.github.io/): fornece os utilitários de linha de comando `ssh` e `scp`.
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/): fornece um cliente SSH gráfico.
* [MobaXterm](http://mobaxterm.mobatek.net/): fornece um cliente SSH gráfico.
* [Cygwin](https://cygwin.com/): fornece os utilitários de linha de comando `ssh` e `scp`.

> [!NOTE]
> As etapas neste documento pressupõem que você tenha acesso ao comando `ssh`. Se estiver usando um cliente como puTTY ou MobaXterm, confira a documentação do produto para obter o comando e os parâmetros equivalentes.

## <a name="ssh-authentication"></a>Autenticação SSH

Uma conexão SSH pode ser autenticada usando uma senha ou [criptografia de chave pública (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography). O uso de uma chave é a opção mais segura, pois ela não é vulnerável a muitos dos ataques que afetam as senhas. No entanto a criação e o gerenciamento de chaves são mais complicados do que o uso de uma senha.

O uso da criptografia de chave pública envolve a criação de um par de chaves _públicas_ e _privadas_.

* A **chave pública** é carregada em nós do cluster HDInsight ou em qualquer outro serviço que você queira usar com a criptografia de chave pública.

* A **chave privada** é o que você apresenta ao cluster HDInsight ao fazer logon usando um cliente SSH, para verificar sua identidade. Proteja essa chave privada. Não a compartilhe.

    Você pode adicionar segurança adicional criando uma senha para a chave privada. Você deve fornecer essa senha para que a chave possa ser usada.

### <a name="create-a-public-and-private-key"></a>Criar uma chave pública e privada

O utilitário `ssh-keygen` é a maneira mais fácil de criar um par de chaves públicas e privadas para uso com o HDInsight. Em uma linha de comando, use o seguinte comando para criar um novo par de chaves para uso com o HDInsight:

> [!NOTE]
> Se estiver usando um cliente SSH GUI como MobaXTerm ou puTTY, confira a documentação do cliente sobre como gerar chaves.

    ssh-keygen -t rsa -b 2048

Você deverá fornecer as seguintes informações:

* O local do arquivo: o local padrão é `~/.ssh/id_rsa`.

* Uma senha opcional: se inserir uma senha, você precisará reinseri-la durante a autenticação no cluster HDInsight.

> [!IMPORTANT]
> A senha é uma senha para a chave privada. Sempre que usar a chave privada para autenticar, você deverá fornecer a senha para que a chave possa ser usada. Se alguém obtiver a chave particular, poderá usá-la sem a senha.
>
> Se você esquecer a senha, não será possível redefini-la nem recuperá-la.

Depois que o comando for concluído, você terá dois novos arquivos:

* __id\_rsa__: este arquivo contém a chave privada.

    > [!WARNING]
    > Você deve restringir o acesso a esse arquivo para impedir o acesso não autorizado a serviços protegidos pela chave pública.

* __id\_rsa.pub__: este arquivo contém a chave pública. Use esse arquivo ao criar um cluster HDInsight.

    > [!NOTE]
    > Não importa quem tem acesso à chave _pública_. Por si só, tudo o que a chave pública pode fazer é verificar a chave privada. Serviços como o servidor SSH usam a chave pública para verificar sua identidade quando você se autentica usando a chave privada.

## <a name="configure-ssh-on-hdinsight"></a>Configurar SSH no HDInsight

Ao criar um cluster HDInsight baseado em Linux, você deve fornecer um _nome de usuário SSH_ e uma _senha_ ou _chave pública_. Durante a criação do cluster, essa informação é usada para criar um logon em nós do cluster HDInsight. A senha ou chave pública é usada para proteger a conta de usuário.

Para obter mais informações sobre como configurar SSH durante a criação do cluster, confira um dos seguintes documentos:

* [Criar o HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Criar o HDInsight usando a CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [Criar o HDInsight usando o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Criar o HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [Criar o HDInsight usando o SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Criar o HDInsight usando REST](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>Usuários adicionais do SSH

Embora usuários SSH adicionais possam ser adicionados ao cluster após ele ser criado, isso não é recomendável.

* Você deve adicionar manualmente novos usuários do SSH a cada nó no cluster.

* Novos usuários do SSH têm o mesmo acesso ao HDInsight que o usuário padrão. Não há maneira de restringir o acesso a dados ou trabalhos no HDInsight com base na conta de usuário do SSH.

Para restringir o acesso por usuário, você deve usar um cluster de HDInsight associado ao domínio. O HDInsight usa o Active Directory para controlar o acesso a recursos de cluster associados ao domínio.

O uso de um cluster HDInsight associado ao domínio permite autenticar-se usando o Active Directory depois de se conectar usando o SSH. Vários usuários podem conectar-se usando SSH e, depois, autenticar suas contas do Active Directory após se conectarem. Confira a seção [HDInsight associado ao domínio](#domainjoined) para obter mais informações.

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a> Conectar-se ao HDInsight

Embora todos os nós em um cluster HDInsight executem o servidor SSH, você só pode se conectar aos nós principais ou nós de borda na Internet pública.

* Para se conectar aos _nós principais_, use `CLUSTERNAME-ssh.azurehdinsight.net`, em que __CLUSTERNAME__ é o nome do cluster do HDInsight. Conectar-se na porta 22 (o padrão para SSH) conecta-se ao nó principal primário. A porta 23 se conecta ao nó principal secundário.

* Para se conectar a um _nó de borda_, use `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`, em que __EDGENAME__ é o nome do nó de borda e __CLUSTERNAME__ é o nome do cluster do HDInsight. Use a porta 22 ao se conectar ao nó de borda.

Os exemplos a seguir demonstram como conectar-se a nós principais e ao nó de borda de um cluster chamado __myhdi__ usando um nome de usuário SSH de __sshuser__. O nó de borda é denominado __myedge__.

| Para fazer isto... | Use isto... |
| ----- | ----- |
| Conectar-se ao nó principal primário | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| Conectar-se ao nó principal secundário | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| Conectar-se ao nó de borda | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

Se usar uma senha para proteger a conta SSH, você precisará digitar a senha.

Se você usar uma chave pública para proteger a conta SSH, talvez seja necessário especificar o caminho para a chave privada correspondente usando a opção `-i`. O seguinte exemplo demonstra como usar a opção `-i`:

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>Conectar-se a outros nós

Os nós de trabalho e Zookeeper não são diretamente acessíveis de fora do cluster, mas podem ser acessados por meio dos nós principais de cluster ou nós de borda. A seguir estão as etapas gerais para fazer isso:

1. Use o SSH para se conectar a um nó de cabeçalho ou de borda:

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Da conexão SSH para o nó de cabeçalho ou de borda, use o comando `ssh` para se conectar a um nó de trabalho no cluster:

        ssh sshuser@wn0-myhdi

    Para recuperar uma lista de nós de trabalho no cluster, confira o exemplo de como recuperar o nome de domínio totalmente qualificado de nós de cluster no documento [Administrar o HDInsight usando a API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Se a conta SSH for protegida usando uma senha, você será solicitado a inserir a senha e a conexão será estabelecida.

Se usar uma chave SSH para autenticar a conta de usuário, você deverá garantir que seu ambiente local esteja configurado para encaminhamento do agente SSH.

> [!IMPORTANT]
> As etapas a seguir pressupõem o uso de um sistema com base em Linux/UNIX e funcionam com Bash no Windows 10. Se essas etapas não funcionarem para o seu sistema, você precisará conferir a documentação de seu cliente SSH.

1. Usando um editor de texto, abra `~/.ssh/config`. Se esse arquivo não existir, você poderá criá-lo digitando `touch ~/.ssh/config` na linha de comando.

2. Adicione o seguinte ao arquivo. Substitua *CLUSTERNAME* pelo nome do seu cluster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Essa entrada configura o encaminhamento do agente SSH para o cluster HDInsight.

3. Teste o encaminhamento do agente SSH usando o seguinte comando no terminal:

        echo "$SSH_AUTH_SOCK"

    Esse comando retorna informações semelhantes ao seguinte texto:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Se nada for retornado, isso indica que `ssh-agent` não está em execução. Confira as informações de scripts de inicialização de agente em [usando ssh-agent com ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) ou confira a documentação do cliente SSH para obter as etapas específicas de instalação e configuração `ssh-agent`.

4. Após confirmar que o **ssh-agent** está em execução, use o seguinte para adicionar sua chave privada SSH ao agente:

        ssh-add ~/.ssh/id_rsa

    Se a chave privada estiver armazenada em um arquivo diferente, substitua `~/.ssh/id_rsa` pelo caminho para o arquivo.

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>HDInsight ingressado no domínio

O [HDInsight ingressado no Domínio ](hdinsight-domain-joined-introduction.md) integra o Kerberos com o Hadoop no HDInsight. Como o usuário SSH não é um usuário de domínio do Active Directory, não é possível executar comandos Hadoop até você se autenticar com o Active Directory. Use as seguintes etapas para autenticar sua sessão SSH com o Active Directory:

1. Conectar-se a um cluster HDInsight de domínio usando o SSH conforme mencionado na seção [Conectar ao HDInsight](#connect). Por exemplo, o comando a seguir se conecta a um cluster HDInsight chamado __myhdi__ usando uma conta SSH chamada __sshuser__.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Use o seguinte para se autenticar usando um domínio de usuário e senha:

        kinit

     Quando solicitado, insira um nome de usuário de domínio e a senha do usuário de domínio.

    Para obter mais informações sobre como configurar os usuários do domínio para os clusters do HDInsight ingressados no domínio, confira [Configurar os clusters HDInisight ingressados no Domínio](hdinsight-domain-joined-configure.md).

Após a autenticação usando o comando `kinit`, você pode usar comandos Hadoop, como `hdfs dfs -ls /` ou `hive`.

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>Túnel SSH

O SSH pode ser usado para criar um túnel de solicitações locais, como solicitações Web, para o cluster HDInsight. A solicitação será encaminhada para o recurso solicitado como se tivesse sido originada no nó principal do cluster HDInsight.

> [!IMPORTANT]
> Um túnel SSH é um requisito para acessar a interface do usuário da Web para alguns serviços do Hadoop. Por exemplo, a interface do usuário de Histórico de trabalho ou a interface do usuário do Gerenciador de Recursos só podem ser acessadas usando um túnel SSH.

Para obter mais informações sobre como criar e usar um túnel SSH, veja [Usar um túnel SSH para acessar a interface do usuário da Web Ambari, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você entende como autenticar usando uma chave SSH, aprenda a usar MapReduce com Hadoop no HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


