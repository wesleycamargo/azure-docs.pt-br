---
title: "Criar exibições para analisar dados no Log Analytics do Azure | Microsoft Docs"
description: "Ao usar Designer de Exibição no Log Analytics, é possível criar exibições personalizadas que são exibidas no Portal do Azure e conter uma variedade de visualizações de dados no espaço de trabalho do Log Analytics. Este artigo contém uma visão geral do Designer de Exibição e apresenta procedimentos para criar e editar exibições personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: 08d0e557f03f771901c9ac92fb080e74e5966452
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Criar exibições personalizadas usando o Designer de Exibição no Log Analytics
Ao usar o Designer de Exibição no [Azure Log Analytics](log-analytics-overview.md), é possível criar uma variedade de exibições personalizadas no Portal do Azure que podem ajudá-lo a visualizar dados no espaço de trabalho do Log Analytics. Este artigo apresenta uma visão geral do Designer de Exibição e procedimentos para criar e editar exibições personalizadas.

Para obter mais informações sobre o Designer de Exibição, consulte:

* [Referência de bloco](log-analytics-view-designer-tiles.md): fornece um guia de referência para as configurações de cada um dos blocos disponíveis em suas exibições personalizadas.
* [Referência da parte de visualização](log-analytics-view-designer-parts.md): fornece um guia de referência para as configurações das partes de visualização que estão disponíveis em nas exibições personalizadas.

>[!NOTE]
> Se o espaço de trabalho foi atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas em todas as exibições deverão ser gravadas na [nova linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856078). Todas as exibições que criadas antes da atualização do espaço de trabalho serão convertidas automaticamente.

## <a name="concepts"></a>Conceitos
Modos de exibição são exibidos na página **Visão geral** do seu espaço de trabalho do Log Analytics no portal do Azure. Os blocos em cada exibição personalizada são exibidos em ordem alfabética e os blocos para as soluções são instalados no mesmo espaço de trabalho.

![Página de visão geral](media/log-analytics-view-designer/overview-page.png)

As exibições criadas com o Designer de Exibição contêm os elementos descritos na tabela a seguir:

| Parte | DESCRIÇÃO |
|:--- |:--- |
| Blocos | São exibidos na página **Visão Geral** do espaço de trabalho do Log Analytics. Cada bloco exibe um resumo visual da exibição personalizada que representa. Cada tipo de bloco fornece uma visualização diferente de seus registros. Você seleciona um bloco para exibir uma exibição personalizada. |
| Exibição personalizada | Exibida quando você seleciona um bloco. Cada exibição contém uma ou mais partes de visualização. |
| Partes de visualização | Apresenta uma visualização de dados no espaço de trabalho do Log Analytics com base em uma ou mais [pesquisas de logs](log-analytics-log-searches.md). A maioria das partes inclui um cabeçalho, que fornece uma visualização de alto nível e uma lista que exibe os melhores resultados. Cada tipo de parte fornece uma visualização diferente dos registros no espaço de trabalho do Log Analytics. Você seleciona elementos na parte para executar uma pesquisa de log que fornece registros detalhados. |


## <a name="work-with-an-existing-view"></a>Trabalhar com um modo de exibição existente
As exibições que foram criadas com o Designer de Exibição exibem as opções a seguir:

![Menu de visão geral](media/log-analytics-view-designer/overview-menu.png)

As opções estão descritas na tabela a seguir:

| Opção | DESCRIÇÃO |
|:--|:--|
| Atualizar   | Atualiza a exibição com os dados mais recentes. | 
| Análise | Abre o [portal de Análise Avançada](log-analytics-log-search-portals.md#advanced-analytics-portal) para analisar dados com pesquisas de logs. |
| Filter    | Define um filtro de tempo para os dados incluídos na exibição. |
| Editar      | Abre a exibição no Designer de Exibição para editar o conteúdo e a configuração.  |
| Clone     | Cria uma nova exibição e abre no Designer de Exibição. O nome da nova exibição é o mesmo que o nome original, mas com *Copiar* acrescentado a ele. |


## <a name="create-a-new-view"></a>Criar um novo modo de exibição
É possível criar uma nova exibição no Designer de Exibição, selecionando o bloco **Designer de Exibição** na página **Visão Geral** do espaço de trabalho do Log Analytics.

![Bloco Designer de modo de exibição](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Trabalhar com o Designer de Exibição
O Designer de Exibição é utilizado para criar novas exibições ou editar as existentes. 

O Designer de Exibição tem três painéis: 
* **Design**: contém a exibição personalizada que você está criando ou editando. 
* **Controles**: contém os blocos e as partes que você adiciona ao painel **Design**. 
* **Propriedades**: exibe as propriedades dos blocos ou partes selecionadas.

![Criador de Modos de Exibição](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurar o bloco de exibição
Um modo de exibição personalizado pode ter apenas um único bloco. Para exibir o bloco atual ou selecionar um alternativo, selecione a guia **Bloco** no painel**Controle**. O painel **Propriedades** exibe as propriedades do bloco atual. 

Você pode configurar as propriedades do bloco de acordo com as informações na [Referência de bloco](log-analytics-view-designer-tiles.md) e, em seguida, clique em **Aplicar** para salvar as alterações.

### <a name="configure-the-visualization-parts"></a>Configurar as partes de visualização
Um modo de exibição pode incluir qualquer número de partes de visualização. Para adicionar partes a uma exibição, selecione a **Exibição** e, em seguida, selecione uma parte de visualização. O painel **Propriedades** exibe as propriedades da parte selecionada. 

Você pode configurar as propriedades da exibição, de acordo com as informações na [Referência da parte de visualização](log-analytics-view-designer-parts.md) e, em seguida, clique em **Aplicar** para salvar as alterações.

As exibições têm apenas uma linha de partes de visualização. É possível reorganizar as partes existentes, arrastando-as para um novo local.

Além disso, é possível remover uma parte de visualização da exibição, selecionando **X** no canto superior direito da parte.


### <a name="menu-options"></a>Opções de menu
As opções para trabalhar com exibições no modo de edição são descritas na tabela a seguir.

![Editar menu](media/log-analytics-view-designer/edit-menu.png)

| Opção | DESCRIÇÃO |
|:--|:--|
| Salvar        | Salva as alterações e fecha a exibição. |
| Cancelar      | Descarta as alterações e fecha a exibição. |
| Excluir Exibição | Exclui a exibição. |
| Exportação      | Exporta a exibição para um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que você poderá importar para outro espaço de trabalho. O nome do arquivo é o nome da exibição e tem uma extensão *omsview*. |
| Importar      | Importa o arquivo *omsview* que foi exportado de outro espaço de trabalho. Essa ação substitui a configuração da exibição existente. |
| Clone       | Cria uma nova exibição e abre no Designer de Exibição. O nome da nova exibição é o mesmo que o nome original, mas com *Copiar* acrescentado a ele. |
| Publicar     | Exporta a exibição para um arquivo JSON que você poderá inserir em uma [Solução de gerenciamento](../operations-management-suite/operations-management-suite-solutions-resources-views.md). O nome do arquivo é o mesmo que o nome da exibição, mas com uma extensão *json*. Um segundo arquivo, criado com uma extensão  *resjson* , inclui valores para os recursos que estão definidos no arquivo JSON.

## <a name="next-steps"></a>Próximas etapas
* Adicione [Blocos](log-analytics-view-designer-tiles.md) ao modo de exibição personalizado.
* Adicione as [Partes de visualização](log-analytics-view-designer-parts.md) à sua exibição personalizada.
