<properties
   pageTitle="Como salvar as pesquisas e fixar os ativos de dados | Microsoft Azure"
   description="Artigo de instruções que destaca recursos do Catálogo de Dados do Azure para salvar fontes de dados e ativos de dados para reutilização posterior."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/10/2016"
   ms.author="maroche"/>


# <a name="how-to-save-searches-and-pin-data-assets"></a>Como salvar pesquisas e fixar ativos de dados

## <a name="introduction"></a>Introdução

O Catálogo de Dados do Microsoft Azure fornece recursos para descoberta de fontes de dados. Os usuários podem pesquisar e filtrar rapidamente o catálogo para localizar fontes de dados e entender a finalidade pretendida, facilitando a localização dos dados corretos para o trabalho em questão.

E quando os usuários precisam trabalhar regularmente com os mesmos dados? E quando os usuários contribuem regularmente com seus conhecimentos para as mesmas fontes de dados no catálogo? Nessas situações, ter que emitir repetidamente as mesmas pesquisas pode ser ineficiente. Nesse caso, a pesquisa salva e os ativos de dados fixados podem ajudar.

## <a name="saved-searches"></a>Pesquisas salvas

Uma pesquisa salva no Catálogo de Dados do Azure é uma definição de pesquisa reutilizável por usuário. Quando um usuário define uma pesquisa, incluindo termos de pesquisa, marcas e outros filtros, pode salvá-la para uso posterior. A definição de pesquisa salva pode ser executada novamente mais tarde para retornar ativos de dados que correspondam a seus critérios de pesquisa.

### <a name="creating-a-saved-search"></a>Como criar uma pesquisa salva

Para criar uma pesquisa salva, primeiro insira os critérios de pesquisa a serem reutilizados. Em seguida, clique no link "Salvar" na caixa "Pesquisa Atual" no portal do Catálogo de Dados do Azure.

 ![Selecione “Salvar” para salvar as configurações de pesquisa atuais](./media/data-catalog-how-to-save-pin/01-save-option.png)

Quando solicitado, insira um nome para a pesquisa salva. Escolha um nome que seja significativo e descritivo para os ativos de dados que serão retornados pela pesquisa.

 ![Forneça um nome para a pesquisa salva](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>Gerenciar pesquisas salvas

Depois que um usuário salvar uma ou mais pesquisas, uma opção "Pesquisas Salvas" será exibida no portal do Catálogo de Dados do Azure, na caixa "Pesquisa Atual". Quando expandida, a lista completa de pesquisas salvas será exibida.

 ![Lista de pesquisas salvas](./media/data-catalog-how-to-save-pin/03-list.png)

A seleção de uma pesquisa salva na lista fará com que a pesquisa seja executada.

A seleção do menu suspenso fornecerá um conjunto de opções de gerenciamento:

 ![Opções para gerenciar as pesquisas salvas](./media/data-catalog-how-to-save-pin/04-managing.png)

A seleção de "Renomear" solicitará que o usuário insira um novo nome para a pesquisa salva. A definição de pesquisa não será alterada.

A seleção de "Excluir" solicitará a confirmação do usuário e, em seguida, removerá a pesquisa salva da lista do usuário.

A seleção de "Salvar como Padrão" marcará a pesquisa salva escolhida como a pesquisa padrão para o usuário. Se o usuário executar uma pesquisa "vazia" na home page do Catálogo de Dados do Azure, a pesquisa padrão do usuário será executada. Além disso, a pesquisa marcada como padrão será exibida na parte superior da lista de pesquisas salvas.

### <a name="organizational-saved-searches"></a>Pesquisas organizacionais salvas

Cada usuário pode salvar pesquisas para uso próprio. Os administradores do Catálogo de Dados também podem salvar pesquisas para todos os usuários dentro da organização. Quando você salva uma pesquisa, os administradores têm a opção de compartilhar a pesquisa salva dentro da empresa. Se essa opção for escolhida, a pesquisa salva será incluída na lista de pesquisas disponíveis para todos os usuários.

 ![Pesquisas organizacionais salvas](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)


## <a name="pinned-data-assets"></a>Ativos de dados fixos

As pesquisas salvas permitem aos usuários salvar e reutilizar definições de pesquisa. Os ativos de dados retornados pelas pesquisas podem ser alterados ao longo do tempo, à medida que o conteúdo do catálogo é alterado. A fixação de ativos de dados permite aos usuários identificar explicitamente ativos de dados específicos para facilitar o acesso a eles sem a necessidade de usar uma pesquisa.

A fixação de um ativo de dados é simples: basta os usuários clicarem no ícone "fixar" do ativo de dados para adicioná-lo à lista fixada. Esse ícone é exibido no canto do bloco ativo, no modo de exibição de bloco, e na coluna mais à esquerda, no modo de exibição de lista, no portal do Catálogo de Dados do Azure.

![Como fixar um ativo de dados](./media/data-catalog-how-to-save-pin/05-pinning.png)

Desafixar um ativo é igualmente simples: basta os usuários clicarem novamente ícone "fixar" para alternar a configuração para o ativo selecionado.

![Como desafixar um ativo de dados](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="“my-assets”"></a>"Meus Ativos"
A home page do portal do Catálogo de Dados do Azure inclui uma seção "Meus Ativos" que exibe os ativos de interesse do usuário atual. Essa seção inclui os ativos fixos e as pesquisas salvas.

![“Meus Ativos” na home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
O Catálogo de Dados do Azure fornece recursos que tornam mais fácil para os usuários descobrir as fontes de dados de que precisam. Assim, eles podem gastar menos tempo procurando dados e mais tempo trabalhando com eles. As pesquisas salvas e os ativos de dados fixados se baseiam nesses recursos principais para que os usuários possam facilmente identificar fontes de dados com as quais trabalharão repetidamente.



<!--HONumber=Oct16_HO2-->


