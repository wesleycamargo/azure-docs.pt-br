<properties
   pageTitle="Registrar dados do Repositório Data Lake no Catálogo de Dados do Azure| Azure"
   description="Registrar dados do Repositório Data Lake no Catálogo de Dados do Azure"
   services="data-lake-store,data-catalog" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Registrar dados do Repositório Data Lake no Catálogo de Dados do Azure

Neste artigo, você aprenderá como integrar o Repositório Azure Data Lake ao Catálogo de Dados do Azure para tornar os dados detectáveis dentro de uma organização interagindo com o Catálogo de Dados. Para obter mais informações sobre a catalogação de dados, consulte [Catálogo de Dados do Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Para compreender os cenários em que você pode usar o Catálogo de Dados, consulte [Cenários comuns do Catálogo de Dados do Azure](../data-catalog/data-catalog-common-scenarios.md).

## Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).

- **Conta do Repositório Azure Data Lake**. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md). Para este tutorial, vamos criar uma conta do Repositório Data Lake chamada **datacatalogstore**.

	Depois de criar a conta, carregue um conjunto de dados de exemplo para ela. Para este tutorial, vamos carregar todos os arquivos .csv para na pasta **AmbulanceData** para o [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Você pode usar vários clientes, como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/), para carregar dados em um contêiner de blob.

- **Catálogo de Dados do Azure**. Sua organização já deve ter um Catálogo de Dados do Azure criados para sua organização. É permitido somente um catálogo para cada organização.

## Registre o Repositório Data Lake como uma fonte do Catálogo de Dados

>[AZURE.VIDEO adcwithadl] 

1. Vá para `https://azure.microsoft.com/services/data-catalog` e clique em **Introdução**.

2. Faça logon no portal do Catálogo de Dados do Azure e clique em **Publicar dados**.

	![Registrar uma fonte de dados](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrar uma fonte de dados")

3. Na página seguinte, clique em **Iniciar Aplicativo**. Isso baixará o arquivo de manifesto do aplicativo no seu computador. Clique duas vezes no arquivo de manifesto para iniciar o aplicativo.

4. Na página de Boas-vindas, clique em **Entrar** e insira suas credenciais.

	![Tela de boas-vindas](./media/data-lake-store-with-data-catalog/welcome.screen.png "Tela de boas-vindas")

5. Na página Selecionar um Fonte de Dados, selecione **Azure Data Lake** e clique em **Avançar**.

	![Selecione uma fonte de dados](./media/data-lake-store-with-data-catalog/select-source.png "Selecione uma fonte de dados")

6. Na próxima página, forneça o nome da conta do Repositório Data Lake que você deseja registrar no Catálogo de Dados. Deixe as outras opções como padrão e clique em **Conectar**.

	![Conectar à fonte de dados](./media/data-lake-store-with-data-catalog/connect-to-source.png "Conectar à fonte de dados")

7. A próxima página pode ser dividida nos seguintes segmentos.

	a. A caixa **Hierarquia de Servidor** representa a estrutura de pastas de conta do Repositório Data Lake. **$Root** representa a raiz da conta do Repositório Data Lake e **AmbulanceData** representa a pasta criada na raiz da conta do Repositório Data Lake.

	b. A caixa **Objetos disponíveis** lista os arquivos e pastas na pasta **AmbulanceData**.

	c. A **caixa Objetos a serem registrados** lista os arquivos e pastas que você deseja registrar no Catálogo de Dados do Azure.

	![Exibir estrutura de dados](./media/data-lake-store-with-data-catalog/view-data-structure.png "Exibir estrutura de dados")

8. Para este tutorial, você deve registrar todos os arquivos no diretório. Para fazer isso, clique no botão (![mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "Mover objetos")) para mover todos os arquivos para a caixa **Objetos a serem registrados**.

	Como os dados serão registrados em um catálogo de dados de toda a organização, uma abordagem recomendada é adicionar alguns metadados que você pode usar posteriormente para localizar rapidamente os dados. Por exemplo, você pode adicionar um endereço de email para o proprietário dos dados (por exemplo, que está carregando os dados) ou adicionar uma marca para identificar os dados. A captura de tela abaixo mostra uma marca que adicionamos aos dados.

	![Exibir estrutura de dados](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Exibir estrutura de dados")

	Clique em **Registrar**.

8. A captura de tela a seguir indica que os dados foram registrados com êxito no Catálogo de Dados.

	![Registro concluído](./media/data-lake-store-with-data-catalog/registration-complete.png "Exibir estrutura de dados")

9. Clique em **Exibir Portal** para voltar ao portal do Catálogo de Dados e verificar se agora você pode acessar os dados registrados no portal. Para pesquisar os dados, você pode usar a marca que usada ao registrar os dados.

	![Pesquisar dados no catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Pesquisar dados no catálogo")

10. Agora você pode executar operações como adicionar anotações e documentação aos dados. Para obter mais informações, consulte os links a seguir.
	* [Anotar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
	* [Documentar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)

## Consulte também

* [Anotar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Documentar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar o Repositório Data Lake a outros serviços do Azure](data-lake-store-integrate-with-other-services.md)

<!---HONumber=AcomDC_0803_2016-->