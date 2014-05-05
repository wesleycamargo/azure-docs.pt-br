<properties linkid="manage-services-hdinsight-howto-administer-hdinsight" urlDisplayName="Administração" pageTitle="Administrar clusters HDInsight com o Portal de Gerenciamento | Azure" metaKeywords="" description="Saiba como administrar o Serviço HDInsight. Crie um cluster HDInsight, abra o console interativo do JavaScript e abra o console de comando Hadoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Administrar clusters HDInsight usando o Portal de Gerenciamento" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />



# Administrar clusters HDInsight usando o Portal de Gerenciamento

Usando o Portal de Gerenciamento do Azure, você pode provisionar os clusters HDInsight, alterar a senha de usuário do Hadoop e habilitar o RDP para que possa acessar o console de comando do Hadoop no cluster. Também há outras ferramentas disponíveis para administração do HDInsight além do Portal de Gerenciamento. 

- Para obter mais informações sobre como administrar o HDInsight usando o PowerShell do Azure, consulte [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].

- Para obter mais informações sobre como administrar o HDInsight usando as Ferramentas de Linha de Comando entre plataformas, consulte [Administrar o HDInsight usando a Interface de Linha de Comando entre plataformas][hdinsight-admin-cross-platform]. 

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura do Azure**. O Azure é uma plataforma baseada em assinatura. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].


##Neste artigo

