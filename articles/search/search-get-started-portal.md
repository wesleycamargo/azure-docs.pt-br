<properties 
	pageTitle="Introdução ao Azure Search | Microsoft Azure | DocumentDB | Serviço de pesquisa na nuvem" 
	description="Aprenda a criar seu primeiro índice do Azure Search usando este tutorial passo a passo e os dados de exemplo do DocumentDB. Este é um exercício sem código e baseado no portal, que usa o Assistente para Importação de Dados." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="jhubbard" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.date="10/03/2016" 
	ms.author="heidist"/>

# Introdução à Pesquisa do Azure no portal

Esta introdução sem código ajuda você a começar a usar a Pesquisa do Microsoft Azure com recursos incorporados ao portal.

Este tutorial pressupõe um [banco de dados de exemplo do Banco de Dados de Documentos do Azure](#apdx-sampledata) simples para criar usando nossos dados e instruções, mas também é possível adaptar estas etapas aos dados existentes no Banco de Dados de Documentos ou no Banco de Dados SQL.

> [AZURE.NOTE] Este tutorial requer uma [Assinatura do Azure](/pricing/free-trial/?WT.mc_id=A261C142F) e um [serviço Azure Search](search-create-service-portal.md).
 
## Localizar o serviço

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. Aqui estão algumas maneiras para localizar o painel.
	- Na barra de navegação, clique em **Serviços de pesquisa**. A barra de navegação lista todos os serviços provisionados em sua assinatura. Se um serviço de pesquisa tiver sido definido, você verá os **Serviços de pesquisa** na lista.
	- Na barra de navegação, clique em **Procurar** e digite "pesquisa" na caixa de pesquisa para produzir uma lista de todos os serviços de pesquisa criados em suas assinaturas.

## Verificar o espaço

Muitos clientes começam com o serviço gratuito. Essa versão é limitada a três índices, três fontes de dados e três indexadores. Verifique se há espaço para itens extras antes de começar. Este passo a passo cria um de cada objeto.

## Criar um índice e carregar dados

Consultas de pesquisa são iteradas em um *índice* que contém os dados pesquisáveis, metadados e construções usados para otimizar certos comportamentos de pesquisa. Como primeira etapa, você precisa definir e popular um índice.

Há várias maneiras de criar um índice. Se os dados estiverem em um repositório que a Pesquisa do Azure pode rastrear - como o Banco de Dados SQL, SQL Server em uma VM do Azure ou Banco de Dados de Documentos - você poderá criar e preencher um índice muito facilmente usando um *indexador*.

Para mantermos essa tarefa baseada no portal, supomos que os dados do DocumentDB possam ser rastreados com um indexador via assistente **Importar dados**.

Antes de continuar, crie um [banco de dados de exemplo do Banco de Dados de Documentos](#apdx-sampledata) a ser usado com este tutorial e volte para esta seção para concluir as etapas abaixo.

<a id="defineDS"></a>
#### Etapa 1: definir a fonte de dados

1. No painel de serviço de Pesquisa do Azure, clique em **Importar dados** na barra de comandos para iniciar um assistente que cria e preenche um índice.

    ![][7]

2. No assistente, clique em **Fonte de Dados** > **Banco de Dados de Documentos** > **Nome** e digite um nome para a fonte de dados. Uma fonte de dados é um objeto de conexão na Pesquisa do Azure que pode ser usado com outros indexadores. Após ser criado, ele se torna disponível como uma "fonte de dados existente" em seu serviço.

3. Escolha sua conta existente do Banco de Dados de Documentos e o banco de dados e a coleção. Se você estiver usando os dados de exemplo que fornecemos, a definição de fonte de dados terá esta aparência:

    ![][2]

Observe que estamos pulando a consulta. Isso ocorre porque não estamos implementando o acompanhamento de alterações em nosso conjunto de dados desta vez. Se o conjunto de dados incluir um campo que acompanha quando um registro é atualizado, você poderá configurar um indexador de Pesquisa do Azure para usar o acompanhamento de alterações para atualizações seletivas no índice.

Clique em **OK** para concluir esta etapa do assistente.

#### Etapa 2: definir o índice

Ainda no assistente, clique em **Índice** e veja a superfície de design usada para criar um índice de Pesquisa do Azure. No mínimo, um índice requer um nome e uma coleção de campos, com um campo marcado como a chave do documento. Como estamos usando um conjunto de dados do Banco de Dados de Documentos, os campos são detectados automaticamente pelo assistente, e o índice é pré-carregado com campos e atribuições de tipo de dados.

  ![][3]

Embora os campos e os tipos de dados sejam configurados, você ainda precisa atribuir atributos. As caixas de seleção na parte superior da lista de campos são os *atributos do índice* que controlam como o campo é usado.

- **Recuperável** significa que ele aparece na lista de resultados da pesquisa. Você pode marcar campos individuais como fora dos limites para os resultados de pesquisa ao desmarcar essa caixa de seleção, por exemplo, quando os campos forem usados somente em expressões de filtro.
- **Filtrável**, **Classificável** e **Com faceta** determinam se um campo pode ser usado em um filtro, em uma classificação ou em uma estrutura de navegação com facetas.
- **Pesquisável** significa que um campo é incluído na pesquisa de texto completo. As cadeias de caracteres geralmente são pesquisáveis. Campos numéricos e boolianos geralmente são marcados como não pesquisáveis.

Antes de você sair dessa página, marque os campos no índice para usar as opções a seguir (Recuperável, Pesquisável e assim por diante). A maioria dos campos é Recuperável. A maioria dos campos de cadeia de caracteres é Pesquisável (você não precisa tornar a chave pesquisável). Alguns campos, como gênero, orderableOnline, classificação e marcas também são Filtrável, Classificável e Com faceta.
	
Campo | Tipo | Opções |
------|------|---------|
ID | Edm.String | |
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

Ainda no assistente **Importar dados**, clique em **Indexador** > **Nome**, digite um nome para o indexador e use os padrões para todos os outros valores. Esse objeto define um processo executável. Depois de criá-lo, você poderá colocá-lo na agenda de recorrência, mas, por enquanto, use a opção padrão para executar o indexador imediatamente, logo que clicar em **OK**.

As entradas de dados de importação devem estar todas preenchidas e prontas.

  ![][5]

Para executar o assistente, clique em **OK** para iniciar a importação e fechar o assistente.

## Verificar o andamento

Para verificar o andamento, volte para o painel do serviço, role para baixo e clique duas vezes no bloco **Indexadores** para abrir a lista de indexadores. Você deverá ver o indexador que acabou de criar na lista e o status que indica "em andamento" ou sucesso, juntamente com o número de documentos indexados na Pesquisa do Azure.

  ![][6]

## Consulte o índice

Agora você tem um índice de pesquisa que está pronto para consulta.

O **Gerenciador de pesquisa** é uma ferramenta de consulta interna do portal. Ele fornece uma caixa de pesquisa para que você possa verificar se uma entrada de pesquisa retorna os dados esperados.

1. Clique em **Gerenciador de pesquisa** na barra de comandos.
2. Observe qual índice está ativo. Se não for aquele que você acabou de criar, clique em **Alterar índice** na barra de comandos para selecionar o desejado.
2. Deixe a caixa de pesquisa vazia e clique no botão **Pesquisar** para executar uma pesquisa curinga que retorna todos os documentos.
3. Insira algumas consultas de pesquisa de texto completo. Você pode examinar os resultados da pesquisa curinga para se familiarizar com artistas, álbuns e gêneros para consulta.
4. Experimente outra sintaxe de consulta usando os [exemplos fornecidos no fim deste artigo](https://msdn.microsoft.com/library/azure/dn798927.aspx) para ter ideias, modificando sua consulta para usar cadeias de caracteres de pesquisa que provavelmente podem ser encontradas no índice.

## Próximas etapas

Depois de executar o assistente uma vez, você pode voltar e exibir ou modificar componentes individuais: índice, indexador ou fonte de dados. Algumas edições, como a alteração do tipo de dados do campo, não são permitidas no índice, mas a maioria das propriedades e configurações pode ser modificada. Para exibir os componentes individuais, clique no bloco **Índice**, **Indexador** ou **Fontes de Dados** no painel para exibir uma lista dos objetos existentes.

Para saber mais sobre outros recursos mencionados neste artigo, acesse estes links:

- [Indexadores](search-indexer-overview.md)
- [Criar índice (inclui uma explicação detalhada sobre os atributos de índice)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Gerenciador de Pesquisa](search-explorer.md)
- [Pesquisar documentos (inclui exemplos de sintaxe de consulta)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

Você pode experimentar esse mesmo fluxo de trabalho usando o assistente para importação de dados para outras fontes de dados, como um Banco de Dados SQL ou o SQL Server em máquinas virtuais do Azure.

> [AZURE.NOTE] O suporte do indexador para rastrear o Armazenamento de Blobs do Azure foi recém-lançado, mas esse recurso está em visualização e ainda não é uma opção do portal. Para testar o indexador, você precisa escrever código. Consulte [Indexação do armazenamento de Blobs do Azure na Pesquisa do Azure](search-howto-indexing-azure-blob-storage.md) para saber mais. <a id="apdx-sampledata"></a>


## Apêndice: Criar dados de exemplo no Banco de Dados de Documentos

Esta seção cria um pequeno banco de dados no Banco de dados de Documentos que pode ser usado para concluir as tarefas deste tutorial.

As instruções a seguir oferecem orientação geral, mas não abrangem tudo. Se precisar de ajuda com tarefas ou navegação do portal do DocumentDB, você poderá consultar a documentação do DocumentDB, mas a maioria dos comandos de que precisará está na barra de comandos de serviço na parte superior do painel ou na folha de banco de dados.

  ![][1]

### Criar musicstoredb para este tutorial

1. [Clique aqui](https://github.com/HeidiSteen/azure-search-get-started-sample-data) para baixar um arquivo ZIP com os arquivos de dados JSON do repositório de músicas. Fornecemos 246 documentos JSON para este conjunto de dados.
2. Adicione o DocumentDB à sua assinatura e abra o painel de serviço.
2. Clique em **Adicionar Banco de Dados** para criar um novo banco de dados com uma ID `musicstoredb`. Ele aparece em um bloco de banco de dados mais abaixo na página, após sua criação.
2. Clique no nome do banco de dados para abrir a folha de banco de dados.
3. Clique em **Adicionar Coleção** para criar uma coleção com a ID `musicstorecoll`.
3. Clique em **Gerenciador de Documentos**.
4. Clique em **Carregar**.
5. Em **Carregar Documento**, navegue até a pasta local que contém os arquivos JSON baixados anteriormente. Selecione os arquivos JSON em lotes de 100 ou menos.
	- 386\.json
	- 387\.json
	- . . .
	- 486\.json
6. Repita para obter o próximo lote de arquivos até que você tenha carregado o último, 669.json.
7. Clique em **Gerenciador de Consultas** para verificar se os dados são carregados para atender aos requisitos de carregamento do Gerenciador de Documentos.

Uma maneira fácil de fazer isso é usar a consulta padrão, mas você também pode modificar a consulta padrão para que ela selecione os primeiros 300 (há menos de 300 itens nesse conjunto de dados).

Você deve obter a saída JSON, começando pelo documento número 386 e terminando com o documento 669. Depois dos dados serem carregados, você poderá [voltar para as etapas neste passo a passo](#defineDS) para criar um índice usando o **Assistente de importação de dados**.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png

<!---HONumber=AcomDC_1005_2016-->