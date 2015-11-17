<properties
	pageTitle="Criar um índice da Pesquisa do Azure no portal | Microsoft Azure | Serviço de pesquisa hospedado na nuvem"
	description="Adicione um índice à Pesquisa do Azure, um serviço de pesquisa hospedado na nuvem, preenchendo as definições de campo no portal do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Criar um índice de Pesquisa do Azure no portal do Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST API](search-create-index-rest-api.md)

Você pode rapidamente gerar um protótipo de índice na Pesquisa do Azure, criando um no portal do Azure. Usar o portal é ótimo para testes de verificação de conceito, mas também é possível usá-lo para exibir definições de esquema e uso de recursos para qualquer índice implantado em seu serviço.

Para concluir essa tarefa, verifique se você tem um serviço de Pesquisa do Azure que está pronto para ser usado. Consulte [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) se precisar de ajuda para configurá-lo.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. Aqui estão algumas maneiras para localizar o painel.
	- Na barra de navegação rápida, clique em **Home**. A home page conta com blocos para cada serviço em sua assinatura. Clique no bloco para abrir o painel de serviços.
	- Na barra de navegação, clique em **Procurar tudo** > **Filtrar por** > **Serviços de Pesquisa** para localizar o serviço de Pesquisa na lista.

3. No painel do serviços, você verá uma barra de comandos na parte superior, inclusive um para **Adicionar índice**.

	Verifique a camada de preços. Se tiver a versão gratuita, você poderá ter até 3 índices. Talvez seja necessário excluir um para liberar espaço.

     ![][1]

4. Para excluir um índice, clique em um para abrir uma folha fazendo-a deslizar. Clique em **Excluir**.

     ![][2]

5. Para criar um novo índice no portal, clique em **Adicionar índice** e dê a ele um nome, como *hotéis*.

	Pode levar um minuto para criar o índice, mas quando ele estiver pronto para se trabalhar com, aparecerá na lista de índices.

6. Clique em **hotéis** para abrir a folha de definição de índice.

	Quando você cria um índice no portal, um campo obrigatório (id) é criado para você. Este é o campo de chave, usado para identificar exclusivamente cada documento. Há apenas um campo por chave (sem chaves compostas) e é sempre uma cadeia de caracteres.

	Se você deseja renomear o campo de chave, é importante fazer isso agora, durante a criação do índice. Você não poderá renomear o campo depois que o índice for criado.

	![][3]

7. Para editar o nome do campo, clique na seta para a direita na lista de campos.

8. Substitua **id** por **hotelId**.

9. Clique em **OK** em cada folha (campos e índice) para criar o índice.

## Adicionar campos

Na Pesquisa do Azure, atributos de índice como pesquisável, facetável e filtrável são habilitados por padrão. Você define esses atributos geralmente para desativar os comportamentos de pesquisa que não fazem sentido (por exemplo, classificação ou facetagem em uma descrição).

O portal é diferente. No portal, comportamentos de pesquisa são desativados por padrão para que você possa selecionar todos os comportamentos que se aplicam, campo por campo.

1. Clique em **Adicionar/editar campos** para adicionar mais campos. Neste exercício, recriaremos o índice *hotéis* descrito no artigo [Como usar o Fiddler com a Pesquisa do Azure](search-fiddler.md).

	![][4]

2. Adicionar e configurar campos para concluir o esquema.

	![][5]

	Analise as [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) e [Tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter informações de referência sobre tipos e nomes de campos.

    Os atributos de índice consistem dos seguintes itens:

	- **Recuperável** especifica se um campo pode ser retornado em um resultado de pesquisa.
	- **Filtrável** permite que o campo seja usado nas consultas **$filter**.
	- **Classificável** permite que o campo seja usado como uma opção de classificação.
	- **Facetável** permite que um campo seja usado em uma estrutura de navegação facetada para filtragem autodirigida. Normalmente os campos que contêm valores repetidos que você pode usar para agrupar vários documentos (por exemplo, vários documentos que estão em um único produto ou serviço) funcionam melhor como facetas.
	- **Chave** é a ID exclusiva de cada documento, usada para pesquisar documentos. Todos os índices devem ter uma chave. Somente um campo pode ser a chave e deve ser definido para o Edm.String.
	- **Pesquisável** marca o campo como texto completo que pode ser pesquisado.

3. Para remover todos os campos não desejados, clique com botão direito do mouse e selecione **Excluir**.

4. Clique em **OK** para salvar o índice que você acabou de definir e, em seguida, clique em **OK** na página Adicionar índice para criar o índice.


## Próximas etapas

Embora o índice esteja definido, ele não estará pronto para uso até que você carregue documentos. Suponha que você está recriando o índice Hotéis, usado para fins de teste, você pode carregar facilmente um pequeno número de documentos para aquele índice no [Fiddler](search-fiddler.md), usando as instruções na seção Carregar documentos de [Como usar o Fiddler com a Pesquisa do Azure](search-fiddler.md). Em seguida, você pode seguir as etapas restantes nesse artigo para executar algumas consultas.

Quando você estiver familiarizado com o índice básico, considere a possibilidade de adicionar um analisador de linguagem ou sugestor para adicionar suporte a vários idiomas ou sugestões de preenchimento automático. Os dois recursos são especificados no esquema de índice. Consulte [Suporte a Idiomas](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) e [Criar Índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obter mais informações.

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG

<!---HONumber=Nov15_HO3-->