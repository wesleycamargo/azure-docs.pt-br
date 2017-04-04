---
title: Usar SSH com HDInsight (Hadoop) no Windows, Linux, Unix ou OS X | Microsoft Docs
description: " Você pode acessar o HDInsight usando o Secure Shell (SSH). Este documento fornece informações sobre como usar SSH para conectar-se ao HDInsight por meio de clientes Windows, Linux, Unix ou OS X."
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
ms.date: 03/16/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 89d44476e9de8ac32195efaf66535cdd9fb4260e
ms.lasthandoff: 03/25/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Conectar ao HDInsight (Hadoop) usando o SSH

Saiba como usar [SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) para se conectar com segurança ao HDInsight. O HDInsight pode usar o Linux (Ubuntu) como o sistema operacional para os nós no cluster. O SSH pode ser usado para se conectar aos nós principal e de borda de um cluster baseado em Linux e executar comandos diretamente nesses nós.

A tabela a seguir contém as informações de endereço e porta necessárias ao se conectar ao HDInsight usando SSH:

| Endereço | Porta | Conecta-se a... |
| ----- | ----- | ----- |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Nó de borda (se houver) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nó de cabeçalho primário |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nó de cabeçalho secundário |

> [!NOTE]
> Substitua `<edgenodename>` pelo nome do nó de borda. Para obter mais informações sobre o uso de nós de borda, confira [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).
>
> Substitua `<clustername>` pelo nome do cluster HDInsight.
>
> É recomendável __sempre se conectar ao nó de borda__, se você tiver um. Os nós de cabeçalho hospedam serviços que são essenciais para a integridade do cluster. O nó de borda executa apenas o que você coloca nele.

## <a name="ssh-clients"></a>Clientes SSH

A maioria dos sistemas operacionais oferece o cliente `ssh`. Por padrão, o Microsoft Windows não fornece um cliente SSH. Um cliente SSH para o Windows está disponível em cada um dos seguintes pacotes:

* [Bash no Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about): o comando `ssh` é fornecido por meio do Bash na linha de comando do Windows.

