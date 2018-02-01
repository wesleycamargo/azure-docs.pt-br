---
title: Gerenciar custos usando o Gerenciamento de Custos do Azure | Microsoft Docs
description: "Gerencie custos usando a alocação de custos e relatórios de análise e estorno."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 804b50d6ba054bbb0eb60b659c98f161ea5272ee
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="manage-costs-by-using-azure-cost-management"></a>Gerenciar custos usando o Gerenciamento de Custos do Azure

Gerencie os custos e produza relatórios de análise no Gerenciamento de Custos do Azure por Cloudyn alocando os custos com base em marcas. O processo de alocação de custos atribui os custos aos recursos de nuvem consumidos. Os custos são totalmente alocados quando todos os seus recursos são categorizados com marcas. Depois que os custos são alocados, você pode fornecer análise ou estorno para seus usuários com painéis e relatórios. Porém, muitos recursos poderão estar desmarcados ou não ser marcáveis quando você começar a usar o Gerenciamento de Custos.

Por exemplo, você talvez queira obter reembolso para custos de engenharia. Você precisa ser capaz de mostrar à sua equipe de engenharia que é necessário um valor específico, com base em custos de recursos. Você pode mostrar um relatório para todos os recursos consumidos que estiverem marcados como *engenharia*.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Use marcas personalizadas para alocar os custos.
> * Crie relatórios de análise e de estorno.

## <a name="use-custom-tags-to-allocate-costs"></a>Usar marcas personalizadas para alocar os custos

Quando você inicia a alocação de custos, a primeira coisa a que fazer é definir o escopo por meio de um modelo de custo. O modelo de custo não altera os custos, mas os distribui. Quando você cria um modelo de custo, você segmenta os dados por entidade de custo, conta ou assinatura, bem como por várias marcas. Marcas de exemplo comuns podem incluir um código de faturamento, o centro de custo ou o nome de grupo. Marcas também lhe ajudam a executar análise ou estorno para outras partes da organização.

Para criar um modelo de alocação de custo personalizado, selecione **Custo** &gt; **Gerenciamento de Custos** &gt; **Alocação de Custo 360°** no menu do relatório.

![Seleção de Alocação de Custo 360](./media/tutorial-manage-costs/cost-allocation-360.png)

Na página **Alocação de Custo 360**, selecione **Adicionar** e, em seguida, insira um nome e uma descrição para o modelo de custo. Selecione uma das opções todas as contas e contas individuais. Se você quiser usar contas individuais, você poderá selecionar várias contas de vários provedores de serviço de nuvem. Em seguida, clique em **Categorização** para escolher as marcas de descoberta que categorizam seus dados de custo. Escolha as marcas (categorias) que você deseja incluir em seu modelo. No exemplo abaixo, a marca **Unidade** foi selecionada.

![Categorização de modelo de custo de exemplo](./media/tutorial-manage-costs/cost-model01.png)



O exemplo mostra que U$ 14.444 é não categorizado (sem marcas).

Em seguida, selecione **Recursos não Categorizados** e selecione os serviços que têm custos não alocados. Em seguida, defina regras para alocar os custos.

Por exemplo, convém pegar seus custos de armazenamento do Azure e distribuí-los igualmente em VMs (máquinas virtuais) do Azure. Para fazer isso, selecione o serviço **Armazenamento do Azure**, selecione **Proporcional ao Categorizado**e, em seguida, selecione **Azure/VM**. Em seguida, selecione **Criar**.

![Regra de alocação de modelo de custo de exemplo para distribuição igual](./media/tutorial-manage-costs/cost-model02.png)



Em um exemplo diferente, você talvez queira alocar todos os seus custos de rede do Azure para uma unidade de negócios específica na sua organização. Para fazer isso, selecione o serviço **Azure/Rede** e, em seguida, selecione **Distribuição Explícita**. Em seguida, defina o percentual de distribuição para 100 e selecione a unidade de negócios —**G&amp;A** na imagem a seguir:

![Regra de alocação de modelo de custo de exemplo para uma unidade de negócios específica](./media/tutorial-manage-costs/cost-model03.png)



Para todos os recursos não categorizados restantes, crie regras de alocação adicionais.

Se você tiver quaisquer instâncias reservadas de AWS (Amazon Web Services) não alocadas, você poderá atribuí-las a categorias marcadas com **Instâncias Reservadas**.

Para exibir informações sobre as escolhas feitas alocar custos, selecione **Resumo**. Para salvar as informações e continuar trabalhando em regras adicionais mais tarde, selecione **Salvar Como Rascunho**. Ou então, para salvar as informações e fazer o Cloudyn começar a processar seu modelo de alocação de custo, selecione **Salvar e Ativar**.

