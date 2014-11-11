<properties linkid="manage-services-hdinsight-howto-administer-hdinsight" urlDisplayName="Administration" pageTitle="Manage Hadoop clusters in HDInsight using Azure Portal | Azure" metaKeywords="" description="Learn how to administer HDInsight Service. Create an HDInsight cluster, open the interactive JavaScript console, and open the Hadoop command console." metaCanonical="" services="hdinsight" documentationCenter="" title="Manage Hadoop clusters in HDInsight using the Azure Management Portal" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Gerenciar clusters Hadoop no HDInsight usando o Portal de Gerenciamento do Azure

Usando o Portal de Gerenciamento do Azure, você pode provisionar os clusters Hadoop no HDInsight, alterar a senha de usuário do Hadoop e habilitar o RDP para que possa acessar o console de comando do Hadoop no cluster. Também há outras ferramentas disponíveis para administração do HDInsight além do Portal de Gerenciamento.

-   Para obter mais informações sobre como administrar o HDInsight usando o PowerShell do Azure, consulte [Administrar o HDInsight usando o PowerShell][Administrar o HDInsight usando o PowerShell].

-   Para obter mais informações sobre como administrar o HDInsight usando as ferramentas de linha de comando entre plataformas, consulte [Administrar o HDInsight usando a interface de linha de comando entre plataformas][Administrar o HDInsight usando a interface de linha de comando entre plataformas].

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

-   **Assinatura do Azure**. O Azure é uma plataforma baseada em assinatura. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra][Opções de compra], [Ofertas para membros][Ofertas para membros] ou [Avaliação gratuita][Avaliação gratuita].

## Neste artigo

-   [Provisione clusters HDInsight][Provisione clusters HDInsight]
-   [Personalizar os clusters HDInsight][Personalizar os clusters HDInsight]
-   [Alterar o nome do usuário e a senha do cluster HDInsight][Alterar o nome do usuário e a senha do cluster HDInsight]
-   [Conectar ao clusters HDInsight usando o RDP][Conectar ao clusters HDInsight usando o RDP]
-   [Conceder/revogar acesso aos serviços HTTP][Conceder/revogar acesso aos serviços HTTP]
-   [Abrir o console de comando do Hadoop][Abrir o console de comando do Hadoop]
-   [Próximas etapas][Próximas etapas]

## <span id="create"></span></a> Provisionar os clusters HDInsight

Existem vários métodos para criar os clusters HDInsight, este artigo cobre apenas o uso da opção Criação Rápida no Portal de Gerenciamento do Azure. Para obter outras opções, consulte [Provisionar os clusters HDInsight][Provisionar os clusters HDInsight].

O cluster HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para obter mais informações sobre como o Armazenamento de Blob do Azure fornece uma experiência perfeita com os clusters HDInsight, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Uma conta de armazenamento do Azure deve ser criada no mesmo data center no qual seu cluster HDInsight irá ser provisionado. Atualmente os clusters HDInsight podem ser provisionados em cinco data centers:

-   Sudeste Asiático
-   Norte da Europa
-   Europa Ocidental
-   Leste dos EUA
-   Oeste dos EUA

Para obter detalhes sobre como criar uma conta de armazenamento do Azure, consulte [Como criar uma conta de armazenamento][Como criar uma conta de armazenamento].

**Para provisionar o cluster HDInsight**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  No final da página, clique em **NOVO**, clique em **SERVIÇOS DE DADOS**, clique em **HDINSIGHT** e, por fim, clique em **CRIAÇÃO RÁPIDA**.

3.  Forneça o **Nome do Cluster**, o **Tamanho do Cluster**, A **Senha do Administrador do Cluster** e uma **Conta de Armazenamento** do Azure e, em seguida, clique em **Criar Cluster HDInsight**. Depois que o cluster estiver criado e em execução, o status será *Executando*.

    ![HDI.QuickCreate][HDI.QuickCreate]

    Ao usar a opção Criação Rápida para criar um cluster, o nome do usuário padrão da conta de usuário do Hadoop é *admin*. Para conceder à conta outro nome do usuário, você pode usar a opção Criação Personalizada em vez da opção Criação Rápida. Ou altere o nome da conta depois que ela for provisionada.

    Ao usar a opção Criação Rápida para criar um cluster, um novo contêiner com o nome do cluster HDInsight é criado automaticamente na conta de armazenamento especificada. Se desejar personalizar o nome do contêiner a ser usado por padrão pelo cluster, você deverá usar a opção Criação Personalizada.

    > [WACOM.NOTE] Depois que uma conta de armazenamento do Azure é escolhida para seu cluster HDInsight, a única maneira de alterar a conta de armazenamento é excluir o cluster e criar um novo cluster com a conta de armazenamento desejada.