* [Provisione clusters HDInsight](#create)
* [Personalizar os clusters HDInsight](#customize)
* [Alterar o nome do usuário e a senha do cluster HDInsight](#password)
* [Conectar ao clusters HDInsight usando o RDP](#rdp)
* [Conceder/revogar acesso aos serviços HTTP](#httpservice)
* [Abrir o console de comando do Hadoop](#hadoopcmd)
* [Próximas etapas](#nextsteps)

##<a id="create"></a> Provisionar os clusters HDInsight

Existem vários métodos para criar os clusters HDInsight, este artigo cobre apenas o uso da opção Criação Rápida no Portal de Gerenciamento do Azure. Para obter outras opções, consulte [Provisionar os clusters HDInsight][hdinsight-provision].

O cluster HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para obter mais informações sobre como o Armazenamento de Blob do Azure fornece uma experiência perfeita com os clusters HDInsight, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

Uma conta de armazenamento do Azure deve ser criada no mesmo data center no qual seu cluster HDInsight irá ser provisionado. Atualmente os clusters HDInsight podem ser provisionados em cinco data centers: 

- Sudeste Asiático 
- Norte da Europa
- Europa Ocidental 
- Leste dos EUA 
- Oeste dos EUA 

Para obter detalhes sobre como criar uma conta de armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].


**Para provisionar o cluster HDInsight**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **NOVO** na parte inferior da página, clique em **SERVIÇOS DE DADOS**, clique em **HDINSIGHT** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.

3. Forneça o **Nome do Cluster**, o **Tamanho do Cluster**, a **Senha do Administrador do Cluster**e uma **Conta de Armazenamento** do Azure e, em seguida, clique em **Criar Cluster HDInsight**. Depois que o cluster estiver criado e em execução, o status será *Executando*.

	![HDI.QuickCreate][image-cluster-quickcreate]

	Ao usar a opção Criação Rápida para criar um cluster, o nome do usuário padrão da conta de administrador é *admin*. Para conceder à conta outro nome do usuário, você pode usar a opção Criação Personalizada em vez da opção Criação Rápida. Ou altere o nome da conta depois que ela for provisionada.

	Ao usar a opção Criação Rápida para criar um cluster, um novo contêiner com o nome do cluster HDInsight é criado automaticamente na conta de armazenamento especificada. Se desejar personalizar o nome do contêiner a ser usado por padrão pelo cluster, você deverá usar a opção Criação Personalizada. 

	> [WACOM.NOTE] Depois que uma conta de armazenamento do Azure é escolhida para seu cluster HDInsight, a única maneira de alterar a conta de armazenamento é excluir o cluster e criar um novo cluster com a conta de armazenamento desejada.

4. Clique no cluster recém-criado.  Ele mostra a página de aterrissagem:

	![HDI.ClusterLanding][image-cluster-landing]


##<a id="customize"></a> Personalizar os clusters HDInsight

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e suportados, consulte [Qual versão do Hadoop está no HDInsight do Azure][hdinsight-version] A personalização do HDInsight pode ser feita usando uma das seguintes opções:

- Use os parâmetros de personalização do cluster no SDK do HDInsight .NET ou no PowerShell do Azure durante o provisionamento do cluster. Fazendo isso, essas alterações de configuração são preservadas durante o ciclo de vida do cluster e não são afetadas pelas novas imagens do nó do cluster que a plataforma do Azure refaz periodicamente para manutenção. Para obter mais informações sobre como usar os parâmetros de personalização do cluster, consulte [Provisionamento de clusters HDInsight][hdinsight-provision].
- Alguns componentes nativos do Java, como o Mahout e o Cascading, podem ser executados no cluster como arquivos JAR. Esses arquivos JAR podem ser distribuídos para o armazenamento de Blob do Azure (WASB) e enviados aos clusters HDInsight usando mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs]. 


	>[WACOM.NOTE] Se você tiver problemas ao implantar arquivos jar nos clusters HDInsight ou ao chamar arquivos jar nos clusters HDInsight, entre em contato com o [Suporte da Microsoft][hdinsight-support].
	
	> O Mahout e o Cascading não têm suporte do HDInsight e não são qualificados para Suporte da Microsoft. Para obter as listas dos componentes suportados, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][hdinsight-version]


Não há suporte para a instalação de software personalizado no cluster usando uma conexão de área de trabalho remota. Evite armazenar arquivos nas unidades de nó de cabeçalho pois eles serão perdidos se você precisar recriar os clusters. Recomendamos armazenar os arquivos no armazenamento de Blob do Azure. O armazenamento de Blob é persistente.

##<a id="password"></a> Alterar o nome do usuário e a senha do cluster HDInsight
Um cluster HDInsight pode ter duas contas de usuário.  A conta de usuário do cluster HDInsight é criada durante o processo de provisionamento.  Você também pode criar uma conta de usuário RDP para acessar o cluster via RDP. Consulte [Habilitar área de trabalho remota](#enablerdp).

**Para alterar o nome do usuário e a senha do cluster HDInsight**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight do qual você deseja redefinir o nome do usuário e a senha.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Clique em **DESATIVADO** ao lado de **SERVIÇOS DO HADOOP**.
6. Clique em **SALVAR** na parte inferior da página e aguarde a conclusão da desativação.
7. Depois que o serviço foi desativado, clique em **ATIVADO** ao lado de **SERVIÇOS DO HADOOP**.
8. Digite o **NOME DE USUÁRIO** e a **NOVA SENHA**.  Esses serão o novo nome do usuário e a senha do cluster.
8. Clique em **SALVAR**.



##<a id="rdp"></a> Conectar ao clusters HDInsight usando o RDP

As credenciais do cluster que você forneceu em sua criação concedem acesso aos serviços do cluster, mas não ao próprio cluster através da área de trabalho remota. O acesso à Área de Trabalho Remota é desativado por padrão e, portanto, o acesso direto ao cluster usando-a requer uma configuração adicional após a criação.

**Para habilitar a área de trabalho remota**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight que você deseja conectar.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Na parte inferior da página, clique em **HABILITAR REMOTO**.
6. No assistente **Configurar Área de Trabalho Remota**, digite um nome do usuário e uma senha para a área de trabalho remota. Observe que o nome do usuário deve ser diferente do usado para criar o cluster (*admin* por padrão com a opção de Criação Rápida). Insira uma data de expiração na caixa de diálogo **EXPIRA EM**. Observe que a data de expiração deve estar no futuro e não mais do que há uma semana de hoje. A hora de expiração do dia será considerada por padrão como meia-noite na data especificada. Em seguida, clique no ícone de verificação.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]

	A data de validade deve estar no futuro e, no máximo, sete dias a partir de agora. E o horário é a meia-noite da data selecionada.

> [WACOM.NOTE] Depois que o RDP foi habilitado para um cluster, você deve atualizar a página antes de conectar-se ao cluster.
 
**Para conectar-se ao cluster usando o RDP**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight que você deseja conectar.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Clique em **CONECTAR** e, em seguida, siga as instruções.

##<a id="httpservice"></a> Conceder/revogar acesso aos serviços HTTP

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso no Portal de Gerenciamento. 

>[WACOM.NOTE] Ao conceder/revogar o acesso, você redefinirá o nome do usuário e a senha do cluster.

**Para conceder/revogar acesso aos serviços Web HTTP**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight que você deseja configurar.
4. Na parte superior da página, clique em **CONFIGURAÇÃO**.
5. Clique em **ATIVADO** ou **DESATIVADO** ao lado de **SERVIÇOS DO HADOOP**.  
6. Digite o **NOME DE USUÁRIO** e a **NOVA SENHA**.  Esses serão o novo nome do usuário e a senha do cluster.
7. Clique em **SALVAR**.

Isso também pode ser feito usando os cmdlets do PowerShell do Azure:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consulte [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].

##<a id="hadoopcmd"></a> Abra a linha de comando do Hadoop.

Para conectar-se ao cluster usando a área de trabalho remota e usar a linha de comando do Hadoop, você deve primeiro habilitar o acesso à área de trabalho remota para o cluster conforme descrito na seção anterior. 

**Para abrir a linha de comando do Hadoop.**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters Hadoop implantados.
3. Clique no cluster HDInsight que você deseja conectar.
3. Clique em **CONFIGURAÇÃO** na parte superior da página.
4. Clique em **Conectar** na parte inferior da página.
5. Clique em **Abrir**.
6. Digite suas credenciais e, em seguida, clique em **OK**.  Use o nome do usuário e a senha que você configurou quando criou o cluster.
7. Clique em **Sim**.
8. Na área de trabalho, clique duas vezes em **Linha de Comando do Hadoop**.
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Para obter mais informações sobre o comando Hadoop, consulte [Referência de comandos Hadoop][hadoop-command-reference].

Na captura de tela anterior, o nome da pasta tem o número de versão do Hadoop incorporado. O número da versão pode ser alterado com base na versão dos componentes do Hadoop instalados no cluster. Você pode usar variáveis de ambiente do Hadoop para referir-se a essas pastas.  Por exemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%

##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight usando o Portal de Gerenciamento do Azure e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
* [Administrar o HDInsight usando a Interface de linha de comando entre plataformas][hdinsight-admin-cross-platform]
* [Provisione clusters HDInsight][hdinsight-provision]
* [Enviar trabalhos Hadoop de forma programática][hdinsight-submit-jobs]
* [Introdução ao Azure HDInsight][hdinsight-getting-started]
* [Qual versão do Hadoop está no Azure HDInsight?][hdinsight-version]

[hdinsight-admin-cross-platform]: /pt-br/manage/services/hdinsight/administer-hdinsight-using-command-line-interface/
[hdinsight-admin-powershell]: /pt-br/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-getting-started]: /pt-br/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-provision]: /pt-br/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-submit-jobs]: /pt-br/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-storage]: /pt-br/manage/services/hdinsight/howto-blob-store/
[hdinsight-version]: /pt-br/manage/services/hdinsight/versioning-in-hdinsight/
[hdinsight-support]: http://www.windowsazure.com/pt-br/support/options/

[azure-create-storageaccount]: /pt-br/manage/services/storage/how-to-create-a-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/
[azure-purchase-options]: https://www.windowsazure.com/pt-br/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/pt-br/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/pt-br/pricing/free-trial/


[hadoop-command-reference]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"

