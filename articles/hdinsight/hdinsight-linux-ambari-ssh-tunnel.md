<properties
pageTitle="Usar o Túnel SSH para acessar a interface do usuário do Ambari Web, ResourceManager, JobHistory, NameNode, Oozie, entre outras"
description="Saiba como usar um túnel SSH para navegar com segurança em recursos da Web hospedados em seus nós HDInsight baseados em Linux."
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
ms.date="10/26/2015"
ms.author="larryfr"/>

#Usar túnel SSH para acessar a interface do usuário do Ambari Web, ResourceManager, JobHistory, NameNode, Oozie, entre outras

Os clusters do HDInsight baseados em Linux oferecem acesso à interface do usuário do Ambari Web pela Internet, mas alguns recursos da interface do usuário não são acessados. Por exemplo, a interface do usuário da Web para outros serviços exibidos por meio do Ambari. Para obter a funcionalidade completa da interface do usuário do Ambari Web, você deverá usar um túnel SSH para o início do cluster.

##O que requer um túnel SSH?

Vários dos menus do Ambari não serão totalmente preenchidos sem um túnel SSH porque se baseiam em sites e em serviços expostos a outros serviços do Hadoop em execução no cluster. Geralmente, esses sites não são protegidos e, portanto, não é seguro expô-los diretamente na Internet. Às vezes, o serviço executa o site em outro nó de cluster, como um nó do Zookeeper.

A seguir, os serviços usados pela interface do usuário do Ambari Web que não podem ser acessados sem um túnel SSH:

* ResourceManager,
* JobHistory,
* NameNode,
* Pilhas de Thread,
* Interface do usuário da Web do Oozie
* Interface do usuário mestre e de logs do HBase
* Interface do Usuário do Storm

Se você usar as Ações de Script para personalizar seu cluster, todos os serviços ou utilitários que forem instalados e que expuserem a interface do usuário da Web exigirão um túnel SSH. Por exemplo, se você instalar o Hue usando uma Ação de Script, deverá usar um túnel SSH para acessar a interface do usuário da Web do Hue.

##O que é um túnel SSH?

O [Túnel SSH](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) roteia o tráfego enviado para uma porta em sua estação de trabalho local por meio de uma conexão SSH com o nó de cabeçalho do cluster HDInsight, onde a solicitação é resolvida como se tivesse sido originada no nó de cabeçalho. A resposta é então roteada de volta pelo túnel até a sua estação de trabalho.

##Pré-requisitos

Ao usar um túnel SSH para o tráfego da Web, você deverá ter o seguinte:

* Um cliente SSH. Para distribuições do Linux e do Unix ou o Macintosh OS X, o comando `ssh` é fornecido com o sistema operacional. Para o Windows, recomendamos o [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE]Se você quiser usar um cliente SSH diferente do `ssh` ou o PuTTY, consulte a documentação de seu cliente sobre como estabelecer um túnel SSH.

* Um navegador da Web que pode ser configurado para usar um proxy SOCKS