4.  Clique no cluster recém-criado. Ele mostra a página de aterrissagem:

    ![HDI.ClusterLanding][HDI.ClusterLanding]

## <span id="customize"></span></a> Personalizar os clusters HDInsight

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e suportados, consulte [Qual versão do Hadoop está no HDInsight do Azure][Qual versão do Hadoop está no HDInsight do Azure] A personalização do HDInsight pode ser feita usando uma das seguintes opções:

-   Use os parâmetros de personalização do cluster no SDK do .NET do HDInsight ou no PowerShell do Azure durante o provisionamento do cluster. Fazendo isso, essas alterações de configuração são preservadas durante o ciclo de vida do cluster e não são afetadas pelas novas imagens do nó do cluster que a plataforma do Azure refaz periodicamente para manutenção. Para obter mais informações sobre como usar os parâmetros de personalização do cluster, consulte [Provisionamento de clusters HDInsight][Provisionar os clusters HDInsight].
-   Alguns componentes nativos do Java, como o Mahout e o Cascading, podem ser executados no cluster como arquivos JAR. Esses arquivos JAR podem ser distribuídos para o armazenamento de Blob do Azure (WASB) e enviados aos clusters HDInsight usando mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática][Enviar trabalhos do Hadoop de forma programática].

    > [WACOM.NOTE] Se você tiver problemas ao implantar arquivos jar nos clusters HDInsight ou ao chamar arquivos jar nos clusters HDInsight, entre em contato com o [Suporte da Microsoft][Suporte da Microsoft].

    > O Cascading não tem suporte do HDInsight e não é qualificado para o Suporte da Microsoft. Para obter as listas dos componentes suportados, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][Qual versão do Hadoop está no HDInsight do Azure]

Não há suporte para a instalação de software personalizado no cluster usando uma conexão de área de trabalho remota. Evite armazenar arquivos nas unidades de nó de cabeçalho pois eles serão perdidos se você precisar recriar os clusters. Recomendamos armazenar os arquivos no armazenamento de Blob do Azure. O armazenamento de Blob é persistente.

## <span id="password"></span></a> Alterar o nome do usuário e a senha do cluster HDInsight

Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight é criada durante o processo de provisionamento. Você também pode criar uma conta de usuário RDP para acessar o cluster via RDP. Consulte [Habilitar área de trabalho remota][Habilitar área de trabalho remota].

**Para alterar o nome do usuário e a senha do cluster HDInsight**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3.  Clique no cluster HDInsight do qual você deseja redefinir o nome do usuário e a senha.
4.  No topo da página, clique em **CONFIGURAÇÃO**.
5.  Clique em **DESATIVADO** próximo a **SERVIÇOS DO HADOOP**.
6.  Clique em **SALVAR** na parte inferior da página e aguarde a conclusão da desativação.
7.  Após o serviço ser desativado, clique em **ATIVADO** ao lado de **SERVIÇOS DO HADOOP**.
8.  Digite o **NOME DE USUÁRIO** e a **NOVA SENHA**. Esses serão o novo nome do usuário e a senha do cluster.
9.  Clique em **SALVAR**.

## <span id="rdp"></span></a> Conectar ao clusters HDInsight usando o RDP

As credenciais do cluster que você forneceu em sua criação concedem acesso aos serviços do cluster, mas não ao próprio cluster através da área de trabalho remota. O acesso à Área de Trabalho Remota é desativado por padrão e, portanto, o acesso direto ao cluster usando-a requer uma configuração adicional após a criação.

**Para habilitar a área de trabalho remota**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3.  Clique no cluster HDInsight que você deseja conectar.
4.  No topo da página, clique em **CONFIGURAÇÃO**.
5.  No final da página, clique em **HABILITAR REMOTO**.
6.  No assistente **Configurar Área de Trabalho Remota**, digite um nome do usuário e uma senha para a área de trabalho remota. Observe que o nome do usuário deve ser diferente do usado para criar o cluster (*admin* por padrão com a opção de Criação Rápida). Insira uma data de expiração na caixa de diálogo **EXPIRA EM**. Observe que a data de expiração deve estar no futuro e não mais do que há uma semana de hoje. A hora de expiração do dia será considerada por padrão como meia-noite na data especificada. Em seguida, clique no ícone de verificação.

    ![HDI.CreateRDPUser][HDI.CreateRDPUser]

    A data de validade deve estar no futuro e, no máximo, sete dias a partir de agora. E o horário é a meia-noite da data selecionada.

> [WACOM.NOTE] Depois que o RDP for habilitado para um cluster, você deve atualizar a página antes de conectar-se ao cluster.

**Para conectar-se ao cluster usando o RDP**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3.  Clique no cluster HDInsight que você deseja conectar.
4.  No topo da página, clique em **CONFIGURAÇÃO**.
5.  Clique em **CONECTAR** e, em seguida, siga as instruções.

