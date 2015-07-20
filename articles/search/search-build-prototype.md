<properties 
	pageTitle="Criar um aplicativo de protótipo para a Pesquisa do Azure" 
	description="Crie o seu primeiro protótipo de aplicativo para começar a usar a Pesquisa do Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Criar um aplicativo de protótipo para a Pesquisa do Azure

Os desenvolvedores que avaliam a Pesquisa do Azure quase sempre começam com um aplicativo de teste preliminar que demonstra a importância de adicionar a Pesquisa do Azure para um aplicativo personalizado. Neste artigo, damos alguns blocos de construção para acelerar o processo de criação de protótipos.
 
- Um projeto do Visual Studio C# que inclui arquivos data.json e schema.json. Ter dados de exemplo permite criar e executar imediatamente a solução, confirmando que a solução funciona em seu sistema antes de escrever uma linha de código. 

	Se possível, tente substituir estes arquivos de dados e esquemas autônomos, desconectados pelos dados de sua empresa. Em seguida, quando você executar o aplicativo, o índice criado é baseado no seu esquema e os documentos com base em seus dados de arquivos estarão imediatamente disponíveis para trabalhar com a Pesquisa do Azure.

- Orientação sobre como estruturar os dados que você carregará na Pesquisa do Azure. É necessário um conjunto de dados para cada índice. A Pesquisa do Azure usa JSON para serialização de dados.

- Por fim, indicaremos a você alguns recursos adicionais que levarão o protótipo para o próximo nível, incorporando recursos de pesquisa específicos como navegação mista, perfis de pontuação para resultados de ajustes, preenchimento automático ou consultas de preenchimento automático e funcionalidade relacionada à página de pesquisa que não está disponível na alternativa pesquisa de texto obrigatoriamente completo.

No final, você terá um aplicativo de protótipo, usando seus dados, para demonstrar uma experiência de pesquisa robusta usando a Pesquisa do Azure.

## Preparar os dados

As operações de pesquisa são executadas em um índice de pesquisa que você cria na Pesquisa do Azure. Um índice tem várias partes, incluindo a pontuação de perfis e suggesters, mas a maior parte de um índice consiste em campos de dados usados em operações de pesquisa.

O conjunto de dados que fornece os campos deve ser um arquivo de dados simples (em JSON), onde cada campo no conjunto de dados é mapeado para um campo equivalente no índice que você está tentando preencher. Outras partes do índice, tais como, um perfil de pontuação ou opções de CORS são adicionados independentemente, diretamente no esquema.

Fontes de dados que usam JSON nativamente, tais como, Armazenamento de Tabela do Azure, fornecerão dados que são mais fáceis de consumir que dados relacionais, exigirá um único modo de exibição ou tabela que fornece todos os campos em um conjunto de linhas.

Para carregar os dados e arquivos de esquema usando o código no aplicativo de exemplo de protótipo, você pode substituir os arquivos de dados e esquema padrões que são inseridos na solução.

> [AZURE.NOTE]Você pode carregar vários arquivos de dados, talvez para carregar dados em lotes, mas a estrutura dos dados deve ser a mesma para todos os arquivos de dados que você carregar para o mesmo índice. Você não pode participar de índices; cada índice funciona como um corpo de busca autônomo.

## Testar a solução de protótipo em seu sistema

1. [Criar um serviço de Pesquisa do Azure no portal do Azure](search-create-service-portal.md)

    Você pode adicionar uma versão compartilhada (gratuita) do serviço para qualquer assinatura existente do Azure. O serviço compartilhado é geralmente usado para protótipos. Tenha em mente que o serviço compartilhado fornece 50 MB de dados ou 10.000 documentos no total, o que ocorrer primeiro. Além disso, os documentos e dados podem ser distribuídos para um máximo de 3 índices.

    Executando o aplicativo de exemplo de protótipo, com os arquivos de dados de exemplo interno, será criado um índice chamado "musicstoreindex", que contém 246 documentos, com 278 KB no seu serviço de Pesquisa do Azure. Posteriormente neste passo a passo, você substituirá esse índice por um novo usando os dados da Adventure Works que consumirão até ## documentos e ## MB.

