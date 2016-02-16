<properties 
	pageTitle="Introdução à Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa na nuvem | Banco de Dados de Documentos" 
	description="Crie sua primeira solução de Pesquisa do Azure usando este tutorial passo a passo. Saiba como criar um índice de Pesquisa do Azure usando dados do Banco de Dados de Documentos. Este é um exercício sem código baseado no portal, usando o assistente para Importação de dados." 
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
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/10/2016" 
	ms.author="heidist"/>

# Introdução à Pesquisa do Azure no Portal do Azure

A Pesquisa do Microsoft Azure é um serviço de nuvem que você pode usar para adicionar funcionalidade de pesquisa a aplicativos personalizados. Ela fornece o mecanismo de pesquisa e armazenamento para seus dados, os quais você acessa e gerencia usando o Portal do Azure, um SDK do .NET ou uma API REST.

Este artigo é uma introdução sem código à Pesquisa do Azure, usando recursos incorporados ao portal. Este tutorial pressupõe um [banco de dados de exemplo do Banco de Dados de Documentos do Azure](#apdx-sampledata) que você pode criar rapidamente usando nossos dados e instruções de exemplo, mas também é possível aplicar esse fluxo de trabalho a dados existentes no Banco de Dados de Documentos ou em um Banco de Dados SQL.

> [AZURE.NOTE] Este tutorial requer uma [Assinatura do Azure](../includes/free-trial-note.md) e um [Serviço de pesquisa do Azure](search-create-service-portal.md). Se você não estiver pronto para se inscrever para uma assinatura de avaliação, pode ignorar este tutorial e optar por [Experimentar o Serviço de Aplicativo do Azure](search-tryappservice.md). Essa opção alternativa fornece uma Pesquisa do Azure com um aplicativo Web ASP.NET gratuitamente, uma hora por sessão, sem precisar de uma assinatura.
 
## Localizar o serviço

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. Aqui estão algumas maneiras para localizar o painel.
	- Na barra de navegação, clique em **Serviços de pesquisa**. A barra de navegação lista todos os serviços provisionados em sua assinatura. Se um serviço de pesquisa tiver sido definido, você verá **Serviços de pesquisa** na lista.
	- Na barra de navegação, clique em **Procurar** e digite "pesquisa" na caixa de pesquisa para produzir uma lista de todos os serviços de pesquisa criados em suas assinaturas.

## Verificar o espaço

Muitos clientes começam com o serviço gratuito. Essa versão é limitada a três índices, três fontes de dados e três indexadores. Verifique se há espaço para itens extras antes de iniciar este passo a passo. Este passo a passo criará um de cada objeto.

## Criar um índice e carregar dados

Consultas de pesquisa são iteradas em um *índice* que contém dados pesquisáveis, metadados e constructos usados para otimizar certos comportamentos de pesquisa. Como primeira etapa, você precisará definir e popular um índice.

Há várias maneiras de criar um índice. As abordagens variam em quantidade de integração ou automação oferecida. Se você tiver dados utilizáveis em um repositório de dados que a Pesquisa do Azure possa rastrear (como o Banco de Dados SQL do Azure, o SQL Server em uma VM do Azure ou o Banco de Dados de Documentos), poderá criar e popular um índice muito facilmente usando um indexador.

Para simplificar a tarefa, vamos supor o uso de uma fonte de dados que a Pesquisa do Azure possa rastrear usando um de seus *indexadores* e o assistente para **Importar dados**.

Como pré-requisito, você pode criar rapidamente [um banco de dados de exemplo do Banco de Dados de Documentos](#apdx-sampledata) para usar este tutorial ou pode tentar executar as etapas usando seus próprios dados.

<a id="defineDS"></a>
#### Etapa 1: definir a fonte de dados

1. No painel de serviço de Pesquisa do Azure, clique em **Importar dados** na barra de comandos para iniciar um assistente que cria e popula um índice.

2. Clique em **Fonte de dados** > **Banco de Dados de Documentos** > **Nome** e digite um nome para a fonte de dados. Uma fonte de dados é um objeto de conexão na Pesquisa do Azure que pode ser usado com outros indexadores. Após ser criado, ele se torna disponível como uma "fonte de dados existente" em seu serviço.

3. Escolha sua conta existente do Banco de Dados de Documentos e o banco de dados e a coleção. Se você estiver usando os dados de exemplo que fornecemos, a definição de fonte de dados terá esta aparência:

  ![][2]

Observe que estamos pulando a consulta. Isso ocorre porque não estamos implementando o acompanhamento de alterações em nosso conjunto de dados desta vez. Se o conjunto de dados incluir um campo que acompanha quando um registro é atualizado, você poderá configurar um indexador de Pesquisa do Azure para usar o acompanhamento de alterações para atualizações seletivas no índice.

Clique em **OK** para concluir esta etapa do assistente.

#### Etapa 2: definir o índice

Ainda no assistente, clique em **Índice** e confira a superfície de design usada para criar um índice de Pesquisa do Azure. No mínimo, um índice requer um nome e uma coleção de campos, com um campo marcado como a chave do documento. Como estamos usando um conjunto de dados do Banco de Dados de Documentos, os campos são detectados automaticamente pelo assistente, e o índice é pré-carregado com campos e atribuições de tipo de dados.

  ![][3]

Embora os campos e os tipos de dados sejam configurados, você ainda precisa atribuir atributos. As caixas de seleção na parte superior da lista de campos são *atributos de índice* que controlam como o campo é usado.

- **Recuperável** significa que ele aparece na lista de resultados de pesquisa. Você pode marcar campos individuais como fora dos limites para os resultados de pesquisa, por exemplo, quando os campos são usados somente em expressões de filtro. 
- **Filtrável**, **Classificável** e **Com faceta** determinam se um campo pode ser usado em um filtro, uma classificação ou uma estrutura de navegação de facetas. 
- **Pesquisável** significa que um campo é incluído na pesquisa de texto completo. Campos numéricos e boolianos geralmente são marcados como não pesquisáveis. 

Antes de você sair dessa página, marque os campos no índice para usar as opções a seguir (Recuperável, Pesquisável e assim por diante). A maioria dos campos é Recuperável. A maioria dos campos de cadeia de caracteres é Pesquisável (você não precisa tornar a chave pesquisável). Alguns campos, como gênero, orderableOnline, classificação e marcas também são Filtrável, Classificável e Com faceta.
	
Campo | Tipo | Opções |
------|------|---------|
chave | Edm.String | |
albumTitle | Edm.String | Recuperável, Pesquisável |
albumUrl | Edm.String | Recuperável, Pesquisável |
gênero | Edm.String | Recuperável, Pesquisável, Filtrável, Classificável, Com faceta |
genreDescription | Edm.String | Recuperável, Pesquisável |
artistName | Edm.String | Recuperável, Pesquisável |
orderableOnline | Edm.Boolean | Recuperável, Filtrável, Classificável, Com faceta |
marcas | Collection(Edm.String) | Recuperável, Filtrável, Com faceta |
preço | Edm.Double | Recuperável, Filtrável, Com faceta |
margem | Edm.Int32 | |
classificação | Edm.Int32 | Recuperável, Filtrável, Classificável, Com faceta |
inventário | Edm.Int32 | Recuperável |
lastUpdated | Edm.DateTimeOffset | |

Como um ponto de comparação, a captura de tela a seguir é uma ilustração de um índice criado de acordo com a especificação da tabela anterior.

 ![][4]

Clique em **OK** para concluir esta etapa do assistente.

#### Etapa 3: definir o indexador

Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome**, digite um nome para o indexador e use os padrões para todos os outros valores. Esse objeto define um processo executável. Depois de criá-lo, você pode colocá-lo na agenda de recorrência, mas, por enquanto, use a opção padrão para executar o indexador uma vez, imediatamente, quando você clicar em **OK**.

As entradas de dados de importação devem estar todas preenchidas e prontas.

  ![][5]

Para executar o assistente, clique em **OK** para iniciar a importação e fechar o assistente.

## Verificar o andamento

Para verificar o andamento, volte para o painel do serviço e clique duas vezes no bloco **Indexadores** para abrir a lista de indexadores. Você deverá ver o indexador que acabou de criar na lista e o status que indica "em andamento" ou sucesso, juntamente com o número de documentos indexados na Pesquisa do Azure.

  ![][6]

## Consulte o índice

Agora você tem um índice de pesquisa que está pronto para consulta.

O **Gerenciador de pesquisa** é uma ferramenta de consulta interna do portal. Ele fornece uma caixa de pesquisa para que você possa verificar se uma entrada de pesquisa retorna os dados esperados.

1. Clique em **Gerenciador de pesquisa** na barra de comandos.
2. Observe qual índice está ativo. Se não for aquele que você acabou de criar, clique em **Alterar índice** na barra de comandos para selecionar o desejado.
2. Deixe a caixa de pesquisa vazia e, em seguida, clique no botão **Pesquisar** para executar uma pesquisa curinga que retorna todos os documentos.
3. Insira algumas consultas de pesquisa de texto completo. Você pode examinar os resultados da pesquisa curinga para se familiarizar com artistas, álbuns e gêneros para consulta.
4. Experimente outra sintaxe de consulta usando os [exemplos fornecidos no fim deste artigo](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter ideias, modificando sua consulta para usar cadeias de caracteres de pesquisa que podem ser encontradas no índice.

## Próximas etapas

Depois de executar o assistente uma vez, você pode voltar e exibir ou modificar componentes individuais: índice, indexador ou fonte de dados. Algumas edições, como a alteração do tipo de dados do campo, não são permitidas no índice, mas a maioria das propriedades e configurações pode ser modificada. Para exibir componentes individuais, clique no bloco **Índice**, **Indexador** ou **Fontes de Dados** no painel para exibir uma lista de objetos existentes.

Para saber mais sobre outros recursos mencionados neste artigo, acesse estes links:

- [Indexadores](search-indexer-overview.md)
- [Criar índice (inclui uma explicação detalhada sobre os atributos de índice)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Gerenciador de Pesquisa](search-explorer.md)
- [Pesquisar documentos (inclui exemplos de sintaxe de consulta)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

Você pode experimentar esse mesmo fluxo de trabalho usando o assistente para importação de dados para outras fontes de dados, como um Banco de Dados SQL ou o SQL Server em máquinas virtuais do Azure.

> [AZURE.NOTE] O suporte do indexador para rastrear o Armazenamento de Blobs do Azure foi recém-lançado, mas esse recurso está em visualização e ainda não é uma opção do portal. Para testar o indexador, você precisará escrever código. Confira [Indexação do armazenamento de blobs do Azure na Pesquisa do Azure](search-howto-indexing-azure-blob-storage.md) para obter mais informações. <a id="apdx-sampledata"></a>


## Apêndice: obter dados de exemplo deste banco de dados do Banco de Dados de Documentos

Esta seção cria um pequeno banco de dados no Banco de dados de Documentos que pode ser usado para concluir as tarefas deste tutorial.

As instruções a seguir oferecem orientação geral, mas não abrangem tudo. Se precisar de ajuda com tarefas ou navegação do portal do Banco de Dados de Documentos, você poderá consultar a documentação do Banco de Dados de Documentos, mas a maioria dos comandos de que precisará está na barra de comandos de serviço na parte superior do painel ou na folha de banco de dados.

  ![][1]

Fornecemos 246 documentos JSON para este conjunto de dados. Você precisa carregar esses documentos em vários lotes (menores do que 100) para atender aos requisitos de carregamento do Gerenciador de Documentos.

[Clique aqui](https://github.com/HeidiSteen/azure-search-get-started-sample-data) para baixar os arquivos de dados JSON de repositório de música.

1. Adicione o Banco de Dados de Documentos à sua assinatura e abra o painel de serviço.
2. Clique em **Adicionar Banco de Dados** para criar um novo banco de dados com a ID `musicstoredb`. Ele aparecerá em uma lista de banco de dados mais abaixo na página, após sua criação.
2. Clique no nome do banco de dados para abrir a folha de banco de dados.
3. Clique em **Adicionar coleção** para criar uma coleção com a ID `musicstorecoll`.
3. Clique em **Gerenciador de documentos**.
4. Clique em **Adicionar documentos**.
5. Em **Adicionar documento**, carregue os arquivos JSON.
	- 386\.json
	- 387\.json
	- . . .
6. Clique em **Gerenciador de Consultas** para verificar se os dados foram carregados.
7. Uma maneira fácil de fazer isso é modificar a consulta padrão para que ela selecione os primeiros 300 (há menos de 300 itens no total), ou você pode escrever `select * from musicstorecoll` e clicar em **Executar consulta**.

Você deve obter a saída JSON, começando pelo documento número 386 e terminando com o documento 669. Depois que os dados forem carregados, você poderá [usá-lo para iniciar este passo a passo](#defineDS) usando o **Assistente de importação** da Pesquisa do Azure.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png

<!---HONumber=AcomDC_0211_2016-->