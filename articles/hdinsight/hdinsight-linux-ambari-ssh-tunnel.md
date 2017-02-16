---
title: "Usar o túnel SSH para acessar os serviços do Azure HDInsight | Microsoft Docs"
description: "Saiba como usar um túnel SSH para navegar com segurança em recursos da Web hospedados em seus nós HDInsight baseados em Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: d9e6fe3bd2ddd511791b7b34ff8d7875a13417c0


---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Usar o Túnel SSH para acessar a interface do usuário do Ambari na Web, JobHistory, NameNode, Oozie, entre outras
Os clusters do HDInsight baseados em Linux oferecem acesso à interface do usuário do Ambari Web pela Internet, mas alguns recursos da interface do usuário não são acessados. Por exemplo, a interface do usuário da Web para outros serviços exibidos por meio do Ambari. Para obter a funcionalidade completa da interface do usuário do Ambari Web, você deverá usar um túnel SSH para o início do cluster.

## <a name="what-requires-an-ssh-tunnel"></a>O que requer um túnel SSH?
Vários dos menus do Ambari não serão totalmente preenchidos sem um túnel SSH porque se baseiam em sites e em serviços expostos a outros serviços do Hadoop em execução no cluster. Geralmente, esses sites não são protegidos e, portanto, não é seguro expô-los diretamente na Internet. Às vezes, o serviço executa o site em outro nó de cluster, como um nó do Zookeeper.

A seguir, os serviços usados pela interface do usuário do Ambari Web que não podem ser acessados sem um túnel SSH:

* JobHistory,
* NameNode,
* Pilhas de Thread,
* Interface do usuário da Web do Oozie
* Interface do usuário mestre e de logs do HBase

Se você usar as Ações de Script para personalizar seu cluster, todos os serviços ou utilitários que forem instalados e que expuserem a interface do usuário da Web exigirão um túnel SSH. Por exemplo, se você instalar o Hue usando uma Ação de Script, deverá usar um túnel SSH para acessar a interface do usuário da Web do Hue.

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH?
[Túnel SSH](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) roteia o tráfego enviado para uma porta em sua estação de trabalho local por meio de uma conexão SSH com o nó de cabeçalho do cluster HDInsight, onde a solicitação é resolvida como se tivesse sido originada no nó de cabeçalho. A resposta é então roteada de volta pelo túnel até a sua estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos
Ao usar um túnel SSH para o tráfego da Web, você deverá ter o seguinte:

