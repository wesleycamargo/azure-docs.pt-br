---
title: "Criar exibições para analisar dados no Log Analytics do Azure | Microsoft Docs"
description: "O Designer de modos de exibição do Log Analytics permite que você crie Exibições personalizadas que são exibidas no portal do Azure e que contêm diferentes visualizações de dados no espaço de trabalho do Log Analytics. Este artigo contém uma visão geral do Designer de modos de exibição e dos procedimentos para criar e editar exibições personalizadas."
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
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Use o Designer de modos de exibição para criar exibições personalizadas no Log Analytics
O Designer de modos de exibição do [Log Analytics](log-analytics-overview.md) permite que você crie modos de exibição personalizados no portal do Azure que contêm diferentes visualizações de dados no seu espaço de trabalho do Log Analytics. Este artigo contém uma visão geral do Designer de modos de exibição e dos procedimentos para criar e editar exibições personalizadas.

Outros artigos disponíveis para o Designer de Modos de Exibição são:

* [Referência de bloco](log-analytics-view-designer-tiles.md) – Referência das configurações de cada um dos blocos disponíveis para uso nas exibições personalizadas.
* [Referência de parte de visualização](log-analytics-view-designer-parts.md) – Referência das configurações para cada um dos blocos disponíveis para uso nas exibições personalizadas.

