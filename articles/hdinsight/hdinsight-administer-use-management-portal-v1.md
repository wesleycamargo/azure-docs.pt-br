<properties
	pageTitle="Gerenciar clusters Hadoop no HDInsight usando o Portal Clássico do Azure | Microsoft Azure"
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
	ms.date="02/04/2016"
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight usando o Portal Clássico do Azure

Com o [Portal Clássico do Azure](https://manage.windowsazure.com), você pode provisionar os clusters Hadoop no Azure HDInsight, alterar a senha de usuário do Hadoop e habilitar o RDP (Protocolo de Desktop Remoto) para que possa acessar o console de comando do Hadoop no cluster.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md)

## Outras ferramentas para administrar o HDInsight
Também há outras ferramentas disponíveis para administrar o HDInsight além do Portal Clássico do Azure.


- Para obter mais informações sobre como administrar o HDInsight usando o PowerShell do Azure, consulte [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md).

- Para obter mais informações sobre como administrar o HDInsight usando a CLI do Azure, consulte [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md).

> [AZURE.WARNING] As versões mais recentes do Azure PowerShell ou a CLI do Azure padrão usando o Azure Resource Manager. Clusters do HDInsight criados usando esse modo não podem ser gerenciados usando o Portal Clássico do Azure.

##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Conta de armazenamento do Azure** - um cluster do HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para obter mais informações sobre como o Armazenamento de Blob do Azure fornece uma experiência perfeita com os clusters HDInsight, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento](../storage/storage-create-storage-account.md).


##Provisionar clusters do HDInsight

Você pode provisionar clusters HDInsight no Portal Clássico do Azure usando a opção Criação Rápida ou Criação Personalizada. Consulte os links a seguir para obter instruções:

- [Provisionar um cluster usando a criação rápida](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Provisionar um cluster usando criação personalizada](hdinsight-provision-clusters.md#portal)

[AZURE.INCLUDE [lista do centro de dados](../../includes/hdinsight-pricing-data-centers-clusters.md)]


##Personalizar os clusters HDInsight

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e suportados, consulte [Qual versão do Hadoop está no HDInsight do Azure](hdinsight-component-versioning.md) Você pode personalizar o HDInsight usando uma das seguintes opções:

- Use a ação de Script para executar scripts personalizados que possam personalizar um cluster para alterar a configuração de cluster ou instalar componentes personalizados como Giraph ou Solr. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).
- Use os parâmetros de personalização do cluster no SDK do .NET do HDInsight ou no PowerShell do Azure durante o provisionamento do cluster. Essas alterações de configuração são preservadas durante o tempo de vida do cluster e não são afetadas pelas novas imagens do nó do cluster que a plataforma do Azure refaz periodicamente para manutenção. Para obter mais informações sobre como usar os parâmetros de personalização do cluster, consulte [Provisionamento de clusters HDInsight](hdinsight-provision-clusters.md).
- Alguns componentes nativos do Java, como Mahout e Cascading, podem ser executados no cluster como arquivos JAR. Esses arquivos JAR podem ser distribuídos para o armazenamento de Blob do Azure e enviados aos clusters HDInsight por meio de mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE] Se você tiver problemas para implantar arquivos JAR nos clusters do HDInsight ou ao chamar arquivos JAR nesses clusters, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

	> O Cascading não tem suporte do HDInsight e não é qualificado para o Suporte da Microsoft. Para obter as listas dos componentes suportados, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?](hdinsight-component-versioning.md)


Não há suporte para a instalação de software personalizado no cluster usando uma conexão de área de trabalho remota. Evite armazenar arquivos nas unidades de nó de cabeçalho, pois eles serão perdidos se você precisar recriar os clusters. É recomendável armazenar os arquivos no armazenamento de Blob do Azure. O armazenamento de Blob é persistente.

##Alterar o nome de usuário e a senha do cluster HDInsight
Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight é criada durante o processo de provisionamento. Você também pode criar uma conta de usuário RDP para acessar o cluster via RDP. Consulte [Habilitar área de trabalho remota](#connect-to-hdinsight-clusters-by-using-rdp).

**Para alterar o nome de usuário e a senha do cluster HDInsight**

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight do qual você deseja redefinir o nome de usuário e a senha.
4. No topo da página, clique em **CONFIGURAÇÃO**.
5. Clique em **DESATIVADO** próximo a **SERVIÇOS DO HADOOP**.
6. Clique em **SALVAR** na parte inferior da página e aguarde a conclusão da desabilitação.
7. Após o serviço ser desativado, clique em **ATIVADO** ao lado de **SERVIÇOS DO HADOOP**.
8. Para **NOME DE USUÁRIO** e **NOVA SENHA**, digite o novo nome de usuário e a senha (respectivamente) para o cluster.
8. Clique em **SALVAR**.


##Conectar ao clusters HDInsight usando o RDP

As credenciais do cluster que você forneceu em sua criação concedem acesso aos serviços do cluster, mas não ao próprio cluster através da área de trabalho remota. O acesso à área de trabalho remota é desativado por padrão e, portanto, o acesso direto ao cluster usando-a requer uma configuração adicional após a criação.

**Para habilitar a área de trabalho remota**

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight que você deseja conectar.
4. No topo da página, clique em **CONFIGURAÇÃO**.
5. No final da página, clique em **HABILITAR REMOTO**.
6. No assistente **Configurar Área de Trabalho Remota**, digite um nome de usuário e uma senha para a área de trabalho remota. Observe que o nome de usuário deve ser diferente daquele usado para criar o cluster (por padrão, **admin** com a opção de Criação Rápida). Insira uma data de expiração na caixa de diálogo **EXPIRA EM**. Observe que a data de vencimento deve estar no futuro e ser até 90 dias a partir de hoje. O horário de validade do dia será considerada, por padrão, como meia-noite da data especificada. Em seguida, clique no ícone de verificação.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]


