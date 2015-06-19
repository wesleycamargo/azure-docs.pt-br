<properties 
	pageTitle="Personalizar os Clusters de Hadoop do Azure HDInsight para ciência de dados | Azure" 
	description="Personalizar os Clusters de Hadoop do Azure HDInsight para ciência de dados" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="hangzh;bradsev" />

# Personalizar os Clusters de Hadoop do Azure HDInsight para ciência de dados

Este artigo descreve como personalizar um cluster do Hadoop do HDInsight instalando Anaconda de 64 bits (Python 2.7) em cada nó quando o cluster está sendo provisionado no serviço HDInsight.  Ele também mostra como acessar o nó principal para enviar trabalhos personalizados para o cluster.

Essa personalização disponibiliza convenientemente muitos módulos Python populares incluídos no Anaconda para uso em UDFs (Funções Definidas pelo Usuário) projetadas para processar os registros Hive do cluster.  Para obter instruções sobre os procedimentos usados neste cenário, consulte [Enviar consultas Hive de clusters do Hadoop do HDInsight no processo de Ciência de Dados de Nuvem](machine-learning-data-science-hive-queries.md).

## <a name="customize"></a>Personalizar o cluster do Hadoop do Azure HDInsight

Para criar um cluster do Hadoop do HDInsight personalizado, os usuários precisam fazer logon no [**Portal de Gerenciamento do Azure**](https://manage.windowsazure.com/), clicar em **Novo** no canto inferior esquerdo e selecionar SERVIÇOS DE DADOS -> HDINSIGHT -> **CRIAÇÃO PERSONALIZADA** para abrir a janela **Detalhes do Cluster**. 

![Create workspace][1]

Insira o nome do cluster a ser criado na página de configuração 1 e aceite os valores padrão para os outros campos.  Clique na seta para ir para a próxima página de configuração. 

![Create workspace][2]

Na página de configuração 2, insira o número dos **NÓS DE DADOS**, selecione a **REGIÃO/REDE VIRTUAL**e selecione os tamanhos do **NÓ PRINCIPAL** e **NÓ DE DADOS**.  Clique na seta para ir para a próxima página de configuração.

>[AZURE.NOTE] A **REGIÃO/REDE VIRTUAL** precisa ser a mesma que a região da conta de armazenamento que será usada para o cluster do Hadoop do HDInsight.  Caso contrário, na quarta página de configuração, a conta de armazenamento que os usuários desejam usar não aparecerá na lista suspensa de **NOME DA CONTA**.

![Create workspace][3]

Na página de configuração 3, forneça um nome de usuário e senha para o cluster do Hadoop do HDInsight.  **Não** selecione o _Inserir o Hive/Oozie Metastore_.  Clique na seta para ir para a próxima página de configuração. 

![Create workspace][4]

Na página de configuração 4, especifique o nome da conta de armazenamento, o contêiner padrão do cluster do Hadoop do HDInsight.  Se os usuários selecionarem _Criar contêiner padrão_ na lista suspensa **CONTÊINER PADRÃO**, um contêiner com o mesmo nome que o cluster será criado.  Clique na seta para ir para a última página de configuração.

![Create workspace][5]

Na página de configuração **Ações de Script** final, clique no botão **adicionar ação de script** preencha os campos de texto com os seguintes valores.
 
* **NOME** - qualquer cadeia de caracteres como o nome dessa ação de script. 
* **TIPO DE NÓ** - selecione **Todos os nós**. 
* **URI DO SCRIPT** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
	* *publicscripts* é um contêiner público na conta de armazenamento 
	* *getgoing* é usado para compartilhar arquivos de script do PowerShell a fim de facilitar o trabalho dos usuários no Azure. 
* **PARÂMETROS** -(deixe em branco)

Por fim, clique na marca de seleção para iniciar a criação do cluster do Hadoop do HDInsight personalizado. 

![Create workspace][6]

## <a name="headnode"></a> Acessar o nó principal do Cluster do Hadoop

Os usuários devem habilitar o acesso remoto ao cluster do Hadoop no Azure para poderem acessar o nó principal do cluster do Hadoop via RDP. 

1. Faça logon no [**Portal de Gerenciamento do Azure**](https://manage.windowsazure.com/), selecione **HDInsight** à esquerda, selecione o cluster de Hadoop na lista de clusters, clique na guia **CONFIGURAÇÃO** e clique no ícone **HABILITAR CONEXÃO REMOTA** na parte inferior da página.
	
	![Create workspace][7]

2. Na janela **Configurar Área de Trabalho Remota**, insira os campos NOME DE USUÁRIO e SENHA e selecione a data de validade para o acesso remoto.  Em seguida, clique na marca de seleção para habilitar o acesso remoto ao nó principal do cluster do Hadoop.
	
	>[AZURE.NOTE] 
	>
	>1. O nome de usuário e senha para o acesso remoto não são os mesmos usados ao criar o cluster do Hadoop.  Eles são um conjunto separado de credenciais
	>
	>2. A data de validade do acesso remoto deve estar dentro de 7 dias a partir da data atual.

	![Create workspace][8]

3. Após habilitar o acesso remoto, clique em **CONECTAR** na parte inferior da página para iniciar a conexão remota com o nó principal.  Faça logon no nó principal do cluster do Hadoop inserindo as credenciais do usuário de acesso remoto que você especificou anteriormente.

	 ![Create workspace][9]

Em seguida, consulte [Enviar consultas de Hive para clusters do Hadoop do HDInsight no processo de ciência de dados de nuvem](machine-learning-data-science-hive-queries.md) para obter instruções sobre como acessar os módulos Python incluídos no Anaconda por meio do nó principal em UDFs (Funções Definidas pelo Usuário) usadas para processar registros Hive armazenados no cluster.

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png


<!--HONumber=49--> 