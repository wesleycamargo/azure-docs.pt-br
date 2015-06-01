<properties 
	pageTitle="Criar um índice de Pesquisa do Azure no portal" 
	description="Adicionar um índice ao serviço de Pesquisa do Azure, preenchendo as definições de campo no portal de gerenciamento" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Criar um índice de Pesquisa do Azure no portal

Você pode rapidamente gerar um protótipo de índice na Pesquisa do Azure, criando um no portal de gerenciamento do Azure. Usar o portal é ótimo para testes de verificação de conceito, mas também é possível usá-lo para exibir definições de esquema e uso de recursos para qualquer índice implantado em seu serviço.

Para concluir essa tarefa, verifique se você tem um serviço de Pesquisa do Azure que está pronto para ser usado. Consulte [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) se precisar de ajuda para configurá-lo.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. Aqui estão algumas maneiras para localizar o painel.
	- Na barra de navegação rápida, clique em **Home**. A home page conta com blocos para cada serviço em sua assinatura. Clique no bloco para abrir o painel de serviços.
	- Na barra de navegação rápida, clique em **Procurar** | **Filtrar por** | **Serviços de pesquisa** para localizar o serviço de pesquisa na lista. 

3. No painel do serviços, você verá uma barra de comandos na parte superior, inclusive um para **Adicionar índice**.
	
	Verifique a camada de preços. Se tiver a versão gratuita, você poderá ter até 3 índices. Talvez seja necessário excluir um para liberar espaço.

     ![][1]

4. Para excluir um índice, clique em um para abrir uma folha fazendo-a deslizar. Clique em **Excluir**.

     ![][2]

5. Para criar um novo índice no portal, clique em **Adicionar índice** e dê a ele um nome, como *hotéis*.

	Pode levar um minuto para criar o índice, mas quando ele estiver pronto para se trabalhar com, aparecerá na lista de índices.

6. Clique em *hotéis* para abrir a folha de definição de índice.

	Quando você cria um índice no portal, um campo obrigatório (id) é criado para você. Este é o campo de chave, usado para identificar exclusivamente cada documento. Há apenas um campo por chave (sem chaves compostas) e é sempre uma cadeia de caracteres.

	Se você deseja renomear o campo de chave, é importante fazer isso agora, durante a criação do índice. Você não poderá renomear o campo depois que o índice for criado.

	![][3]

7. Para editar o nome do campo, clique na seta para a direita na lista de campos.

8. Substitua *id* por *hotelId*.

9. Clique em **OK** em cada folha (campos e índice) para criar o índice.

##Adicionar campos

Na Pesquisa do Azure, atributos de índice como pesquisável, facetável e filtrável são habilitados por padrão. Você define esses atributos geralmente para desativar os comportamentos de pesquisa que não fazem sentido (por exemplo, classificação ou facetagem em uma descrição).

O portal é diferente. No portal, comportamentos de pesquisa são desativados por padrão para que você possa selecionar todos os comportamentos que se aplicam, campo por campo.

1. Clique em **Adicionar/editar campos** para adicionar mais campos. Neste exercício, recriaremos o índice *hotéis* mencionado no artigo [Como usar o Fiddler com a Pesquisa do Azure](search-fiddler.md). 

	![][4]

2. Adicionar e configurar campos para concluir o esquema.

	![][5]

	Consulte [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) e [Tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter informações de referência sobre tipos e nomes de campos.

3. Clique em **Salvar** na parte superior da página.

  	![][6]

##Próximas etapas

Embora o índice esteja definido, ele não estará pronto para uso até que você carregue documentos. Para fazer isso facilmente, prossiga com [Como usar o Fiddler com a Pesquisa do Azure](search-fiddler.md), em **Carregar Documentos**. Em seguida, você pode seguir as etapas restantes nesse artigo para executar algumas consultas.

Quando você estiver familiarizado com o índice básico, considere a possibilidade de adicionar um analisador de linguagem ou sugestor para adicionar suporte a vários idiomas ou sugestões de preenchimento automático. Os dois recursos são especificados no esquema de índice. Consulte [Suporte a Idiomas](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) e [Criar Índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obter mais informações.

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG
[6]: ./media/search-create-index-portal/AzureSearch-PortalIndex-6.PNG
<!--HONumber=54-->