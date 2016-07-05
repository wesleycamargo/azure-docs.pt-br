<properties 
	pageTitle="Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure | Microsoft Azure" 
	description="Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure" 
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
	ms.date="06/14/2016"
	ms.author="bradsev" />

# Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure

O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita do Windows para inspecionar e alterar dados em uma conta de Armazenamento do Azure. Este tópico descreve como usá-lo para carregar e baixar os dados do armazenamento de blobs do Azure. A ferramenta pode ser baixada no [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).

Orientações sobre as tecnologias usadas para mover dados para e/ou do armazenamento de blobs do Azure estão vinculadas aqui:
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
&nbsp;
 
> [AZURE.NOTE] Se você estiver usando a VM configurada com os scripts fornecidos pelas [Máquinas virtuais de ciências de dados no Azure](machine-learning-data-science-virtual-machines.md), o Gerenciador de armazenamento do Azure já estará instalado na VM.
 
&nbsp;
 
> [AZURE.NOTE] Para obter uma introdução completa ao armazenamento de Blobs do Azure, consulte [Noções básicas do Serviço Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Pré-requisitos

Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure.

- Para configurar uma assinatura do Azure, confira [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
- Para obter instruções sobre como criar uma conta de armazenamento e para obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../storage/storage-create-storage-account.md).


<a id="explorer"></a>
## Usar o Gerenciador de Armazenamento do Azure 

As etapas a seguir mostram como carregar/baixar os dados usando o Gerenciador de Armazenamento do Azure.

1.  Abra o Gerenciador de Armazenamento do Azure 
2.  Se a conta de armazenamento que você deseja acessar não tiver sido adicionada ao Gerenciador de Armazenamento do Azure, clique no botão "Adicionar Conta" para adicionar a conta. Se já tiver sido adicionada, selecione a conta na lista suspensa “--Selecione uma conta de armazenamento.” ![Criar espaço de trabalho][1] <br>
3. Digite o nome e a chave de conta de armazenamento e clique em Adicionar Conta de Armazenamento. Você pode adicionar várias contas de armazenamento e cada conta será exibidas em uma guia. Os contêineres nesta conta de armazenamento são mostrados no painel à esquerda. Selecione um contêiner para ver os blobs no contêiner no painel à direita. ![Criar espaço de trabalho][2] <br> ![Criar espaço de trabalho][3] <br>
4. Carregue os dados clicando no botão "Carregar". Selecione um ou vários arquivos para carregar do sistema de arquivos e clique em "Abrir" para começar a carregar os arquivos.
5. Baixe os dados selecionando o blob no contêiner correspondente e clicando no botão “Baixar".

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=AcomDC_0622_2016-->