* __(opcional)__: um plug-in como o [FoxyProxy](http://getfoxyproxy.org/,) que pode aplicar regras que só roteiam solicitações específicas pelo túnel.

	> [AZURE.WARNING]Sem um plug-in como o FoxyProxy, todas as solicitações feitas por meio do navegador poderão ser roteadas pelo túnel. Isso pode resultar em um carregamento mais lento de páginas da Web em seu navegador.

##<a name="usessh"></a>Criar um túnel usando o comando SSH

Use o comando a seguir para criar um túnel SSH usando o comando `ssh`. Substitua __NOMEDEUSUÁRIO__ por um usuário SSH para seu cluster HDInsight e substitua __NOMEDOCLUSTER__ pelo nome do seu cluster HDInsight

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Isso cria uma conexão que encaminha o tráfego para a porta local 9876 do cluster via SSH. As opções são:

* **D 9876**: a porta local que roteará o tráfego pelo túnel.

* **C**: compactar todos os dados, porque o tráfego da Web é texto, em sua maioria.

* **2**: forçar o SSH para tentar somente a versão 2 do protocolo.

* **q**: modo silencioso.

* **T**: desabilitar alocação pseudo-tty, já que estamos apenas encaminhando uma porta.

* **n**: impede a leitura de STDIN, já que estamos apenas encaminhando uma porta.

* **N**: não executar um comando remoto, pois estamos apenas encaminhando uma porta.

* **f**: executar em segundo plano.

Se você tiver configurado o cluster com uma chave SSH, talvez seja necessário usar o parâmetro `-i` e especificar o caminho para a chave privada de SSH.

Quando o comando terminar, o tráfego enviado para a porta 9876 no computador local será roteado pelo protocolo SSL para o nó de cabeçalho do cluster e parecerá originar-se de lá .

##<a name="useputty"></a>Criar um túnel usando o PuTTY

Use as etapas a seguir para criar um túnel SSH usando o PuTTY.

1. Abra o PuTTY e insira as informações da sua conexão. Se você não estiver familiarizado com o PuTTY, confira [Usar o SSH com o Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como usá-lo com o HDInsight.

2. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e selecione **Túneis**.

3. Forneça as seguintes informações no formulário **Opções de controle do encaminhamento de porta SSH**:

	* **Porta de Origem**: a porta no cliente que você deseja encaminhar. Por exemplo, **9876**.

	* **Destino**: o endereço SSH para o cluster HDInsight baseado em Linux. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

	* **Dinâmico**: habilita roteamento de proxy SOCKS dinâmico.

	![imagem de opções de túnel](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.

5. Quando solicitado, faça logon no servidor. Isso estabelecerá uma sessão SSH e habilitará o túnel.

##Usar o túnel de seu navegador

> [AZURE.NOTE]As etapas desta seção usam o navegador Firefox, pois ele está disponível gratuitamente para os sistemas Linux, Unix, Macintosh OS X e Windows. Outros navegadores modernos, como o Google Chrome, o Microsoft Edge ou o Apple Safari também devem funcionar; no entanto, é possível que o plug-in FoxyProxy usado em algumas etapas não esteja disponível para todos os navegadores.

1. Configure o navegador para usar **localhost:9876** como um proxy **SOCKS v5**. Aqui está a aparência das configurações do Firefox. Se você tiver usado uma porta diferente da 9876, altere a porta que usou:

	![imagem das configurações do Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE]Selecionar **DNS Remoto** resolverá as solicitações de DNS (Sistema de Nomes de Domínio) usando o cluster HDInsight. Se essa opção estiver desmarcada, o DNS será resolvido localmente.

2. Verifique se o tráfego está sendo roteado pelo túnel visitando um site como [http://www.whatismyip.com/](http://www.whatismyip.com/) com as configurações de proxy habilitadas e desabilitadas no Firefox. Embora as configurações estejam habilitadas, o endereço IP será de um computador no datacenter do Microsoft Azure.

###Extensões do navegador

Embora configurar o navegador para usar o túnel funcione, geralmente não deseja encaminhar todo o tráfego através do túnel. As extensões de navegador, como o [FoxyProxy](http://getfoxyproxy.org/), aceitam a correspondência de padrão para solicitações de URL (somente o FoxyProxy Standard ou Plus) e, portanto, apenas as solicitações de URLs específicas serão enviadas pelo túnel.

Se você tiver instalado o FoxyProxy Standard, use as seguintes etapas para configurá-lo para encaminhar o tráfego apenas para HDInsight pelo túnel.

1. Abra a extensão FoxyProxy no seu navegador. Por exemplo, no Firefox, selecione o ícone FoxyProxy ao lado do campo de endereço.

	![ícone do foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. Selecione **Adicionar Novo Proxy**, clique na guia **Geral** e insira um nome de proxy de **HDInsightProxy**.

	![foxyproxy geral](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. Selecione a guia **Detalhes de Proxy** e preencha os campos a seguir:

	* **Host ou Endereço IP**: é localhost, já que estamos usando um túnel SSH no computador local.

	* **Porta**: é a porta usada para o túnel SSH.

	* **Proxy SOCKS**: selecione esta opção para habilitar o navegador a usar o túnel como proxy.

	* **SOCKS v5**: selecione esta opção para definir a versão necessária do proxy.

	![proxy do foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. Clique na guia **Padrões de URL** e selecione **Adicionar Novo Padrão**. Use o seguinte para definir o padrão e clique em **OK**:

	* **Nome padrão** – **clusternodes** – Apenas um nome amigável para o padrão.

	* **Padrão de URL** – ***internal.cloudapp.net*** – Isso define um padrão que corresponde ao nome de domínio totalmente qualificado interno de nós do cluster.

	![padrão do foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

4. Clique em **OK** para adicionar o proxy e fechar **Configurações de Proxy**.

5. Na parte superior da caixa de diálogo FoxyProxy, altere **Modo de Seleção** para **Usar proxies com base em seus padrões e prioridades predefinidos** e clique em **Fechar**.

	![modo de seleção do foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

Após a execução destas etapas, somente solicitações de URLs que contêm a cadeia de caracteres __internal.cloudapp.net__ serão roteadas pelo túnel SSL.

##Verifique com a interface do usuário do Ambari Web

Assim que o cluster tiver sido estabelecido, use as etapas a seguir para verificar se você pode acessar as interfaces do usuário da Web do serviço Ambari Web:

1. Em seu navegador, vá para https://CLUSTERNAME.azurehdinsight.net, em que CLUSTERNAME é o nome do cluster HDInsight.

	Quando solicitado, insira o nome de usuário do administrador (admin) e a senha do seu cluster. Talvez a interface do usuário do Ambari Web seja solicitada uma segunda vez. Nesse caso, insira novamente as informações.

2. Na interface do usuário do Ambari Web, selecione YARN na lista à esquerda da página.

	![Imagem com o YARN selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/yarnservice.png)

3. Quando as informações do serviço YARN forem exibidas, selecione __Links Rápidos__. Será exibida uma lista de nós de cabeçalho do cluster. Selecione um dos nós de cabeçalho e então selecione __Interface de Usuário do ResourceManager__.

	![Imagem do menu Links Rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/yarnquicklinks.png)

	> [AZURE.NOTE]Se você tiver uma conexão lenta com a Internet ou se o nó de cabeçalho estiver muito ocupado, é possível obter um indicador de espera em vez de um menu quando você selecionar __Links Rápidos__. Nesse caso, aguarde um minuto ou dois pelos dados a serem recebidos do servidor e experimente a lista novamente.


	> [AZURE.TIP]Se o monitor de resolução for inferior, ou se a janela do navegador não estiver maximizada, algumas entradas do menu __Links Rápidos__ poderão ser cortadas no lado direito da tela. Nesse caso, expanda o menu usando o mouse e a tecla de seta para a direita para rolar a tela para a direita e ver o restante do menu.

4. Você verá uma página semelhante à seguinte:

	![Imagem da interface do usuário do ResourceManager YARN](./media/hdinsight-linux-ambari-ssh-tunnel/yarnresourcemanager.png)

	> [AZURE.TIP]Observe a URL dessa página; ela deverá ser semelhante a \___http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__. Ela está usando o nome de domínio totalmente qualificado (FQDN) interno do nó e não pode ser acessada sem a utilização de um túnel SSH.

##Próximas etapas

Agora que você aprendeu a criar e a usar um túnel SSH, consulte o seguinte para obter informações sobre como monitorar e gerenciar o cluster usando o Ambari:

* [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

Para obter mais informações sobre como usar SSH com o HDInsight, consulte o seguinte:

* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=Nov15_HO1-->