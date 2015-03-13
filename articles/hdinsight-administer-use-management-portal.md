<properties 
	pageTitle="Gerenciar clusters do Hadoop no HDInsight usando o Portal do Azure | Azure" 
	description="Saiba como administrar o serviço do HDInsight. Crie um cluster HDInsight, abra o console interativo do JavaScript e abra o console de comando Hadoop." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="jgao"/>



# Gerenciar clusters Hadoop no HDInsight usando o Portal de Gerenciamento do Azure

Usando o Portal de Gerenciamento do Azure, você pode provisionar clusters do Hadoop no HDInsight, alterar a senha de usuário do Hadoop e habilitar o RDP para que possa acessar o console de comando do Hadoop no cluster. Também há outras ferramentas disponíveis para administração do HDInsight além do Portal de Gerenciamento. 

- Para obter mais informações sobre como administrar o HDInsight usando o PowerShell do Azure, consulte [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].

- Para obter mais informações sobre como administrar o HDInsight usando as Ferramentas de Linha de Comando entre plataformas, consulte [Administrar o HDInsight usando a Interface de Linha de Comando entre plataformas][hdinsight-admin-cross-platform]. 

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura do Azure**. O Azure é uma plataforma baseada em assinatura. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].


##Neste artigo

* [Provisionar clusters do HDInsight](#create)
* [Personalizar clusters do HDInsight](#customize)
* [Alterar o nome de usuário e a senha do cluster do HDInsight](#password)
* [Conectar aos clusters HDInsight usando o RDP](#rdp)
* [Crie um certificado autoassinado](#cert)
* [Conceder/revogar acesso aos serviços HTTP](#httpservice)
* [Abrir o console de comando do Hadoop](#hadoopcmd)
* [Próximas etapas](#nextsteps)

##<a id="create"></a> Provisionar clusters do HDInsight

Existem vários métodos para criar clusters do HDInsight, este artigo cobre apenas o uso da opção Criação Rápida no Portal de Gerenciamento do Azure. Para obter outras opções, consulte [Provisionar clusters do HDInsight][hdinsight-provision].

O cluster do HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para obter mais informações sobre como o Armazenamento de Blob do Azure fornece uma experiência perfeita com os clusters HDInsight, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

Uma conta de armazenamento do Azure deve ser criada no mesmo datacenter no qual seu cluster do HDInsight será provisionado. Atualmente, os clusters HDInsight podem ser provisionados em cinco data centers: 

- Sudeste Asiático 
- Norte da Europa
- Europa Ocidental 
- Leste dos EUA 
- Oeste dos EUA 

Para obter detalhes sobre como criar uma conta de armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].


**Para provisionar um cluster do HDInsight**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **NOVO** na parte inferior da página, clique em **SERVIÇOS DE DADOS**, clique em **HDINSIGHT** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.

3. Forneça o **Nome do Cluster**, o **Tamanho do Cluster**, a **Senha do Administrador do Cluster**e uma **Conta de Armazenamento** do Azure e, em seguida, clique em **Criar Cluster HDInsight**. Depois que o cluster estiver criado e em execução, o status será *Running*.

	![HDI.QuickCreate][image-cluster-quickcreate]

	Ao usar a opção Criação Rápida para criar um cluster, o nome de usuário padrão da conta de usuário do Hadoop é *admin*. Para dar à conta outro nome de usuário, você pode usar a opção Criação Personalizada em vez da opção Criação Rápida. Ou altere o nome da conta depois que ela for provisionada.

	Ao usar a opção de Criação Rápida para criar um cluster, um novo contêiner com o nome do cluster do HDInsight será criado automaticamente na conta de armazenamento especificada. Se desejar personalizar o nome do contêiner a ser usado por padrão pelo cluster, você deverá usar a opção Criação Personalizada. 

	> [AZURE.NOTE] Depois que uma conta de armazenamento do Azure for escolhida para seu cluster do HDInsight, a única maneira de alterar a conta de armazenamento será excluir o cluster e criar um novo cluster com a conta de armazenamento desejada.

4. Clique no cluster recém-criado.  Ele mostra a página de aterrissagem:

	![HDI.ClusterLanding][image-cluster-landing]


##<a id="customize"></a> Personalizar clusters do HDInsight

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e suportados, consulte [Qual versão do Hadoop está no HDInsight do Azure][hdinsight-versions]. A personalização do HDInsight pode ser feita usando uma das seguintes opções:

- Use os parâmetros de personalização do cluster no SDK do .NET do HDInsight ou no PowerShell do Azure durante o provisionamento do cluster. Quando isso é feito, essas alterações de configuração são preservadas durante o tempo de vida do cluster e não são afetadas pelas novas imagens do nó de cluster que a plataforma do Azure refaz periodicamente para manutenção. Para obter mais informações sobre como usar os parâmetros de personalização do cluster, consulte [Provisionamento de clusters HDInsight][hdinsight-provision].
- Alguns componentes nativos do Java, como o Mahout e o Cascading, podem ser executados no cluster como arquivos JAR. Esses arquivos JAR podem ser distribuídos para o Armazenamento de Blob do Azure (WASB) e enviados aos clusters do HDInsight usando mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs]. 


	>[AZURE.NOTE] Se você tiver problemas para implantar arquivos JAR nos clusters do HDInsight ou ao chamar arquivos JAR nesses clusters, entre em contato com o [Suporte da Microsoft][hdinsight-support].
	
	> O Cascading não dá suporte para HDInsight e não é qualificado para o Suporte da Microsoft. Para obter as listas dos componentes suportados, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][hdinsight-versions].


Não há suporte para a instalação de software personalizado no cluster usando uma conexão de área de trabalho remota. Evite armazenar arquivos nas unidades de nó de cabeçalho pois eles serão perdidos se você precisar recriar os clusters. Recomendamos armazenar os arquivos no Armazenamento de Blob do Azure. O Armazenamento de Blob é persistente.

##<a id="password"></a> Alterar o nome de usuário e a senha do cluster HDInsight
Um cluster do HDInsight pode ter duas contas de usuário.  A conta de usuário do cluster do HDInsight é criada durante o processo de provisionamento.  Você também pode criar uma conta de usuário RDP para acessar o cluster via RDP. Consulte [Habilitar área de trabalho remota](#enablerdp).

**Para alterar o nome do usuário e a senha do cluster HDInsight**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster do HDInsight cujo nome de usuário e senha deseja redefinir.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Clique em **DESATIVADO** ao lado  de **SERVIÇOS DO HADOOP**.
6. Clique em **SALVAR** na parte inferior da página e aguarde a conclusão da desativação.
7. Depois que o serviço foi desativado, clique em **ATIVADO** ao lado de **SERVIÇOS DO HADOOP**.
8. Digite o **NOME DE USUÁRIO** e a **NOVA SENHA**.  Esses serão o novo nome do usuário e a nova senha do cluster.
8. Clique em **SALVAR**.


##<a id="rdp"></a> Conectar a clusters HDInsight usando o RDP

As credenciais do cluster que você forneceu em sua criação concedem acesso aos serviços do cluster, mas não ao próprio cluster através da área de trabalho remota. O acesso à Área de Trabalho Remota é desativado por padrão e, portanto, o acesso direto ao cluster usando-a requer uma configuração adicional após a criação.

**Para habilitar a área de trabalho remota**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight ao qual você deseja se conectar.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Na parte inferior da página, clique em **HABILITAR REMOTO**.
6. No assistente **Configurar Área de Trabalho Remota**, digite um nome do usuário e uma senha para a área de trabalho remota. Observe que o nome do usuário deve ser diferente do usado para criar o cluster (*admin* por padrão com a opção de Criação Rápida). Insira uma data de expiração na caixa de diálogo **EXPIRA EM**. Observe que a data de validade deve estar no futuro e a não mais de uma semana a partir da data atual. O horário de validade do dia será considerada, por padrão, como meia-noite da data especificada. Em seguida, clique no ícone de verificação.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]

	A data de validade deve estar no futuro e a, no máximo, sete dias a partir da data atual. E o horário é a meia-noite da data selecionada.

> [AZURE.NOTE] Depois que o RDP for habilitado para um cluster, você deverá atualizar a página antes de conectar-se ao cluster.
 
**Para conectar-se ao cluster usando o RDP**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight ao qual você deseja se conectar.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Clique em **CONECTAR** e, em seguida, siga as instruções.

##<a id="cert"></a>Criar um certificado autoassinado

Se quiser realizar qualquer operação no cluster usando o SDK .NET, você deve criar um certificado autoassinado na estação de trabalho e carregar o certificado em sua assinatura do Azure. Essa é uma tarefa que só precisa ser executada uma vez. Você pode instalar o mesmo certificado em outras máquinas, desde que ele seja válido.

**Para criar um certificado autoassinado**

1. Crie um certificado autoassinado usado para autenticar as solicitações. Você pode usar o IIS ou o [makecert][makecert-info] para criar o certificado.
 
2. Navegue até o local do certificado, clique com o botão direito do mouse no certificado, clique em **Instalar Certificado** e instale o certificado no repositório pessoal do computador. Edite as propriedades do certificado para atribuir a ele um nome amigável.

3. Importe o certificado no Portal de Gerenciamento do Azure. No portal, clique em **Configurações** no lado inferior esquerdo da página e clique em **Certificados de Gerenciamento**. Na parte inferior da página, clique em **Carregar** e sigas as instruções para carregar o arquivo .cer criado na etapa anterior.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##<a id="httpservice"></a> Conceder/revogar acesso aos serviços HTTP

Os clusters do HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso no Portal de Gerenciamento. 

>[AZURE.NOTE] Ao conceder/revogar o acesso, você redefinirá o nome de usuário e a senha do cluster.

**Para conceder/revogar acesso aos serviços Web HTTP**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster do HDInsight que deseja configurar.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Clique em **ATIVADO** ou **DESATIVADO** ao lado de **SERVIÇOS DO HADOOP**.  
6. Digite o **NOME DE USUÁRIO** e a **NOVA SENHA**.  Esses serão o novo nome do usuário e a nova senha do cluster.
7. Clique em **SALVAR**.

Isso também pode ser feito usando os cmdlets do PowerShell do Azure:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consulte [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].

##<a id="hadoopcmd"></a> Abrir a linha de comando do Hadoop

Para conectar-se ao cluster usando a área de trabalho remota e usar a linha de comando do Hadoop, você deve primeiro habilitar o acesso à área de trabalho remota para o cluster conforme descrito na seção anterior. 

**Para abrir a linha de comando do Hadoop**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters Hadoop implantados.
3. Clique no cluster HDInsight ao qual você deseja se conectar.
3. Clique em **CONFIGURAÇÃO** na parte superior da página.
4. Clique em **Conectar** na parte inferior da página.
5. Clique em **Abrir**.
6. Digite suas credenciais e clique em **OK**.  Use o nome de usuário e a senha que você configurou quando criou o cluster.
7. Clique em **Sim**.
8. Na área de trabalho, clique duas vezes em **Linha de Comando do Hadoop**.
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Para obter mais informações sobre o comando Hadoop, consulte [Referência de comandos Hadoop][hadoop-command-reference].

Na captura de tela anterior, o nome da pasta tem o número de versão do Hadoop incorporado. O número de versão pode ser alterado com base na versão dos componentes do Hadoop instalados no cluster. Você pode usar variáveis de ambiente do Hadoop para referir-se a essas pastas.  Por exemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%

##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu como criar um cluster do HDInsight usando o Portal de Gerenciamento do Azure e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
* [Administrar o HDInsight usando a Interface de linha de comando entre plataformas][hdinsight-admin-cross-platform]
* [Provisionar clusters do HDInsight][hdinsight-provision]
* [Enviar trabalhos Hadoop de modo programático][hdinsight-submit-jobs]
* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Qual versão do Hadoop está no Azure HDInsight?][hdinsight-versions]

[hdinsight-admin-cross-platform]: ../hdinsight-administer-use-command-line/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-support]: http://azure.microsoft.com/support/options/
[makecert-info]: http://msdn.microsoft.com/library/bfsktky3(v=vs.110).aspx

[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hadoop-command-reference]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png
<!--HONumber=42-->
