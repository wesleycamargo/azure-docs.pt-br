---
title: "Pesquisar dados não estruturados no armazenamento em nuvem do Azure"
description: "Pesquisando dados não estruturados usando o Azure Search."
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Pesquisar dados não estruturados no armazenamento em nuvem

Neste tutorial, você aprenderá a pesquisar dados não estruturados usando o [Azure Search](../../search/search-what-is-azure-search.md) com dados armazenados em blobs do Azure. Os dados não estruturados são dados que não são organizados de maneira predefinida ou não têm um modelo de dados. Um exemplo seria um arquivo .txt.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo de recursos
> * Criar uma conta de armazenamento
> * Criar um contêiner
> * Carregar dados para o contêiner
> * Criar um serviço de pesquisa no portal
> * Usar o serviço de pesquisa para pesquisar o contêiner

## <a name="download-the-sample"></a>Baixar o exemplo

Um conjunto de dados de exemplo foi preparado para você. **Baixe [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** e descompacte-o em sua própria pasta.

O exemplo consiste em arquivos de texto obtidos de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Você pode usá-los como arquivos de texto de exemplo para pesquisar usando o Azure.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece um local exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure.

Atualmente, existem dois tipos de contas de armazenamento, **Blob** e **Uso geral**. Para este tutorial, crie uma conta de armazenamento de **Uso geral**.

Se você não estiver familiarizado com o processo de criação de conta de armazenamento de uso geral, veja como criar uma:

1. No menu à esquerda, selecione **Contas de Armazenamento** e selecione **Adicionar**.

2. Insira um nome exclusivo para sua conta de armazenamento. 

3. Selecione **Gerenciador de Recursos** como **Modelo de implantação** e selecione **Uso geral** no menu suspenso **Tipo de conta**.

4. Selecione **LRS (armazenamento localmente redundante)** na lista suspensa **Replicação**.

5. Em **Grupo de recursos**, selecione **Criar novo** e insira um nome exclusivo.

6. Selecione a assinatura apropriada.

7. Escolha um local e selecione **Criar.**

  ![Pesquisa não estruturada](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Criar um contêiner

Os contêineres são semelhantes às pastas e são usados para armazenar blobs.

Para este tutorial, você pode usar um contêiner único para armazenar os arquivos de texto obtidos de clinicaltrials.gov.

1. Navegue até sua conta de armazenamento no portal do Azure.

2. Selecione **Procurar blobs** em **Serviço Blob.**

3. Adicione um novo contêiner.

4. Nomeie o contêiner "dados" e selecione **Contêiner** como nível de acesso público.

5. Selecione **OK** para criar o contêiner. 

  ![Pesquisa não estruturada](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Carregar os dados de exemplo

Agora que você tem um contêiner, pode carregar os dados de exemplo nele.

1. Selecione o contêiner e selecione **Carregar**.

2. Selecione o ícone de pasta azul mostrado ao lado do campo Arquivos e navegue até a pasta local de onde você extraiu os dados de exemplo.

3. Selecione todos os arquivos extraídos e selecione **Abrir**

4. Selecione **Carregar** para iniciar o processo de carregamento.

  ![Pesquisa não estruturada](media/storage-unstructured-search/upload.png)

O processo de carregamento pode levar alguns instantes.

Depois de concluir, navegue para o seu contêiner de dados para confirmar o carregamento dos arquivos de texto.

  ![Pesquisa não estruturada](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Criar um serviço de pesquisa

O Azure Search é uma solução de pesquisa como serviço na nuvem que oferece aos desenvolvedores APIs e ferramentas para adicionar uma experiência de pesquisa avançada aos dados em aplicativos Web, móveis e empresariais.

Se você não estiver familiarizado com o processo de criação do serviço de pesquisa, veja como criá-lo:

1. Navegue até sua conta de armazenamento no portal do Azure.

2. Role para baixo e clique em **Adicionar Azure Search** em **SERVIÇO BLOB**.

3. Em **Importar dados**, selecione **Escolher seu serviço**.

4. Selecione **Clique aqui para criar um novo serviço de pesquisa**.

5. Em **Novo Serviço de Pesquisa**, insira um nome exclusivo para o serviço de pesquisa no campo **URL**.

6. Em **Grupo de recursos**, selecione **Usar existente** e escolha o grupo de recursos criado anteriormente.

7. Em **Tipo de preço**, selecione a camada **Gratuita** e clique em **Selecionar**.

8. Selecione **Criar** para criar o serviço de pesquisa.

  ![Pesquisa não estruturada](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Conecte o serviço de pesquisa ao seu contêiner

Agora que você tem um serviço de pesquisa, pode anexá-lo ao armazenamento de blobs. Esta seção orienta você pelo processo de escolha de uma fonte de dados, criação de um índice e de um indexador.

1. Navegue até sua conta de armazenamento.

2. Selecione **Adicionar Azure Search** em **SERVIÇO BLOB.**

3. Selecione **Serviço de Pesquisa** em **Importar Dados** e clique no serviço de pesquisa criado na seção anterior. Isso abre a **Nova fonte de dados**.

### <a name="new-data-source"></a>Nova fonte de dados

  Uma fonte de dados especifica os dados a serem indexados e como acessar os dados. Uma fonte de dados pode ser usada várias vezes pelo mesmo serviço de pesquisa.

1. Insira um nome para a fonte de dados. Em **Dados para extração**, selecione **Conteúdo e Metadados**. A fonte de dados especifica quais partes do blob são indexadas.
    
    a. Em seus próprios cenários futuros, você também pode selecionar **Somente metadados de armazenamento**. Faça essa seleção se desejar limitar os dados que são indexados a propriedades de blob padrão ou propriedades definidas pelo usuário.
    
    b. Você também pode escolher **Todos os metadados** para obter as propriedades de blob padrão e *todos* os metadados específicos do tipo de conteúdo. 

2. Já que os blobs que você está usando são arquivos de texto, defina **Modo de Análise** como **Texto**.
    
    a. Em seus próprios cenários futuros, talvez seja ideal selecionar [outros modos de análise](../../search/search-howto-indexing-azure-blob-storage.md), dependendo do conteúdo dos blobs.

  ![Pesquisa não estruturada](media/storage-unstructured-search/datasources.png)

3. Selecione **Contêiner de Armazenamento** para listar as contas de armazenamento disponíveis.

4. Selecione sua conta de armazenamento e o contêiner que você criou anteriormente.

5. Clique em **Selecionar** para retornar à **Nova fonte de dados** e selecione **OK** para continuar.

  ![Pesquisa não estruturada](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Configurar o índice

  Um índice é uma coleção de campos da fonte de dados que podem ser pesquisados. O índice é como o serviço de pesquisa sabe de que forma os dados devem ser pesquisados.

1. Em **Importar dados**, selecione **Personalizar índice de destino**.
 
2. Insira um nome para o índice no campo **Nome do índice**.

3. Marque a caixa de seleção do atributo **Recuperável** em **metadata_storage_name**.

  ![Pesquisa não estruturada](media/storage-unstructured-search/valuestoselect.png)

4. Clique em **OK**, que gera **Criar um Indexador**.

Os parâmetros de índice e os atributos que você fornecer aos parâmetros são importantes. Os parâmetros especificam *quais* dados armazenar; os atributos especificam *como* armazenar os dados.

A coluna **NOME DO CAMPO** contém os parâmetros. A tabela a seguir fornece uma lista de atributos disponíveis e suas descrições.

### <a name="field-attributes"></a>Atributos do campo
| Atributo | Descrição |
| --- | --- |
| *Chave* |Uma cadeia de caracteres que fornece a ID exclusiva de cada documento, usada para pesquisar documentos. Todos os índices devem ter uma chave. Somente um campo pode ser a chave e seu tipo deve ser definido para Edm.String. |
| *Recuperável* |Especifica se um campo pode ser retornado em um resultado da pesquisa. |
| *Filtrável* |Permite que o campo seja usado nas consultas de filtro. |
| *Classificável* |Permite que uma consulta classifique os resultados da pesquisa usando esse campo. |
| *Com faceta* |Permite que um campo seja usado em uma estrutura de navegação mista para a filtragem autodirecionada do usuário. Normalmente, os campos que contêm valores repetidos que você pode usar para agrupar vários documentos (por exemplo, vários documentos que estejam em uma única categoria de marca ou de serviço) funcionam melhor como facetas. |
| *Pesquisável* |Marca o campo como texto completo pesquisável. |


### <a name="create-an-indexer"></a>Criar um indexador
    
  Um indexador conecta uma fonte de dados a um índice de pesquisa e fornece uma agenda para reindexar seus dados.

1. Insira um nome no campo **Nome** e selecione **OK**.

  ![Pesquisa não estruturada](media/storage-unstructured-search/exindexer.png)

2. Você é trazido de volta para **Importar dados**. Selecione **OK** para concluir o processo de conexão.

Agora você conectou com êxito o blob ao serviço de pesquisa. Leva alguns minutos para o portal mostrar que o índice está preenchido. No entanto, o serviço de pesquisa começa logo a indexação, para que você possa começar a pesquisa imediatamente.

## <a name="search-your-text-files"></a>Pesquisar os arquivos de texto

Para pesquisar os arquivos, abra o gerenciador de pesquisa dentro do índice do serviço de pesquisa criado recentemente.

As etapas a seguir mostram onde você pode encontrar o gerenciador de pesquisa e fornece algumas consultas de exemplo:

1. Navegue até os recursos e localize o serviço de pesquisa criado recentemente.

  ![Pesquisa não estruturada](media/storage-unstructured-search/exampleurl.png)

3. Selecione o índice para abri-lo. 

  ![Pesquisa não estruturada](media/storage-unstructured-search/overview.png)

4. Selecione o **Gerenciador de Pesquisa** para abrir o gerenciador de pesquisa, onde você pode fazer consultas dinâmicas em seus dados.

  ![Pesquisa não estruturada](media/storage-unstructured-search/indexespane.png)

5. Selecione **Pesquisar** quando o campo de cadeia de caracteres de consulta estiver vazio. Uma consulta vazia retorna *todos* os dados de seus blobs.

  ![Pesquisa não estruturada](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Pesquisa de texto completo 

Digite "Miopia" no campo **Cadeia de consulta** e selecione **Pesquisar**. Iniciando uma pesquisa de conteúdo dos arquivos e retornando um subconjunto deles contendo a palavra "Miopia".

  ![Pesquisa não estruturada](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Pesquisa de propriedades do sistema

Você também pode criar consultas que pesquisam por propriedades de sistema usando o parâmetro `$select`. Digite `$select=metadata_storage_name` na cadeia de consulta e pressione Enter, retornando somente determinado campo.
    
A cadeia de consulta está modificando a URL diretamente e, portanto, não são permitidos espaços. Para pesquisar vários campos, use uma vírgula, como:`$select=metadata_storage_name,metadata_storage_path`
    
O parâmetro `$select` só pode ser usado com campos que são marcados como recuperáveis na definição do índice.

  ![Pesquisa não estruturada](media/storage-unstructured-search/metadatasearchunstructured.png) 

Você acabou de concluir este tutorial e tem um conjunto pesquisável de dados não estruturados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a pesquisa de dados não estruturados usando o Azure Search, como:

> [!div class="checklist"]
> * Criar um grupo de recursos
> * Criar uma conta de armazenamento
> * Criar um contêiner
> * Carregando dados para seu contêiner
> * Criar um Serviço de Pesquisa
> * Usando o Serviço de Pesquisa para pesquisar o contêiner

Siga este link para saber mais sobre indexação de documentos com o Azure Search.

> [!div class="nextstepaction"]
> [Indexando documentos no Armazenamento de Blobs do Azure com o Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)