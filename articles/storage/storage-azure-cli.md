<properties
    pageTitle="Usando a CLI do Azure com o Armazenamento do Azure | Microsoft Azure"
    description="Saiba como usar a interface de linha de comando do Azure (CLI do Azure) com o Armazenamento do Azure para criar e gerenciar contas de armazenamento e trabalhar com blobs e arquivos do Azure."
    services="storage"
    documentationCenter="na"
    authors="tamram"
    manager="jdial"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="05/27/2015"
    ms.author="chungli;jiyang;yaxia;tamram"/>

# Usando a CLI do Azure com o Armazenamento do Azure

## Visão geral

A CLI do Azure fornece um conjunto de comandos entre plataformas de software livre para trabalhar com a Plataforma Azure. Ela fornece grande parte das mesmas funcionalidades encontradas no Portal de Gerenciamento do Azure, bem como funcionalidades avançadas de acesso a dados.

Neste guia, exploraremos como usar a [Interface de Linha de Comando do Azure (CLI do Azure)](../xplat-cli.md) para executar uma variedade de tarefas de administração e desenvolvimento com o Armazenamento do Azure. É recomendável baixar e instalar ou atualizar para a CLI mais recente do Azure antes usar este guia.

Este guia pressupõe que você conhece os conceitos básicos do Armazenamento do Azure. Este guia fornece vários scripts que demonstram o uso da CLI do Azure com o Armazenamento do Azure. Não se esqueça de atualizar as variáveis de script com base na sua configuração antes de executar cada script.

> [AZURE.NOTE]O guia fornece os exemplos de comando e script da CLI do Azure em execução no modo ASM (Gerenciamento de Serviços do Azure). Consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) para ver comandos de CLI do Azure para armazenamento no modo ARM (Modo de Gerenciamento de Recursos) do Azure.

## Introdução ao Armazenamento do Azure e à CLI do Azure em 5 minutos

Nos exemplos deste guia, usamos o Ubuntu, mas outras plataformas de sistema operacional devem funcionar de forma semelhante.