>[!NOTE]
> Se o seu espaço de trabalho tiver sido atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), consultas em todas as exibições deverão ser gravadas na [nova linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas as exibições que foram criadas antes de atualizar o espaço de trabalho serão automaticamente convertidas.

## <a name="concepts"></a>Conceitos
Modos de exibição são exibidos na página **Visão geral** do seu espaço de trabalho do Log Analytics no portal do Azure.  O bloco para cada modo de exibição personalizado será exibido em ordem alfabética com os blocos para as soluções instalados mesmo espaço de trabalho.

![Página de visão geral](media/log-analytics-view-designer/overview-page.png)

Exibições criadas com o Designer de modo de exibição contêm os elementos na tabela a seguir.

| Parte | DESCRIÇÃO |
|:--- |:--- |
| Bloco |Exibido na página Visão Geral de seu espaço de trabalho do Log Analytics.  Inclui um resumo visual das informações contidas no modo de exibição personalizado.  Diferentes tipos de bloco fornecem visualizações diferentes de registros.  Clique no bloco para abrir o modo de exibição personalizado. |
| Exibição personalizada |Exibido quando o usuário clica no bloco.  Contém uma ou mais partes da visualização. |
| Partes da visualização |Visualização de dados no espaço de trabalho do Log Analytics com base em uma ou mais [pesquisas de log](log-analytics-log-searches.md).  A maioria das partes incluirá um cabeçalho que fornece uma visualização de alto nível e uma lista dos principais resultados.  Diferentes tipos de partes fornecem visualizações diferentes de registros no espaço de trabalho do Log Analytics.  Clique em elementos na parte para realizar uma pesquisa de log fornecendo registros detalhados. |


## <a name="work-with-an-existing-view"></a>Trabalhar com um modo de exibição existente
Quando você abre um modo de exibição que foi criado com o Designer de exibição, ele terá um menu com as opções na tabela a seguir.

![Menu de visão geral](media/log-analytics-view-designer/overview-menu.png)


| Opção | DESCRIÇÃO |
|:--|:--|
| Atualizar   | Atualize a exibição com os dados mais recentes. | 
| Análise | Abra o [portal Advanced Analytics](log-analytics-log-search-portals.md#advanced-analytics-portal) para analisar os dados com pesquisas de log. ( log-Analytics-log-Search-Portals.MD#Advanced-Analytics-portal). |
| Filter    | Defina um filtro de tempo para os dados incluídos na exibição. |
| Editar      | Abra o modo de exibição no Designer de exibição para editar o conteúdo e a configuração.   |
| Clone     | Criar uma nova exibição e abri-lo no Designer de exibição.  O novo modo de exibição tem o mesmo nome do original com "Copiar" acrescentado ao final. |


## <a name="create-a-new-view"></a>Criar um novo modo de exibição
Criar uma nova exibição no **View Designer** clicando no bloco do Designer de exibição da página de visão geral do seu espaço de trabalho do Log Analytics no portal do Azure.

![Bloco Designer de modo de exibição](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>Trabalhando com o Designer de modo de exibição
Você trabalhará com o Designer de modo de exibição quer você esteja criando um novo modo de exibição ou editando um existente.  

O Designer de modo de exibição tem três painéis.  O painel **Design** contém o modo de exibição personalizado que você está criando ou editando.  Você adiciona blocos e partes do painel **Controle** ao painel **Design**.  O painel **Propriedades** exibirá as propriedades do bloco ou a parte selecionada.

![Criador de Modos de Exibição](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurar bloco de modo de exibição
Um modo de exibição personalizado pode ter apenas um único bloco.  Selecione a guia **Bloco** no painel **Controle** para exibir o bloco atual ou selecionar um alternativo.  O painel **Propriedades** exibirá as propriedades do bloco atual.  Configure as propriedades de bloco de acordo com as informações detalhadas na [Referência de Bloco](log-analytics-view-designer-tiles.md) e clique em **Aplicar** para salvar as alterações.

### <a name="configure-visualization-parts"></a>Configurar partes da visualização
Um modo de exibição pode incluir qualquer número de partes de visualização.  Selecione a guia **Modo de Exibição** e, em seguida, uma parte da visualização para adicionar ao modo de exibição.  O painel **Propriedades** exibirá as propriedades da parte selecionada.  Configure as propriedades do modo de exibição de acordo com as informações detalhadas na [Referência da parte de visualização](log-analytics-view-designer-parts.md) e clique em **Aplicar** para salvar as alterações.

Os modos de exibição têm apenas uma linha de partes da visualização.  Reorganize as partes existentes em um modo de exibição clicando e arrastando-as para um novo local.

Você pode remover uma parte da visualização do modo de exibição clicando no botão **X** no canto superior direito da parte.


### <a name="menu-options"></a>Opções de menu
Quando você estiver trabalhando com um modo de exibição no modo de edição, você tem as opções de menu na tabela a seguir.

![Editar menu](media/log-analytics-view-designer/edit-menu.png)

| Opção | DESCRIÇÃO |
|:--|:--|
| Salvar        | Salvar suas alterações e fechar o modo de exibição. |
| Cancelar      | Descartar suas alterações e fechar o modo de exibição. |
| Excluir Exibição | Excluir a exibição. |
| Exportação      | Exportar o modo de exibição para um [modelo do Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que você pode importar para outro espaço de trabalho.  O nome do arquivo será o nome do modo de exibição com a extensão *omsview*. |
| Importar      | Importar um arquivo *omsview* exportado de outro espaço de trabalho.  Isso substituirá a configuração do modo de exibição existente. |
| Clone       | Criar uma nova exibição e abri-lo no Designer de exibição.  O novo modo de exibição tem o mesmo nome do original com "Copiar" acrescentado ao final. |
| Publicar     | Exportar o modo de exibição para um arquivo JSON que pode ser inserido em uma [Solução de gerenciamento](../operations-management-suite/operations-management-suite-solutions-resources-views.md).  O nome do arquivo será o nome do modo de exibição com a extensão *json*. Um segundo arquivo é criado com a extensão *resjson* que incluem valores referentes a recursos definidos no arquivo JSON.

## <a name="next-steps"></a>Próximas etapas
* Adicione [Blocos](log-analytics-view-designer-tiles.md) ao modo de exibição personalizado.
* Adicione [Partes da visualização](log-analytics-view-designer-parts.md) ao modo de exibição personalizado.