* [Git (https://git-scm.com/)](https://git-scm.com/): o comando `ssh` é fornecido por meio da linha de comando de GitBash.

* [Área de trabalho do GitHub (https://desktop.github.com/)](https://desktop.github.com/) O comando `ssh` é fornecido por meio da linha de comando de Git Shell. A Área de Trabalho do GitHub pode ser configurada para usar o Bash, o Prompt de Comando do Windows ou o PowerShell como a linha de comando para o Shell do Git.

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH): a equipe do PowerShell está realizando a portabilidade do OpenSSH para o Windows e fornece versões de teste.

    > [!WARNING]
    > O pacote OpenSSH inclui o componente de servidor SSH, `sshd`. Esse componente inicia um servidor SSH no sistema, permitindo que outras pessoas se conectem a ele. Não configure esse componente nem abra a porta 22, a menos que você deseje hospedar um servidor SSH no sistema. Não é necessário se comunicar com o HDInsight.

Também há vários clientes SSH gráficos, como [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) e [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Embora esses clientes possam ser usados para se conectar ao HDInsight, o processo de conexão a um servidor é diferente usando o utilitário `ssh`. Para obter mais informações, confira a documentação do cliente gráfico que você está usando.

## <a id="sshkey"></a>Autenticação: chaves SSH

As chaves SSH usam a [Criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) para proteger o cluster. Chaves SSH são mais seguras do que senhas e fornecem uma maneira fácil de proteger o cluster HDInsight.

Se a conta SSH for protegida usando uma chave, o cliente deverá fornecer a chave privada correspondente ao se conectar:

* A maioria dos clientes pode ser configurada para usar uma __chave padrão__. Por exemplo, o cliente `ssh` procura uma chave privada em `~/.ssh/id_rsa` em ambientes Unix e Linux.

* Você pode especificar o __caminho para uma chave particular__. Com o cliente `ssh`, o parâmetro `-i` é usado para especificar o caminho para a chave privada. Por exemplo: `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Se você tiver __várias chaves privadas__ para uso com servidores diferentes, utilitários como [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) poderão ser usados para selecionar automaticamente a chave a ser usada.

> [!IMPORTANT]
>
> Se proteger a chave privada com uma senha, você deverá inserir a senha ao usar a chave. Utilitários como o `ssh-agent` podem armazenar a senha em cache para conveniência.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Use o comando `ssh-keygen` para criar arquivos de chaves públicas e privadas. O comando a seguir gera um par de chaves RSA de 2048 bits que podem ser usadas com o HDInsight:

    ssh-keygen -t rsa -b 2048

Você será solicitado a fornecer informações durante o processo de criação de chave. Por exemplo, onde as chaves são armazenadas ou se deseja usar uma frase secreta. Após a conclusão do processo, dois arquivos são criados: uma chave pública e uma chave privada.

* A __chave pública__ é usada para criar um cluster HDInsight. A chave pública tem uma extensão de `.pub`.

* A __chave privada__ é usado para autenticar o cliente no cluster HDInsight.

> [!IMPORTANT]
> Você pode proteger as chaves usando uma frase secreta. Isso é efetivamente uma senha na chave privada. Mesmo se alguém obtiver a chave privada, deverá ter a senha para usar a chave.

### <a name="create-hdinsight-using-the-public-key"></a>Criar o HDInsight usando a chave pública

| Método de criação | Como usar a chave pública |
| ------- | ------- |
| **Portal do Azure** | Desmarque __Usar a mesma senha como logon do cluster__ e selecione __Chave Pública__ como o tipo de autenticação SSH. Por fim, selecione o arquivo de chave pública ou cole o conteúdo do arquivo de texto do campo __Chave pública SSH__.</br>![Caixa de diálogo de chave pública SSH na criação do cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **PowerShell do Azure** | Use o parâmetro `-SshPublicKey` do cmdlet `New-AzureRmHdinsightCluster` e passe o conteúdo da chave pública como uma cadeia de caracteres.|
| **CLI 1.0 do Azure** | Use o parâmetro `--sshPublicKey` do `azure hdinsight cluster create` comando e passe o conteúdo da chave pública como uma cadeia de caracteres. |
| **Modelo do Resource Manager** | Para obter um exemplo de como usar chaves SSH com um modelo, confira [Implantar o HDInsight no Linux com uma chave SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). O elemento `publicKeys` o arquivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) são usados para passar as chaves do Azure ao criar o cluster. |

## <a id="sshpassword"></a>Autenticação: senha

Contas SSH podem ser protegidas usando uma senha. Ao se conectar ao HDInsight usando SSH, você precisará digitar a senha.

> [!WARNING]
> Não recomendamos o uso da autenticação de senha para o SSH. As senhas podem ser adivinhadas e são vulneráveis a ataques de força bruta. Em vez disso, é recomendável usar [chaves SSH para autenticação](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Criar o HDInsight usando uma senha

| Método de criação | Como especificar a senha |
| --------------- | ---------------- |
| **Portal do Azure** | Por padrão, a conta de usuário SSH tem a mesma senha que a conta de logon do cluster. Para usar uma senha diferente, desmarque __Usar a mesma senha como logon do cluster__e digite a senha no campo __Senha SSH__.</br>![Caixa de diálogo de senha SSH na criação do cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **PowerShell do Azure** | Use o parâmetro `--SshCredential` do cmdlet `New-AzureRmHdinsightCluster` e passe um objeto `PSCredential` que contém o nome da conta de usuário SSH e a senha. |
| **CLI 1.0 do Azure** | Use o parâmetro `--sshPassword` do comando `azure hdinsight cluster create` e forneça o valor da senha. |
| **Modelo do Resource Manager** | Para obter um exemplo de como usar uma senha com um modelo, confira [Implantar o HDInsight no Linux com uma senha SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). O elemento `linuxOperatingSystemProfile` no arquivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) é usado para passar o nome de conta do SSH e a senha para o Azure ao criar o cluster.|

### <a name="change-the-ssh-password"></a>Alterar a senha SSH

Para obter informações sobre como alterar a senha de conta de usuário do SSH, confira a seção __Alterar senhas__ do documento [Gerenciar o HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a id="domainjoined"></a>Autenticação: HDInsight associado ao domínio

Se estiver usando um __cluster HDInsight associado ao domínio__, você deverá usar o comando `kinit` após a conexão com o SSH. Este comando solicita um usuário de domínio e uma senha e autentica a sessão com o domínio do Azure Active Directory associado ao cluster.

Para obter mais informações, confira [Configurar o HDInsight associado ao domínio](hdinsight-domain-joined-configure.md).

## <a name="connect-to-worker-and-zookeeper-nodes"></a>Conectar-se ao trabalho e aos nós Zookeeper

Os nós de trabalho e Zookeeper não são diretamente acessíveis da Internet, mas podem ser acessados por meio dos nós principais de cluster ou nós de borda. A seguir estão as etapas gerais para se conectar-se a outros nós:

1. Use o SSH para se conectar a um nó de cabeçalho ou de borda:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. Da conexão SSH para o nó de cabeçalho ou de borda, use o comando `ssh` para se conectar a um nó de trabalho no cluster:

        ssh sshuser@wn0-myhdi

    Para recuperar uma lista dos nomes de domínio de nós do cluster, confira os exemplos no documento [Administrar o HDInsight usando a API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Se a conta SSH for protegida usando uma __senha__, você será solicitado a inserir a senha e a conexão será estabelecida.

Se a conta SSH for protegida usando __chaves SSH__, assegure-se de que seu ambiente local está configurado para encaminhamento do agente SSH.

> [!NOTE]
> Outra maneira de acessar diretamente todos os nós do cluster é instalar o HDInsight em uma Rede Virtual do Azure. Em seguida, você pode associar o computador remoto à mesma rede virtual e acessar diretamente todos os nós no cluster.
>
> Para obter mais informações, confira [Usar uma rede virtual com o HDInsight](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>Configurar o encaminhamento do agente SSH

> [!IMPORTANT]
> As etapas a seguir pressupõem o uso de um sistema com base em Linux/UNIX e funcionam com Bash no Windows 10. Se essas etapas não funcionarem para o seu sistema, você precisará conferir a documentação de seu cliente SSH.

1. Usando um editor de texto, abra `~/.ssh/config`. Se esse arquivo não existir, você poderá criá-lo digitando `touch ~/.ssh/config` na linha de comando.

2. Adicione o texto a seguir ao arquivo `config`.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Substitua as informações de __Host__ pelo endereço do nó ao qual você se conecta usando o SSH. O exemplo anterior usa o nó de borda. Essa entrada configura o encaminhamento de agente SSH para o nó especificado.

3. Teste o encaminhamento do agente SSH usando o seguinte comando no terminal:

        echo "$SSH_AUTH_SOCK"

    Esse comando retorna informações semelhantes ao seguinte texto:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Se nada for retornado, o `ssh-agent` não estará em execução. Confira as informações de scripts de inicialização de agente em [usando ssh-agent com ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) ou confira a documentação do cliente SSH para obter as etapas específicas de instalação e configuração `ssh-agent`.

4. Após confirmar que o **ssh-agent** está em execução, use o seguinte para adicionar sua chave privada SSH ao agente:

        ssh-add ~/.ssh/id_rsa

    Se a chave privada estiver armazenada em um arquivo diferente, substitua `~/.ssh/id_rsa` pelo caminho para o arquivo.

5. Conecte-se ao nó de borda de cluster ou aos nós de cabeçalho usando o SSH. Em seguida, use o comando SSH para se conectar a um nó de trabalho ou zookeeper. A conexão é estabelecida usando a chave encaminhada.

## <a name="next-steps"></a>Próximas etapas

* [Usar o túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Usar uma rede virtual com o HDInsight](hdinsight-extend-hadoop-virtual-network.md)
* [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