> [AZURE.NOTE] Você também pode usar o SDK do .NET HDInsight para habilitar a área de trabalho remota em um cluster. Use o **EnableRdp** método no objeto de cliente HDInsight da seguinte maneira: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Da mesma forma, para desabilitar a área de trabalho remota no cluster, você pode usar **client.DisableRdp(clustername, location)**. Para obter mais informações sobre esses métodos, consulte [Referência do SDK do .NET HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Isso é aplicável apenas a clusters do HDInsight em execução no Windows.



> [AZURE.NOTE] Depois que o RDP for habilitado para um cluster, você deverá atualizar a página antes de conectar-se ao cluster.

**Para conectar-se ao cluster usando o RDP**

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight que você deseja conectar.
4. No topo da página, clique em **CONFIGURAÇÃO**.
5. Clique em **CONECTAR** e, em seguida, siga as instruções.

##Crie um certificado autoassinado

Se você quiser realizar qualquer operação no cluster usando o SDK do .NET, deve criar um certificado autoassinado na estação de trabalho e também carregar o certificado para sua assinatura do Azure. Essa é uma tarefa que só precisa ser executada uma vez. Você pode instalar o mesmo certificado em outras máquinas, contanto que ele seja válido.

**Para criar um certificado autoassinado**

1. Crie um certificado autoassinado usado para autenticar as solicitações. Você pode usar o ISS (Serviços de Informações da Internet) ou [makecert](http://go.microsoft.com/fwlink/?LinkId=534000) para criar o certificado.

2. Navegue até o local do certificado, clique com o botão direito do mouse no certificado, clique em **Instalar Certificado** e instale o certificado no repositório pessoal do computador. Edite as propriedades do certificado para atribuir a ele um nome amigável.

3. Importe o certificado para o Portal Clássico do Azure. No portal, clique em **CONFIGURAÇÕES** na lado inferior esquerdo da página e clique em **CERTIFICADOS DE GERENCIAMENTO**. Na parte inferior da página, clique em **CARREGAR** e sigas as instruções para carregar o arquivo .cer criado na etapa anterior.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##Conceder/revogar acesso aos serviços HTTP

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar ou conceder o acesso no Portal Clássico do Azure.

>[AZURE.NOTE] Ao conceder/revogar o acesso, você redefinirá o nome de usuário de cluster e a senha.

**Para conceder/revogar acesso aos serviços Web HTTP**

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3. Clique no cluster HDInsight que você deseja configurar.
4. No topo da página, clique em **CONFIGURAÇÃO**.
5. Clique em **ATIVADO** ou **DESATIVADO** ao lado de **SERVIÇOS DO HADOOP**.
6. Para **NOME DE USUÁRIO** e **NOVA SENHA**, digite o novo nome de usuário e a senha (respectivamente) para o cluster.
7. Clique em **SALVAR**.

Consulte [Administrar clusters HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md).

##Abrir a linha de comando do Hadoop

Para conectar-se ao cluster usando a área de trabalho remota e usar a linha de comando do Hadoop, você deve primeiro habilitar o acesso à área de trabalho remota para o cluster conforme descrito na seção anterior.

**Para abrir a linha de comando do Hadoop**

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters Hadoop implantados.
3. Clique no cluster HDInsight que você deseja conectar.
3. Clique em **CONFIGURAÇÃO** na parte superior da página.
4. Clique em **CONECTAR** na parte inferior da página.
5. Clique em **Abrir**.
6. Digite suas credenciais e **OK**. Use o nome de usuário e a senha que você configurou quando criou o cluster.
7. Clique em **Sim**.
8. Na área de trabalho, clique duas vezes em **Linha de Comando do Hadoop**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Para obter mais informações sobre os comandos Hadoop, consulte [Referência de comandos Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Na captura de tela anterior, o nome da pasta tem o número de versão do Hadoop incorporado. O número da versão pode ser alterado com base na versão dos componentes do Hadoop instalados no cluster. Você pode usar variáveis de ambiente do Hadoop para referir-se a essas pastas. Por exemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%

##Dimensionar clusters
Consulte [Dimensionar clusters Hadoop no HDInsight](hdinsight-administer-use-management-portal.md#scale-clusters).

##Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight usando o Portal Clássico do Azure e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Provisionar clusters HDInsight](hdinsight-provision-clusters.md)
* [Enviar trabalhos Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal-v1/hdi.createrdpuser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal-v1/hdinsight-hadoop-command-line.png "Linha de comando do Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal-v1/hdi.clustercreate.uploadcert.png

<!---HONumber=AcomDC_0427_2016-->