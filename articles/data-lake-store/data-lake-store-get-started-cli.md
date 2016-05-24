<properties
   pageTitle="Introdução ao Repositório Data Lake usando a interface de linha de comando entre plataformas | Microsoft Azure"
   description="Usar a linha de comando da plataforma cruzada do Azure para criar uma conta do Repositório Data Lake e executar operações básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Introdução ao Repositório Azure Data Lake usando a linha de comando do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Saiba como usar a interface de linha de comando do Azure ara criar uma conta do Repositório Azure Data Lake e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações sobre o Repositório Data Lake, veja [Visão geral do Repositório Data Lake](data-lake-store-overview.md).

A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux. A CLI do Azure é de software livre. O código-fonte é gerenciado no GitHub em <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artigo aborda apenas o uso da CLI do Azure com o Repositório Data Lake. Para obter um orientação geral sobre como usar a CLI do Azure, consulte [Cmo usar a CLI do Azure][azure-command-line-tools].


##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).
- **CLI do azure** - consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração. Certifique-se de reinicializar o computador depois de instalar a CLI.

##Faça logon na sua assinatura do Azure

Siga as etapas documentadas em [Conectar a uma assinatura do Azure a partir da Interface de Linha de Comando do Azure (Azure CLI)](../xplat-cli-connect.md) e conectar à sua assinatura usando o método de __logon__.


## Criar uma conta do Repositório Azure Data Lake

Abra uma sessão de prompt, shell ou terminal de comando e execute os comandos a seguir.

1. Faça logon na sua assinatura do Azure:

		azure login

	Você precisará abrir uma página da Web e inserir um código de autenticação. Siga as instruções na página para fazer logon em sua assinatura do Azure.

2. Alterne para modo Gerenciador de Recursos do Azure usando o seguinte comando:

		azure config mode arm


3. Liste as assinaturas do Azure para sua conta.

		azure account list


4. Se você tiver várias assinaturas do Azure, use o comando a seguir para definir a assinatura que os comandos do Azure CLI vai usar:

		azure account set <subscriptionname>

5. Criar um novo grupo de recursos. No comando a seguir fornecem os valores de parâmetro que você deseja usar.

		azure group create <resourceGroup> <location>

	Se o nome do local contiver espaços, coloque-o entre aspas duplas. Por exemplo "Leste dos EUA 2".

5. Crie uma conta do Repositório Data Lake.

		azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## Crie pastas na conta do Repositório Data Lake

Você pode criar pastas em sua conta do Repositório Azure Data Lake para gerenciar e armazenar dados. Use o seguinte comando para criar uma pasta chamada "mynewfolder" na raiz do Repositório Data Lake.

	azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por exemplo:

	azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## Carregar dados no Repositório Azure Data Lake

É possível carregar seus dados no Repositório Data Lake diretamente no nível da raiz ou em uma pasta que você criou na conta. Os trechos de código abaixo demonstram como carregar alguns dados de exemplo no diretório (**mynewdirectory**) criado na seção anterior.

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixe o arquivo e armazene-o em um diretório local no computador, como C:\\sampledata.

	azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por exemplo:

	azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## Listar os arquivos no Repositório Data Lake

Use o comando a seguir para listar os arquivos em uma conta do Repositório Data Lake.

	azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por exemplo:

	azure datalake store filesystem list mynewdatalakestore /mynewfolder

A saída desse comando deve ser:

	info:    Executing command datalake store filesystem list
	data:    accessTime: 1446245025257
	data:    blockSize: 268435456
	data:    group: NotSupportYet
	data:    length: 1589881
	data:    modificationTime: 1446245105763
	data:    owner: NotSupportYet
	data:    pathSuffix: vehicle1_09142014.csv
	data:    permission: 777
	data:    replication: 0
	data:    type: FILE
	data:    ------------------------------------------------------------------------------------
	info:    datalake store filesystem list command OK

## Renomear, baixar e excluir dados do Repositório Data Lake

* **Para renomear um arquivo**, use o seguinte comando:

    	azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

	Por exemplo:

		azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Para baixar um arquivo**, use o seguinte comando: Verifique se o caminho de destino especificado já existe.

		azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

	Por exemplo:

		azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Para excluir um arquivo**, use o seguinte comando:

		azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

	Por exemplo:

		azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

	Quando solicitado, insira **Y** para excluir o item.

## Exibir a lista de controle de acesso para uma pasta no Repositório Data Lake

Use o seguinte comando para exibir as ACLs em uma pasta do Repositório Data Lake. Na versão atual, as ACLs podem ser definidas somente na raiz do Repositório Data Lake. Portanto, o parâmetro path abaixo será sempre raiz (/).

	azure datalake store permissions show <dataLakeStoreName> <path>

Por exemplo:

	azure datalake store permissions show mynewdatalakestore /


## Excluir sua conta do Repositório Data Lake

Use o comando a seguir para excluir sua conta do Repositório Data Lake.

	azure datalake store account delete <dataLakeStoreAccountName>

Por exemplo:

	azure datalake store account delete mynewdatalakestore

Quando solicitado, insira **Y** para excluir a conta.


## Próximas etapas

- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0518_2016-->