* Um cliente SSH. Para distribuições Linux e Unix ou o Macintosh OS X, o comando `ssh` é fornecido com o sistema operacional. Para sistemas Windows, é recomendável [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
  
  > [!NOTE]
  > Se você quiser usar um cliente SSH diferente de `ssh` ou PuTTY, consulte a documentação de seu cliente sobre como estabelecer um túnel SSH.
  > 
  > 
* Um navegador da Web que pode ser configurado para usar um proxy SOCKS

## <a name="a-nameusesshacreate-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Criar um túnel usando o comando SSH
Use o comando a seguir para criar um túnel SSH usando o comando `ssh` . Substitua **USERNAME** por um usuário SSH para seu cluster HDInsight e substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Isso cria uma conexão que encaminha o tráfego para a porta local 9876 do cluster via SSH. As opções são:

* **D 9876** : a porta local que roteará o tráfego pelo túnel.
* **C** : compactar todos os dados, porque o tráfego da Web é texto, em sua maioria.
* **2** : forçar o SSH para tentar somente a versão 2 do protocolo.
* **q** : modo silencioso.
* **T** : desabilitar alocação pseudo-tty, já que estamos apenas encaminhando uma porta.
* **n** : impede a leitura de STDIN, já que estamos apenas encaminhando uma porta.
* **N** : não executar um comando remoto, pois estamos apenas encaminhando uma porta.
* **f** : executar em segundo plano.

Se você tiver configurado o cluster com uma chave SSH, talvez seja necessário usar o parâmetro `-i` e especificar o caminho para a chave privada de SSH.

Quando o comando terminar, o tráfego enviado para a porta 9876 no computador local será roteado pelo protocolo SSL para o nó de cabeçalho do cluster e parecerá originar-se de lá .

## <a name="a-nameuseputtyacreate-a-tunnel-using-putty"></a><a name="useputty"></a>Criar um túnel usando o PuTTY
Use as etapas a seguir para criar um túnel SSH usando o PuTTY.

1. Abra o PuTTY e insira as informações da sua conexão. Se você não estiver familiarizado com o PuTTY, confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como usá-lo com o HDInsight.
2. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e selecione **Túneis**.
3. Forneça as seguintes informações no formulário **Opções de controle do encaminhamento de porta SSH** :
   
   * **Porta de Origem** : a porta no cliente que você deseja encaminhar. Por exemplo, **9876**.
   * **Destino** : o endereço SSH para o cluster HDInsight baseado em Linux. Por exemplo, **mycluster-ssh.azurehdinsight.net**.
   * **Dinâmico** : habilita roteamento de proxy SOCKS dinâmico.
     
     ![imagem de opções de túnel](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)
4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.
5. Quando solicitado, faça logon no servidor. Isso estabelecerá uma sessão SSH e habilitará o túnel.

## <a name="use-the-tunnel-from-your-browser"></a>Usar o túnel de seu navegador
> [!NOTE]
> As etapas desta seção usam o navegador Firefox, pois ele está disponível gratuitamente para os sistemas Linux, Unix, Macintosh OS X e Windows. Outros navegadores modernos que sejam compatíveis com o uso do proxy SOCKS também funcionarão.
> 
> 

1. Configure o navegador para usar **localhost:9876** como um proxy **SOCKS v5**. Aqui está a aparência das configurações do Firefox. Se você tiver usado uma porta diferente da 9876, altere a porta que usou:
   
    ![imagem das configurações do Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)
   
   > [!NOTE]
   > Selecionar **DNS Remoto** resolverá as solicitações de DNS (Sistema de Nomes de Domínio) usando o cluster HDInsight. Se essa opção estiver desmarcada, o DNS será resolvido localmente.
   > 
   > 
2. Verifique se o tráfego está sendo roteado pelo túnel visitando um site como [http://www.whatismyip.com/](http://www.whatismyip.com/) com as configurações de proxy habilitadas e desabilitadas no Firefox. Embora as configurações estejam habilitadas, o endereço IP será de um computador no datacenter do Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verifique com a interface do usuário do Ambari Web
Assim que o cluster tiver sido estabelecido, use as etapas a seguir para verificar se você pode acessar as interfaces do usuário da Web do serviço Ambari Web:

1. Em seu navegador, vá para http://headnodehost:8080. O endereço `headnodehost` será enviado pelo túnel para o cluster e resolverá o nó principal Ambari em execução. Quando solicitado, insira o nome de usuário do administrador (admin) e a senha do seu cluster. Talvez a interface do usuário do Ambari Web seja solicitada uma segunda vez. Nesse caso, insira novamente as informações.
   
   > [!NOTE]
   > Ao usar o endereço http://headnodehost:8080 para se conectar ao cluster, você está se conectando diretamente através do túnel com o nó principal que o Ambari está executando ao utilizar o HTTP, e a comunicação é protegida usando o túnel SSH. Ao se conectar pela Internet sem o uso de um túnel, a comunicação é protegida usando o HTTPS. Para se conectar pela Internet usando o HTTPS, use https://CLUSTERNAME.azurehdinsight.net, em que **CLUSTERNAME** é o nome do cluster.
   > 
   > 
2. Na interface do usuário do Ambari na Web, selecione HDFS na lista à esquerda da página.
   
    ![Imagem com o HDFS selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)
3. Quando as informações do serviço HDFS forem exibidas, selecione **Links Rápidos**. Será exibida uma lista de nós de cabeçalho do cluster. Escolha um dos nós de cabeça e **Interface de Usuário do NameNode**.
   
    ![Imagem do menu Links Rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)
   
   > [!NOTE]
   > Se você tiver uma conexão lenta com a Internet ou se o nó de cabeçalho estiver muito ocupado, será possível obter um indicador de espera em vez de um menu quando você selecionar **Links Rápidos**. Nesse caso, aguarde um minuto ou dois pelos dados a serem recebidos do servidor e experimente a lista novamente.
   > 
   > Se o monitor de resolução for inferior, ou se a janela do navegador não estiver maximizada, algumas entradas do menu **Links Rápidos** poderão ser cortadas no lado direito da tela. Nesse caso, expanda o menu usando o mouse e a tecla de seta para a direita para rolar a tela para a direita e ver o restante do menu.
   > 
   > 
4. Você verá uma página semelhante à seguinte:
   
    ![Imagem da interface do usuário do NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)
   
   > [!NOTE]
   > Observe a URL da página; ela deve ser semelhante a **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Ela está usando o nome de domínio totalmente qualificado (FQDN) interno do nó e não pode ser acessada sem a utilização de um túnel SSH.
   > 
   > 

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a criar e a usar um túnel SSH, consulte o seguinte para obter informações sobre como monitorar e gerenciar o cluster usando o Ambari:

* [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

Para obter mais informações sobre como usar SSH com o HDInsight, consulte o seguinte:

* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)




<!--HONumber=Jan17_HO4-->