2. [Baixar a solução do construtor de protótipo](http://go.microsoft.com/fwlink/p/?LinkId=536479). Essa é uma solução do Visual Studio 2013 em C# que cria um aplicativo de console, usado para criar, carregar e consultar um índice. Se você não tiver o Visual Studio, você pode obter gratuitamente a versão gratuita da [edição Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx).

3. Edite um app.config para adicionar definições de configuração que tem o serviço de Pesquisa e a chave de api como destino.

	Você pode obter a URL e a chave de api do administrador a partir do [painel de serviço no portal](search-create-service-portal.md). Para a URL, digite o caminho completo do nome do serviço, incluindo o prefixo https e o domínio `search.windows.net`.

4. Compile a solução para garantir que não há nenhum erro de compilação. Talvez seja necessário atualizar pacotes para resolver erros de compilação. Clique com o botão direito do mouse em **Gerenciar Pacotes NuGet** na solução para atualizar os pacotes.

5. Execute a solução usando os arquivos de dados e esquema internos. Esta etapa é opcional, mas ela confirma que a solução funciona antes de você investir o seu tempo para adicionar os seus próprios dados. O console gera as seguintes mensagens:

	- Índice excluído (ocorre somente se existir um índice de nome "musicstoreindex")
	- Índice criado (um novo índice chamado "musicstoreindex" é criado em seu serviço)
	- Documentos postados (uma mensagem para cada arquivo JSON)
	- Aguarde 5 segundos (permite que a indexação seja concluída antes de emitir a primeira consulta)
	- Resultados da pesquisa de “melhor” termo de pesquisa com nenhum estímulo (executa uma consulta simples para verificar que os dados são carregados no índice)

6. Interrompa o aplicativo e exclua o índice para liberar espaço para o seu índice.

    > [AZURE.TIP]Você pode usar [o portal](https://portal.azure.com) para excluir o índice. Clique no nome de índice para abrir a folha de índice e usar o comando **Excluir**.

## Substitua os arquivos de esquema e os dados JSON pelos seus dados

Na solução de protótipo, há o arquivo de um esquema e três arquivos de dados: data1.json, data2.json e data3.json. O arquivo de esquema (schema.json) deve ser substituído por um esquema que descreve os dados.

Por padrão, esses arquivos estão localizados na pasta da solução:

![][1]

Se você pode obter os dados em arquivos JSON, você pode substituir os arquivos existentes pelos seus dados e, em seguida, executar o aplicativo para criar e carregar um índice. A distribuição de dados entre vários arquivos ajuda a demonstrar uma operação de carregamento em lote.

Outras abordagens para carregar dados incluem o uso de um indexador (requer uma fonte de dados do Banco de Dados de Documentos do Azure ou uma fonte de dados do Banco de dados SQL do Azure). Um código de exemplo que demonstra maneiras adicionais de carregamento de dados pode ser encontrado no [Vídeo de Pesquisa do Azure e na Lista de Tutoriais](https://msdn.microsoft.com/library/azure/dn818681.aspx) no MSDN.

### Editar a consulta

A consulta padrão incorporada na solução referencia os campos e estruturas de arquivos de dados internos. Após sobrescrever os arquivos de dados para que os seus dados sejam usados, você pode comentar o código de consulta ou modificá-lo para usar campos do seu esquema. Para obter mais informações sobre a sintaxe de consulta de pesquisa, consulte [Pesquisar documentos](https://msdn.microsoft.com/library/azure/dn798927.aspx).

### Compile e execute o aplicativo

Pressione **F5** para executar o aplicativo. Como antes, você deverá ver mensagens indicando que o índice foi criado, carregado e que pode ser consultado.

Agora você tem um índice operacional que você pode usar como base para obter mais informações.

Neste ponto, talvez você queira alternar para [Fiddler](search-fiddler.md) ou [Chrome Postman](search-chrome-postman.md) para executar consultas, modificar qualquer um dos aplicativos de exemplo para usar o serviço de pesquisa e índice ou adicionar código personalizado que fornece visualização de dados.

## Adicionar um perfil de pontuação

Sugerimos que você experimente os [perfis de pontuação](search-get-started-scoring-profiles.md) por meio do portal ou por meio de código. Os perfis de pontuação aumentam a importância de um termo de pesquisa encontrado em um campo específico. Por exemplo, se o termo de pesquisa é um nome de cidade, você esperaria documentos tendo *Seattle* no campo Cidade aparecendo antes nos resultados do que documentos com *Seattle* em um campo de Descrição.

Adicionar um perfil de pontuação altera índice; você precisará recompilar e recarregar o índice sempre que você modificar o esquema. Por esse motivo, é recomendável adicionar segmentos de código de perfil de pontuação para o seu protótipo (ou modificar os exemplos de perfil de pontuação para usar o código de indexação).

Consulte [Adicionar um perfil de pontuação](https://msdn.microsoft.com/library/dn798928.aspx) e veja a documentação de referência sobre perfis de pontuação.

## Adicionar um suggesters

Suggesters refere-se ao recurso de pesquisa popular que projeta uma lista de possíveis termos de pesquisa com base em entradas de texto do usuário (digitando "tem" é exibida uma lista de termos de pesquisa de preenchimento automático para "tempo", "previsão do tempo" e assim por diante).

Para adicionar suggesters, adicione uma seção para o esquema de índice que especifica quais campos são usados para criar consultas de preenchimento automático ou typeahead. Campos que contêm nomes ou cadeias de caracteres mais curtas sem valores repetidos costumam funcionar melhor. Consulte [Criar Índice](https://msdn.microsoft.com/library/dn798928.aspx) para obter mais informações.

## Tente um analisador de linguagem

Analisadores de linguagem fornecem as regras linguísticas para fazer distinção entre palavras essenciais e não essenciais, formulários de raiz e até mesmo sinônimos. Por padrão, a pesquisa do Azure usa o analisador de linguagem Lucene para inglês. Você pode especificar diferentes analisadores como um atributo de índice em campos específicos, que permite que você crie esquemas e documentos que incluem campos usando analisadores diferentes (por exemplo, um aplicativo multilíngue pode combinar campos em francês e inglês lado a lado no mesmo documento). Consulte [Suporte ao idioma](https://msdn.microsoft.com/library/dn879793.aspx) para obter mais informações.

## Próximas etapas

Nas seções anteriores, você modificou o índice para adicionar mais funcionalidades ao seu protótipo. Neste ponto, as alterações de índice estão quase concluídas (não mencionamos habilitando CORS, que é a única alteração de esquema restante que você pode fazer).

A partir daqui, a criação do protótipo poderá seguir duas direções diferentes: um foco para fora da interface do usuário, talvez adicionando navegação facetada ou outros filtros que ajudam a restringir a pesquisa ou explorar ainda mais os aspectos de sincronização de dados de sua solução. Muitas soluções possuem dados voláteis. Para muitos desenvolvedores, sincronizar atualizações de dados entre sistemas de banco de dados transacionais e um índice de Pesquisa do Azure é a prioridade seguinte após os comportamentos de pesquisa básicos terem sido verificados.

Visite estes links para saber mais:

- [Fluxos de trabalho típicos para projetos de desenvolvimento usando a Pesquisa do Azure](search-workflow.md)

- [Personalização do indexador da Pesquisa do Azure](search-indexers-customization.md)

- [Navegação facetada na Pesquisa do Azure](search-faceted-navigation.md)

- [Resultados da pesquisa de paginação na Pesquisa do Azure](search-pagination-page-layout.md)


<!--Image references-->
[1]: ./media/search-build-prototype/azsearch-datafiles.png
 

<!---HONumber=July15_HO2-->