A lista de modelos de custo mostra o novo modelo de custo com o **Status do processamento**. Pode levar algum tempo até o banco de dados do Cloudyn seja atualizado com o modelo de custo. Quando o processamento for concluído, o status será atualizado para **Concluído**. Em seguida, você poderá exibir dados do seu modelo de custo no relatório de análise de custo em **Filtros Estendidos** &gt; **Modelo de Custo**.

### <a name="category-manager"></a>Gerenciador de categoria

O Gerenciador de Categorias é uma ferramenta de limpeza de dados que lhe ajudará a mesclar os valores das várias categorias (tags) para criar outros. Ele é uma ferramenta simples baseada em regra em que você seleciona uma categoria e cria regras para mesclar os valores existentes. Por exemplo, você pode ter categorias existentes para **R&amp;D** e **desenvolvimento** em que ambas representam o grupo de desenvolvimento.

No portal do Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Gerenciamento de Categorias**. Para criar uma nova categoria, selecione o símbolo de adição (**+**). Insira um nome para a categoria e, em seguida, em **Chaves**, insira as chaves de categoria que você deseja incluir na nova categoria.

Quando você define uma regra, você pode adicionar vários valores com uma condição OR. Você também pode fazer algumas operações de cadeia de caracteres básicas. Para ambos os casos, clique no símbolo de reticências (**...**) à direita de **Regra**.

Para definir uma nova regra, na área **Regras**, crie uma nova regra. Por exemplo, digite **desenvolvimento** em **Regras** e, em seguida, digite **R&amp;D** em **Ações**. Quando terminar, salve sua nova categoria.

A imagem a seguir mostra um exemplo de regras criadas para uma nova categoria chamada **Work-Load**:

![Categoria de exemplo](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Relatórios e fontes de marca

Os dados de marca que você visualiza nos relatórios do Cloudyn originam-se em três lugares:

- APIs de recursos do provedor de nuvem
- APIs de cobrança do provedor de nuvem
- Marcas criadas manualmente das seguintes fontes:
    - Marcas da entidade Cloudyn - metadados definidos pelo usuário aplicados às entidades Cloudyn
    - Gerente de Categoria - uma ferramenta de limpeza de dados que cria novas marcas com base em regras que são aplicadas a marcas existentes

Para visualizar as marcas do provedor de nuvem nos relatórios de custos do Cloudyn, você deve criar um modelo personalizado de alocação de custos utilizando a Alocação de Custo 360. Para fazer isso, acesse **Custos** > **Gerenciamento de Custos** > **Alocação de Custos 360**, selecione as marcas desejadas e, em seguida, defina regras para tratar os custos não marcados. Em seguida, crie um novo modelo de custo. Posteriormente, você poderá exibir relatórios na Análise de Alocação de Custos para visualizar, filtrar e classificar as marcas de recursos do Azure.

As marcas de recursos do Azure aparecem somente nos relatórios de **Análise de Alocação de Custos**.

As marcas de cobrança do fornecedor de nuvem aparecem em todos os relatórios de custos.

As marcas de entidade do Cloudyn e as marcas que você criou manualmente aparecem em todos os relatórios de custos.


## <a name="create-showback-and-chargeback-reports"></a>Criar relatórios de análise e de estorno

O método que as organizações usam para executar análise e estorno varia consideravelmente. No entanto, você pode usar qualquer um dos painéis e relatórios no portal do Cloudyn como a base para qualquer finalidade. Você pode fornecer acesso de usuário a qualquer pessoa na sua organização para que eles possam exibir painéis e relatórios sob demanda. Todos os relatórios de análise de custo dão suporte a análise porque eles mostram aos usuários os recursos consumidos por eles. Além disso, eles permitem que os usuários detalhem dados de custo ou de uso específicos para o grupo deles dentro da organização.

Para exibir os resultados de alocação de custos, abra o relatório de análise de custo e selecione o modelo de custo que você criou. Em seguida, adicione um agrupamento por uma ou mais das marcas selecionadas no modelo de custo.

![Relatório de análise de custo](./media/tutorial-manage-costs/cost-analysis.png)

Você pode facilmente criar e salvar relatórios que se concentram em serviços específicos consumidos por grupos específicos. Por exemplo, você pode ter um departamento que faz uso extensivo de VMs do Azure. Você pode criar um relatório que é filtrado por VMs do Azure para mostrar o consumo e os custos.

Se você precisar fornecer dados de instantâneo para outras equipes, você poderá exportar qualquer relatório no formato PDF ou CSV.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Use marcas personalizadas para alocar os custos.
> * Crie relatórios de análise e de estorno.



Para saber mais sobre como começar com a Cloudyn e como usar seus recursos, prossiga para a documentação da Cloudyn.

> [!div class="nextstepaction"]
> [Documentação da Cloudyn](https://support.cloudyn.com/hc/)