## <span id="httpservice"></span></a> Conceder/revogar acesso aos serviços HTTP

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso no Portal de Gerenciamento.

> [WACOM.NOTE] Ao conceder/revogar o acesso, você redefinirá o nome e a senha de usuário do cluster.

**Para conceder/revogar acesso aos serviços Web HTTP**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.
3.  Clique no cluster HDInsight que você deseja configurar.
4.  No topo da página, clique em **CONFIGURAÇÃO**.
5.  Clique em **ATIVADO** ou **DESATIVADO** próximo a **SERVIÇOS DO HADOOP**
    .
6.  Digite o **NOME DE USUÁRIO** e a **NOVA SENHA**. Esses serão o novo nome do usuário e a senha do cluster.
7.  Clique em **SALVAR**.

Isso também pode ser feito usando os cmdlets do PowerShell do Azure:

-   Grant-AzureHDInsightHttpServicesAccess
-   Revoke-AzureHDInsightHttpServicesAccess

Consulte [Administrar o HDInsight usando o PowerShell][Administrar o HDInsight usando o PowerShell].

## <span id="hadoopcmd"></span></a> Abra a linha de comando do Hadoop.

Para conectar-se ao cluster usando a área de trabalho remota e usar a linha de comando do Hadoop, você deve primeiro habilitar o acesso à área de trabalho remota para o cluster conforme descrito na seção anterior.

**Para abrir a linha de comando do Hadoop.**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters Hadoop implantados.
3.  Clique no cluster HDInsight que você deseja conectar.
4.  Clique em **CONFIGURAÇÃO** na parte superior da página.
5.  Clique em **Conectar** na parte inferior da página.
6.  Clique em **Abrir**.
7.  Digite suas credenciais e **OK**. Use o nome do usuário e a senha que você configurou quando criou o cluster.
8.  Clique em **Sim**.
9.  Na área de trabalho, clique duas vezes em **Linha de Comando do Hadoop**.

    ![HDI.HadoopCommandLine][HDI.HadoopCommandLine]

    Para obter mais informações sobre o comando Hadoop, consulte [Referência de comandos Hadoop][Referência de comandos Hadoop].

Na captura de tela anterior, o nome da pasta tem o número de versão do Hadoop incorporado. O número da versão pode ser alterado com base na versão dos componentes do Hadoop instalados no cluster. Você pode usar variáveis de ambiente do Hadoop para referir-se a essas pastas. Por exemplo:

    cd %hadoop_home%
    cd %hive_home%
    cd %pig_home%
    cd %sqoop_home%   
    cd %hcatalog_home%

## <span id="nextsteps"></span></a>Próximas etapas

Neste artigo, você aprendeu como criar um cluster HDInsight usando o Portal de Gerenciamento do Azure e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

-   [Administrar o HDInsight usando o PowerShell][Administrar o HDInsight usando o PowerShell]
-   [Administrar o HDInsight usando a Interface de linha de comando entre plataformas][Administrar o HDInsight usando a interface de linha de comando entre plataformas]
-   [Provisione clusters HDInsight][Provisionar os clusters HDInsight]
-   [Enviar trabalhos Hadoop de forma programática][Enviar trabalhos do Hadoop de forma programática]
-   [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight]
-   [Qual versão do Hadoop está no Azure HDInsight?][Qual versão do Hadoop está no HDInsight do Azure]

  [Administrar o HDInsight usando o PowerShell]: ../hdinsight-administer-use-powershell/
  [Administrar o HDInsight usando a interface de linha de comando entre plataformas]: ../hdinsight-administer-use-command-line/
  [Opções de compra]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/pt-br/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Provisione clusters HDInsight]: #create
  [Personalizar os clusters HDInsight]: #customize
  [Alterar o nome do usuário e a senha do cluster HDInsight]: #password
  [Conectar ao clusters HDInsight usando o RDP]: #rdp
  [Conceder/revogar acesso aos serviços HTTP]: #httpservice
  [Abrir o console de comando do Hadoop]: #hadoopcmd
  [Próximas etapas]: #nextsteps
  [Provisionar os clusters HDInsight]: ../hdinsight-provision-clusters/
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Como criar uma conta de armazenamento]: ../storage-create-storage-account/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [HDI.QuickCreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
  [HDI.ClusterLanding]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Página de aterrissagem do cluster"
  [Qual versão do Hadoop está no HDInsight do Azure]: ../hdinsight-component-versioning/
  [Enviar trabalhos do Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Suporte da Microsoft]: http://azure.microsoft.com/pt-br/support/options/
  [Habilitar área de trabalho remota]: #enablerdp
  [HDI.CreateRDPUser]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
  [HDI.HadoopCommandLine]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Linha de comando do Hadoop"
  [Referência de comandos Hadoop]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
