---
title: 'Tutorial: Pesquisar dados não estruturados no Armazenamento de Blobs do Azure'
description: 'Tutorial: Pesquisando dados não estruturados no Armazenamento de Blobs usando o Azure Search.'
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 8c11d2e6d9a902707d3fd98f09d3474a5d2a5f64
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150784"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Tutorial: Pesquisar dados não estruturados no armazenamento em nuvem

Neste tutorial, você aprenderá a pesquisar dados não estruturados com o [Azure Search](../../search/search-what-is-azure-search.md) usando dados armazenados no Armazenamento de Blobs do Azure. Os dados não estruturados são dados que não são organizados de maneira predefinida ou não têm um modelo de dados. Um exemplo é um arquivo .txt.

Este tutorial requer que você tenha uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo de recursos
> * Criar uma conta de armazenamento
> * Criar um contêiner
> * Carregar dados para o contêiner
> * Criar um serviço de pesquisa no portal
> * Conectar um serviço de pesquisa a uma conta de armazenamento
> * Criar uma fonte de dados
> * Configurar o índice
> * Criar um indexador
> * Usar o serviço de pesquisa para pesquisar o contêiner

## <a name="prerequisites"></a>Pré-requisitos

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Esse tutorial cria um novo grupo de recursos.

Entre no [Portal do Azure](https://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

Preparamos um conjunto de dados de exemplo para ser usado neste tutorial. Baixe [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) e descompacte-o em sua própria pasta.

O exemplo consiste em arquivos de texto obtidos de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Este tutorial usa-os como arquivos de texto de exemplo que são pesquisados usando os serviços do Azure Search.

## <a name="create-a-container"></a>Criar um contêiner

Os contêineres são semelhantes às pastas e são usados para armazenar blobs.

Para este tutorial, você pode usar um contêiner único para armazenar os arquivos de texto obtidos de clinicaltrials.gov.

1. Acesse sua conta de armazenamento no portal do Azure.

2. Selecione **Procurar blobs** em **Serviço Blob**.

3. Adicione um novo contêiner.

4. Dê ao contêiner o nome **dados** e selecione **Contêiner** para o nível de acesso público.

5. Selecione **OK** para criar o contêiner.

   ![Pesquisa não estruturada](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Carregar os dados de exemplo

Agora que você tem um contêiner, pode carregar os dados de exemplo nele.

1. Selecione o contêiner e selecione **Carregar**.

2. Selecione o ícone de pasta azul ao lado do campo **Arquivos** e navegue até a pasta local da qual você extraiu os dados de exemplo.

3. Selecione todos os arquivos extraídos e selecione **Abrir**.

4. Selecione **Carregar** para iniciar o processo de carregamento.

   ![Pesquisa não estruturada](media/storage-unstructured-search/upload.png)

O processo de upload pode levar alguns instantes.

Após a conclusão, volte para seu contêiner de dados para confirmar se os arquivos de texto foram carregados.

  ![Pesquisa não estruturada](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Criar um serviço de pesquisa

O Azure Search é uma solução de pesquisa como serviço na nuvem que oferece aos desenvolvedores as APIs e as ferramentas para adicionar uma experiência de pesquisa nos seus dados.

Para este tutorial, você pode usar um serviço de pesquisa para pesquisar os arquivos de texto obtidos de clinicaltrials.gov.

1. Acesse sua conta de armazenamento no portal do Azure.

2. Role para baixo e selecione **Adicionar Azure Search** em **SERVIÇO BLOB**.

3. Em **Importar dados**, selecione **Escolher seu serviço**.

4. Selecione **Clique aqui para criar um novo serviço de pesquisa**.

5. Em **Novo Serviço de Pesquisa**, insira um nome exclusivo para o serviço de pesquisa no campo **URL**.

6. Em **Grupo de recursos**, selecione **Usar existente** e escolha o grupo de recursos criado anteriormente.

7. Em **Tipo de preço**, selecione a camada **Gratuita** e clique em **Selecionar**.

8. Selecione **Criar** para criar o serviço de pesquisa.

   ![Pesquisa não estruturada](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Conecte o serviço de pesquisa ao seu contêiner

Agora que você tem um serviço de pesquisa, pode anexá-lo ao armazenamento de blobs. Esta seção orienta você pelo processo de escolha de uma fonte de dados, criação de um índice e de um indexador.

1. Vá até sua conta de armazenamento.

2. Selecione **Adicionar Azure Search** em **SERVIÇO BLOB.**

3. Selecione **Serviço de Pesquisa** em **Importar Dados** e clique no serviço de pesquisa que você criou na seção anterior. Isso abre a **Nova fonte de dados**.

### <a name="create-a-data-source"></a>Criar uma fonte de dados

  Uma fonte de dados especifica os dados a serem indexados e como acessar os dados. Uma fonte de dados pode ser usada várias vezes pelo mesmo serviço de pesquisa.

1. Insira um nome para a fonte de dados. Em **Dados para extração**, selecione **Conteúdo e Metadados**. A fonte de dados especifica quais partes do blob são indexadas.

2. Uma vez que os blobs que você está usando são arquivos de texto, defina **Modo de Análise** como **Texto**.

   ![Pesquisa não estruturada](media/storage-unstructured-search/datasources.png)

3. Selecione **Contêiner de Armazenamento** para listar as contas de armazenamento disponíveis.

4. Selecione sua conta de armazenamento e o contêiner que você criou anteriormente.

   ![Pesquisa não estruturada](media/storage-unstructured-search/datacontainer.png)

5. Clique em **Selecionar** para retornar à **Nova fonte de dados** e selecione **OK** para continuar.

### <a name="configure-the-index"></a>Configurar o índice

  Um índice é uma coleção de campos da fonte de dados que podem ser pesquisados. Defina e configure parâmetros para esses campos para que o serviço de pesquisa saiba de que maneiras os dados devem ser pesquisados.

1. Em **Importar dados**, selecione **Personalizar índice de destino**.

2. Insira um nome para o índice no campo **Nome do índice**.

3. Marque a caixa de seleção do atributo **Retrievable** em **metadata_storage_name**.

   ![Pesquisa não estruturada](media/storage-unstructured-search/valuestoselect.png)

4. Clique em **OK**, que abre **Criar um Indexador**.

Os parâmetros de índice e os atributos que você fornecer aos parâmetros são importantes. Os parâmetros especificam *quais* dados armazenar e os atributos especificam *como* armazenar os dados.

A coluna **NOME DO CAMPO** contém os parâmetros. A tabela a seguir fornece uma lista de atributos disponíveis e suas descrições.

#### <a name="field-attributes"></a>Atributos do campo

| Atributo | DESCRIÇÃO |
| --- | --- |
| *Chave* |Uma cadeia de caracteres que fornece a ID exclusiva de cada documento, usada para pesquisar documentos. Todos os índices devem ter uma chave. Somente um campo pode ser a chave e seu tipo deve ser definido para Edm.String. |
| *Recuperável* |Especifica se um campo pode ser retornado em um resultado da pesquisa. |
| *Filtrável* |Permite que o campo seja usado nas consultas de filtro. |
| *Classificável* |Permite que uma consulta classifique os resultados da pesquisa usando esse campo. |
| *Com faceta* |Permite que um campo seja usado em uma estrutura de navegação mista para a filtragem autodirecionada do usuário. Normalmente, os campos que contêm valores repetidos que você pode usar para agrupar documentos (por exemplo, vários documentos que estejam em uma única categoria de marca ou de serviço) funcionam melhor como facetas. |
| *Pesquisável* |Marca o campo como texto completo pesquisável. |

### <a name="create-an-indexer"></a>Criar um indexador

  Um indexador conecta uma fonte de dados a um índice de pesquisa e fornece uma agenda para reindexar seus dados.

1. Insira um nome no campo **Nome** e selecione **OK**.

   ![Pesquisa não estruturada](media/storage-unstructured-search/exindexer.png)

2. Você será direcionado de volta para **Importar Dados**. Selecione **OK** para concluir o processo de conexão.

Agora você conectou com êxito o blob ao serviço de pesquisa. Leva alguns minutos para o portal mostrar que o índice está preenchido. No entanto, o serviço de pesquisa começa logo a indexação, para que você possa começar a pesquisa imediatamente.

## <a name="search-your-text-files"></a>Pesquisar os arquivos de texto

Para pesquisar os arquivos, abra o gerenciador de pesquisa dentro do índice do serviço de pesquisa criado recentemente.

As etapas a seguir mostram onde você pode encontrar o gerenciador de pesquisa e fornece algumas consultas de exemplo:

1. Vá para os recursos e localize o serviço de pesquisa criado recentemente.

   ![Pesquisa não estruturada](media/storage-unstructured-search/exampleurl.png)

2. Selecione o índice para abri-lo.

   ![Pesquisa não estruturada](media/storage-unstructured-search/overview.png)

3. Selecione o **Gerenciador de Pesquisa** para abrir o gerenciador de pesquisa, no qual você pode fazer consultas dinâmicas em seus dados.

   ![Pesquisa não estruturada](media/storage-unstructured-search/indexespane.png)

4. Selecione **Pesquisar** quando o campo de cadeia de caracteres de consulta estiver vazio. Uma consulta vazia retorna *todos* os dados de seus blobs.

   ![Pesquisa não estruturada](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>Executar uma pesquisa de texto completo

Digite **Miopia** no campo **Cadeia de consulta** e selecione **Pesquisar**. Esta etapa inicia uma pesquisa dos conteúdos dos arquivos e retorna um subconjunto deles contendo a palavra "Miopia".

  ![Pesquisa não estruturada](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>Executar uma pesquisa de propriedades do sistema

Além de uma pesquisa de texto completo, você pode criar consultas que pesquisam propriedades do sistema usando o parâmetro `$select`.

Insira `$select=metadata_storage_name` na cadeia de consulta e pressione **Enter**. Com isso, somente um determinado campo é retornado.

A cadeia de consulta está modificando a URL diretamente e, portanto, não são permitidos espaços. Para pesquisar vários campos, use uma vírgula, como:`$select=metadata_storage_name,metadata_storage_path`

O parâmetro `$select` só pode ser usado com campos que são marcados como recuperáveis na definição do índice.

  ![Pesquisa não estruturada](media/storage-unstructured-search/metadatasearchunstructured.png)

Você acabou de concluir este tutorial e tem um conjunto pesquisável de dados não estruturados.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais fácil de remover os recursos que você criou é excluir o grupo de recursos. Remover o grupo de recursos também exclui todos os recursos contidos no grupo. No exemplo a seguir, remover o grupo de recursos remove a conta de armazenamento e o próprio grupo de recursos.

1. No portal do Azure, vá para a lista de grupos de recursos em sua assinatura.
2. Selecione o grupo de recursos que você deseja excluir.
3. Selecione o botão **Excluir grupo de recursos** e insira o nome do grupo de recursos no campo de exclusão.
4. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Siga este link para saber mais sobre indexação de documentos com o Azure Search:

> [!div class="nextstepaction"]
> [Indexando documentos no Armazenamento de Blobs do Azure com o Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)