**Novo no Azure:** obtenha uma assinatura do Microsoft Azure e uma conta da Microsoft associada a essa assinatura. Para obter informações sobre opções de compra do Azure, confira [Avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/), [Opções de compra](http://azure.microsoft.com/pricing/purchase-options/) e [Ofertas para membros](http://azure.microsoft.com/pricing/member-offers/) (para membros do MSDN, Microsoft Partner Network e BizSpark, entre outros programas da Microsoft).

Consulte [Gerenciar contas, assinaturas e funções administrativas](https://msdn.microsoft.com/library/azure/hh531793.aspx) para saber mais sobre as assinaturas do Azure.

**Depois de criar uma assinatura e conta do Microsoft Azure:**

1. Baixe e instale a CLI do Azure seguindo as instruções descritas em [Instalar a CLI do Azure](../xplat-cli-install.md).
2. Quando a CLI do Azure estiver instalada, você poderá usar o comando azure na sua interface de linha de comando (Bash, Terminal, Prompt de comando) para acessar os comandos da CLI do Azure. Digite o comando `azure` e você deverá ver a saída a seguir.

    ![Saída de comando do Azure][Image1]

3. Na interface de linha de comando, digite `azure storage` para listar todos os comandos de armazenamento do Azure e obter uma primeira impressão das funcionalidades fornecidas pela CLI do Azure. Você pode digitar o nome do comando com o parâmetro **-h** (por exemplo, `azure storage share create -h`) para ver os detalhes da sintaxe de comando.
4. Agora, você terá um script simples que mostra os comandos básicos da CLI do Azure para acessar o Armazenamento do Azure. Primeiramente, o script solicitará que você defina duas variáveis para sua conta e chave de armazenamento. Em seguida, o script criará um novo contêiner nessa nova conta de armazenamento e carregará um arquivo de imagem existente (blob) nesse contêiner. Depois que o script listar todos os blobs nesse contêiner, ele baixará o arquivo de imagem no diretório de destino, que existe no computador local.

		#!/bin/bash
		# A simple Azure storage example

		export AZURE_STORAGE_ACCOUNT=<storage_account_name>
		export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

		export container_name=<container_name>
		export blob_name=<blob_name>
		export image_to_upload=<image_to_upload>
		export destination_folder=<destination_folder>

		echo "Creating the container..."
		azure storage container create $container_name

		echo "Uploading the image..."
		azure storage blob upload $image_to_upload $container_name $blob_name

		echo "Listing the blobs..."
		azure storage blob list $container_name

		echo "Downloading the image..."
		azure storage blob download $container_name $blob_name $destination_folder

		echo "Done"

5. No computador local, abra o editor de texto de sua preferência (vim, por exemplo). Digite o script acima no editor de texto.

6. Agora, você precisa atualizar as variáveis de script com base nas suas configurações.

    - **<storage_account_name>** Use o nome fornecido no script ou insira um novo nome para a conta de armazenamento. **Importante:** o nome da conta de armazenamento deve ser exclusivo no Azure. Ele também deve ter somente letras minúsculas!

    - **<storage_account_key>** A chave de acesso da conta de armazenamento.

    - **<container_name>** Use o nome fornecido no script ou insira um novo nome para o contêiner.

    - **<image_to_upload>** Insira um caminho para uma imagem no computador local, como: "\~/images/HelloWorld.png".

    - **<destination_folder>** Insira um caminho para um diretório local onde armazenar arquivos baixados do Armazenamento do Azure, como: “\~/downloadImages”.

7. Depois de atualizar as variáveis necessárias no vim, pressione as combinações de teclas “Esc, : , wq!” para salvar o script.

8. Para executar esse script, basta digitar o nome do arquivo de script no console do bash. Após execução desse script, você deverá ter uma pasta de destino local que inclua o arquivo de imagem baixado. A captura de tela a seguir mostra um exemplo de saída:

Depois que o script é executado, você deve ter uma pasta de destino que inclui o arquivo de imagem baixada.

## Gerenciar contas de armazenamento com a CLI do Azure

### Conecte-se à sua assinatura do Azure

Embora a maioria dos comandos de armazenamento funcione sem uma assinatura do Azure, é recomendável se conectar à sua assinatura na CLI do Azure. Para configurar a CLI do Azure para trabalhar com sua assinatura, siga as etapas em [Como se conectar à sua assinatura do Azure](../xplat-cli.md#how-to-connect-to-your-azure-subscription).

### Criar uma nova conta de armazenamento

Você precisa de uma conta de armazenamento para usar o Armazenamento do Azure. Depois de configurar seu computador para se conectar à sua assinatura, você pode criar uma nova conta de Armazenamento do Azure.

        azure storage account create <account_name>

O nome da conta de armazenamento deve ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas.

### Definir uma conta de armazenamento padrão do Azure nas variáveis de ambiente

Você pode ter várias contas de armazenamento na sua assinatura. É possível escolher uma delas e defini-la nas variáveis de ambiente para todos os comandos de armazenamento na mesma sessão. Isso permite executar os comandos de armazenamento da CLI do Azure sem especificar explicitamente a chave e a conta de armazenamento.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Outra forma de definir uma conta de armazenamento padrão é usar a cadeia de conexão. Em primeiro lugar, obtenha a cadeia de conexão pelo comando:

        azure storage account connectionstring show <account_name>

Em seguida, copie a cadeia de conexão de saída e a defina como variável de ambiente:

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## Criar e gerenciar blobs

A Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Esta seção pressupõe que você esteja familiarizado com o conceitos de Armazenamento de Blob do Azure. Para obter informações detalhadas, confira [Como usar o Armazenamento de Blob do .NET](storage-dotnet-how-to-use-blobs.md) e [Conceitos do serviço Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### Criar um contêiner

Todos os blobs no armazenamento do Azure devem residir em um contêiner. Você pode criar um contêiner privado usando o comando `azure storage container create`:

        azure storage container create mycontainer

> [AZURE.NOTE]Há três níveis de acesso de leitura anônimo: **Desativado**, **Blob** e **Contêiner**. Para evitar o acesso anônimo a blobs, defina o parâmetro de permissão como **Desativado**. Por padrão, o novo contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público anônimo a recursos de blob, mas não aos metadados do contêiner ou à lista de blobs no contêiner, defina o parâmetro de permissão como **Blob**. Para permitir acesso de leitura público completo a recursos, metadados do contêiner e à lista de blobs no contêiner, defina o parâmetro de permissão como **Contêiner**. Para saber mais, confira [Gerenciar o acesso aos recursos de Armazenamento do Azure](storage-manage-access-to-resources.md).

### Carregar um blob em um contêiner

O Armazenamento de Blob do Azure dá suporte a blobs de blocos e a blobs de páginas. Para saber mais, confira [Noções básicas sobre blobs de blocos e blobs de páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs em um contêiner, você poderá usar `azure storage blob upload`. Por padrão, esse comando carrega os arquivos locais para um blob de blocos. Para especificar o tipo de blob, você pode usar o parâmetro`--blobtype`.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### Baixar blobs de um contêiner

O exemplo a seguir demonstra como baixar blobs de um contêiner.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### Copiar blobs

É possível copiar blobs em ou entre regiões e contas de armazenamento de modo assíncrono.

O exemplo a seguir demonstra como copiar blobs de uma conta de armazenamento para outra. Neste exemplo, podemos criar um contêiner onde os blobs podem ser acessados pública e anonimamente.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

Neste exemplo, é executada uma cópia assíncrona. Você pode monitorar o status de cada operação de cópia, executando a operação `azure storage blob copy show`.

Observe que a URL de origem fornecida para a operação de cópia deve ser acessível publicamente ou incluir um token SAS (assinatura de acesso compartilhado).

### Excluir um blob

Para excluir um blob, use o comando abaixo:

        azure storage blob delete mycontainer myBlockBlob2

## Criar e gerenciar compartilhamentos de arquivos

O armazenamento de arquivos do Azure oferece armazenamento compartilhado para aplicativos usando o protocolo padrão SMB 2.1. As máquinas virtuais e os serviços de nuvem da Microsoft podem compartilhar arquivos de dados entre componentes de aplicativo usando compartilhamentos montados. Você pode gerenciar compartilhamentos de arquivos e dados de arquivos por meio da CLI do Azure. Para saber mais sobre o Armazenamento de Arquivos do Azure, confira [Como usar o Armazenamento de Arquivos do Azure com o PowerShell e .NET](storage-dotnet-how-to-use-files).

> [AZURE.NOTE]O armazenamento de arquivos do Azure está atualmente em visualização. Para solicitar acesso à visualização, vá até a [página de visualização do Microsoft Azure](/services/preview/) e solicite acesso aos **Arquivos do Azure**. Após a solicitação ser aprovada, você será notificado de que pode acessar a visualização do armazenamento de arquivos. Você pode então criar uma conta de armazenamento para acessar o armazenamento de arquivos.

### Criar um compartilhamento de arquivos

Um compartilhamento de arquivos do Azure é um compartilhamento de arquivos SMB 2.1 no Azure. Todos os arquivos e diretórios devem ser criados em um compartilhamento de arquivos. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento. O exemplo a seguir cria um compartilhamento de arquivos denominado **myshare**.

        azure storage share create myshare

### Criar um diretório

Um diretório fornece uma estrutura hierárquica opcional para um compartilhamento de arquivos do Azure. O exemplo a seguir cria um diretório denominado **myDir** no compartilhamento de arquivos.

        azure storage directory create myshare myDir

Observe que esse caminho de diretório pode incluir vários níveis, *por exemplo*: **a/b**. No entanto, você deve garantir a existência de todos os diretórios pai. Por exemplo, para o caminho **a/b**, você deve criar o diretório **a** primeiro e, em seguida, o diretório **b**.

### Carregar um arquivo local no diretório

O exemplo a seguir carrega um arquivo de **\~/temp/samplefile.txt** no diretório **myDir**. Edite o caminho do arquivo para que ele aponte para um arquivo válido em seu computador local:

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Observe que um arquivo no compartilhamento pode ter até 1 TB.

### Listar os arquivos no diretório ou na raiz de compartilhamento

É possível listar os arquivos e subdiretórios em um diretório ou uma raiz de compartilhamento usando o comando a seguir:

        azure storage file list myshare myDir

Observe que o nome do diretório é opcional para a operação de listagem. Se omitido, o comando listará o conteúdo do diretório raiz do compartilhamento.

## Próximas etapas

Veja a seguir alguns artigos e recursos relacionados para saber mais sobre o Armazenamento do Azure.

- [Documentação do Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)
- [Referência de API REST do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
 

<!---HONumber=August15_HO6-->