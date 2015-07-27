<properties 
	pageTitle="Mover dados de e para o Armazenamento de Blobs do Azure | Microsoft Azure" 
	description="Mover dados de e para o Armazenamento de Blobs do Azure" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;sachouks;mohabib;bradsev" />

# Mover dados de e para o Armazenamento de Blobs do Azure

O artigo [Cenários para ADAPT (Processo e Tecnologia de Análise Avançada) no Aprendizado de Máquina do Azure](../machine-learning-data-science-plan-sample-scenarios.md) o ajudará a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados usados no processo de análise avançada. Se você precisar mover os dados de e para o armazenamento de Blobs do Azure com base no seu cenário, use um dos seguintes métodos:

- [Usando o Gerenciador de Armazenamento do Azure](#explorer)
- [Usando o utilitário de linha de comando AzCopy](#AzCopy)
- [Usando o SDK do Azure em Python](#PythonSDK)


> [AZURE.TIP]Como alternativa, você pode usar o [Azure Data Factory](https://azure.microsoft.com/pt-br/services/data-factory/) para criar e agendar um pipeline que baixará os dados do armazenamento de Blob do Azure, os passará para um serviço Web publicado do Aprendizado de Máquina do Azure, receberá os resultados de análise preditiva e carregará os resultados no armazenamento. Para obter mais informações, consulte [Criar pipelines preditivos usando o Azure Data Factory e o Aprendizado de Máquina do Azure](../data-factory/data-factory-create-predictive-pipelines.md).

<para></para>

> [AZURE.NOTE]Para obter uma introdução completa ao armazenamento de Blobs do Azure, consulte [Noções básicas do Serviço Blob do Azure](../storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure. Para obter instruções sobre como obter essas informações, consulte a seção "Como: exibir, copiar e regenerar chaves de acesso de armazenamento" de [Gerenciar contas de armazenamento](../storage-create-storage-account.md). Este documento presume que você tem uma conta de Armazenamento do Azure e tem as chaves de armazenamento correspondentes.


<a id="explorer"></a>
## Usar o Gerenciador de Armazenamento do Azure 

O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita do Windows para inspecionar e alterar dados em uma conta de Armazenamento do Azure. Ele pode ser baixado do [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/). As etapas a seguir mostram como carregar/baixar os dados usando o Gerenciador de Armazenamento do Azure.

1.  Abra o Gerenciador de Armazenamento do Azure 
2.  Se a conta de armazenamento que você deseja acessar não tiver sido adicionada ao Gerenciador de Armazenamento do Azure, clique no botão "Adicionar Conta" para adicionar a conta. Se já tiver sido adicionada, selecione a conta na lista suspensa “--Selecione uma conta de armazenamento.” ![Criar espaço de trabalho][1] <br>
3. Digite o nome e a chave de conta de armazenamento e clique em Adicionar Conta de Armazenamento. Você pode adicionar várias contas de armazenamento e cada conta será exibidas em uma guia. Os contêineres nesta conta de armazenamento são mostrados no painel à esquerda. Selecione um contêiner para ver os blobs no contêiner no painel à direita. ![Criar espaço de trabalho][2] <br> ![Criar espaço de trabalho][3] <br>
4. Carregue os dados clicando no botão "Carregar". Selecione um ou vários arquivos para carregar do sistema de arquivos e clique em "Abrir" para começar a carregar os arquivos.
5. Baixe os dados selecionando o blob no contêiner correspondente e clicando no botão “Baixar".

<a id="AzCopy"></a>
## Usar AzCopy

AzCopy é um utilitário de linha de comando para carregar e baixar os dados.

**Aviso** Se você estiver usando um computador diferente da VM que foi configurada anteriormente no processo de análise avançada, instale o AzCopy usando as seguintes instruções de instalação: [Baixar e instalar o AzCopy](../storage-use-azcopy.md#install).

###Exemplos de carregar/baixar arquivos de/para blobs:

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]1. Ao carregar arquivos, /S carregará arquivos recursivamente. Sem esse parâmetro, todos os arquivos no subdiretório não serão carregados. 2. Ao baixar o arquivo, /S vai pesquisar recursivamente o contêiner até que todos os arquivos no diretório especificado e de seus subdiretórios, ou todos os arquivos que correspondam ao padrão especificado no diretório especificado e seus subdiretórios, sejam baixados. 3. Não é possível especificar um arquivo de blob específico para baixar usando o parâmetro /Source. Para baixar um arquivo específico, especifique o nome do arquivo de blob a ser baixado usando o parâmetro /Pattern. O parâmetro /S pode ser usado para que AzCopy procure recursivamente um padrão de nome de arquivo. Sem o parâmetro padrão, o AzCopy baixará todos os arquivos nesse diretório.

Para ver o uso detalhado de AzCopy, consulte [Noções básicas sobre o utilitário de linha de comando AzCopy](../storage-use-azcopy.md#install).


<a id="PythonSDK"></a>
## Usar Python

Com a API de Python fornecida no SDK do Azure, você pode:

- Criar um contêiner
- Carregar um blob em um contêiner
- Baixar blobs
- Listar os blobs em um contêiner
- Excluir um blob

Esta seção documenta como listar, carregar e baixar blobs. Para obter mais detalhes do uso da API de Python, consulte [Como usar o Serviço de Armazenamento de Blobs do Python](../storage-python-how-to-use-blob-storage.md).

> [AZURE.NOTE]Se você estiver usando um computador diferente da VM que foi configurada anteriormente no processo de análise avançada, instale o [SDK do Azure de Python](../python-how-to-install.md) usando o código de exemplo abaixo.

### Carregar dados para Blob

Adicione o trecho de código a seguir à parte superior de qualquer código Python no qual você deseja acessar programaticamente o Armazenamento do Azure:

	from azure.storage import BlobService

O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto BlobService usando o nome da conta de armazenamento e a chave da conta. Substitua o nome e a chave de conta pela sua conta e chave reais.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Use os seguintes métodos para carregar dados para um blob:
 
1. put_block_blob_from_path (carrega o conteúdo de um arquivo do caminho especificado)
2. put_block_blob_from_file (carrega o conteúdo de um arquivo/fluxo já aberto)
3. put_block_blob_from_bytes (carrega uma matriz de bytes)
4. put_block_blob_from_text (carrega o valor de texto especificado usando a codificação especificada)
 
O código de exemplo a seguir carrega um arquivo local para um contêiner:
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo a seguir carrega todos os arquivos (exceto diretórios) em um diretório local para o armazenamento de blob:

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

### Baixar Dados de Blob

Use os seguintes métodos para baixar os dados de um blob: 1. get_blob_to_path 2. get_blob_to_file 3. get_blob_to_bytes 4. get_blob_to_text

Esses métodos realizam a divisão necessária quando o tamanho dos dados excede 64 MB.

O código de exemplo a seguir baixa o conteúdo de um blob em um contêiner para um arquivo local:

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo a seguir baixa todos os blobs de um contêiner. Ele usa list_blobs para obter a lista de blobs disponíveis no contêiner e baixá-los para um diretório local.

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png
 

<!---HONumber=July15_HO3-->