<properties 
	pageTitle="Mover dados de e para o Armazenamento de Blobs do Azure usando AzCopy | Microsoft Azure" 
	description="Mover dados de e para o Armazenamento de Blobs do Azure usando o AzCopy" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="bradsev" />

# Mover dados de e para o Armazenamento de Blobs do Azure usando o AzCopy

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Introdução

AzCopy é um utilitário de linha de comando projetado para upload, download e cópia de dados de alto desempenho do e o armazenamento de blob, arquivo e tabela do Microsoft Azure.

Para obter instruções sobre como instalar o AzCopy e informações adicionais sobre como usá-lo com a plataforma do Azure, consulte [Introdução ao Utilitário de Linha de Comando AzCopy](../storage-use-azcopy.md).

> [AZURE.NOTE]Se você está usando a VM que foi configurada com os scripts fornecidos pelas [Máquinas virtuais de Ciência de Dados no Azure](machine-learning-data-science-virtual-machines.md), AzCopy já está instalado na VM.

> [AZURE.NOTE]Para obter uma introdução completa ao armazenamento de Blobs do Azure, consulte [Noções básicas do Serviço Blob do Azure](../storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Pré-requisitos

Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure.

- Para configurar uma assinatura do Azure, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pt-BR/pricing/free-trial/).
- Para obter instruções sobre como criar uma conta de armazenamento e para obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../storage-create-storage-account.md).

## Carregar arquivos para um blob do Azure

Para carregar um arquivo, use o seguinte comando no AzCopy que é uma linha de comando.

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

## Baixe arquivos em um blob do Azure

Para baixar um arquivo em um blob do Azure, use o comando a seguir no AzCopy que é uma linha de comando.

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

## Transferir os blobs entre os contêineres do Azure

Para transferir os blobs entre os contêineres do Azure, use o comando a seguir no AzCopy que é uma linha de comando.

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

## Dicas para usar o AzCopy

> [AZURE.TIP]1. Ao carregar arquivos, /S carregará arquivos recursivamente. Sem esse parâmetro, todos os arquivos no subdiretório não serão carregados. 2. Ao baixar o arquivo, /S vai pesquisar recursivamente o contêiner até que todos os arquivos no diretório especificado e de seus subdiretórios, ou todos os arquivos que correspondam ao padrão especificado no diretório especificado e seus subdiretórios, sejam baixados. 3. Não é possível especificar um arquivo de blob específico para baixar usando o parâmetro /Source. Para baixar um arquivo específico, especifique o nome do arquivo de blob a ser baixado usando o parâmetro /Pattern. O parâmetro /S pode ser usado para que AzCopy procure recursivamente um padrão de nome de arquivo. Sem o parâmetro padrão, o AzCopy baixará todos os arquivos nesse diretório.

<!---HONumber=Oct15_HO1-->