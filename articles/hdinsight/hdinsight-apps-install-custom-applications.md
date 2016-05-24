<properties
   	pageTitle="Instalar aplicativos do Hadoop no HDInsight | Microsoft Azure"
   	description="Saiba como instalar aplicativos do HDInsight em aplicativos do HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/18/2016"
   	ms.author="jgao"/>

# Instalar aplicativos personalizados do HDInsight

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster HDInsight baseado em Linux. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria. Neste artigo, você aprenderá a instalar um aplicativo do HDInsight, que não foi publicado no portal do Azure, no HDInsight. O aplicativo que você irá instalar é o [Hue](http://gethue.com/).

Outros artigos relacionados:

- [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
- [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.

 
## Pré-requisitos

Se você quiser instalar aplicativos do HDInsight em um cluster HDInsight existente, precisará ter um cluster HDInsight. Para criar um, confira [Criar clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Você também pode instalar aplicativos do HDInsight quando cria um cluster HDInsight.


## Instalar o aplicativo do HDInsight

Os aplicativos do HDInsight podem ser instalados em um cluster HDInsight existente ou durante a criação de um cluster. Para definir modelos do ARM, confira [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

Arquivos necessários para implantar esse aplicativo (Hue):

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modelo do ARM para a instalação do aplicativo do HDInsight. Confira [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) para desenvolver seu próprio modelo do ARM.
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): ação de script a ser chamada pelo modelo do ARM para configurar o nó de borda. 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): o arquivo binário do Hue chamado de hui-install\_v0.sh. 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): o arquivo binário do Hue sendo chamado de hui-install\_v0.sh. 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): um aplicativo Web de exemplo (Tomcat) sendo chamado de hui-install\_v0.sh.

**Para instalar o Hue em um cluster HDInsight existente**

1. Clique na imagem a seguir para entrar no Azure e abra o modelo ARM no Portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Esse botão abre um modelo de ARM no portal do Azure. O modelo do ARM está localizado em [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue). Para aprender a escrever esse modelo do ARM, confira [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
    
2. Na folha **Parâmetros**, insira o seguinte:

    - **ClusterName**: insira o nome do cluster onde você deseja instalar o aplicativo. Esse cluster deve ser um cluster existente.
    
3. Clique em **OK** para salvar os parâmetros.
4. Na folha **Implantação personalizada**, digite **Grupo de recursos**. O grupo de recursos é um contêiner que agrupa o cluster, a conta de armazenamento dependente e outros recursos. É necessário usar o mesmo grupo de recursos do cluster.
5. Clique em **Termos legais** e em **Criar**.
6. Verifique se a caixa de seleção **Fixar no painel** está marcada e clique em **Criar**. Você pode ver o status da instalação no bloco ao painel do portal e na notificação no portal (clique no ícone de sino na parte superior do portal). Demora cerca de 10 minutos para instalar o aplicativo.

**Para instalar o Hue durante a criação de um cluster**

1. Clique na imagem a seguir para entrar no Azure e abra o modelo ARM no Portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Esse botão abre um modelo de ARM no portal do Azure. O modelo do ARM está localizado em [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json). Para aprender a escrever esse modelo do ARM, confira [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

2. Siga as instruções para criar o cluster e instalar o Hue. Para saber mais sobre a criação de clusters HDInsight, confira [Criar clusters Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## validar a instalação

Você pode verificar o status do aplicativo no portal do Azure para validar a instalação do aplicativo. Além disso, você também poderá validar todos os pontos de extremidade HTTP, se estiverem conforme o esperado, e a página da Web, se houver:

**Para abrir o portal do Hue**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu esquerdo. Se você não consegue ver a opção, clique em **Procurar** e clique em **Clusters HDInsight**.
3. Clique no cluster em que você instalou o aplicativo.
4. Na folha **Configurações**, clique em **Aplicativos** na categoria **Geral**. Você deverá ver **hue** listado na folha **Aplicativos Instalados**.
5. Clique em **hue** na lista para listar as propriedades.  
6. Clique no link da página da Web para validar o site. Abra o ponto de extremidade HTTP em um navegador para validar a interface do usuário Web do Hue e abra o ponto de extremidade SSH usando [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) ou outros [clientes SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## Solucionar problemas de instalação

Confira a seção [Solucionar problemas](hdinsight-apps-install-applications.md#troubleshoot) em Instalar aplicativos do HDInsight.


## Próximas etapas

- [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como desenvolver modelos do ARM para implantar aplicativos do HDInsight.
- [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
- [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
- [Personalizar clusters Hadoop baseados em Linux no HDInsight usando modelos do ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do ARM para criar clusters HDInsight.

<!---HONumber=AcomDC_0518_2016-->