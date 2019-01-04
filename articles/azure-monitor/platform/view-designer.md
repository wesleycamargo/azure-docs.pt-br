---
title: Criar exibições para analisar dados no Log Analytics do Azure | Microsoft Docs
description: Ao usar Designer de Exibição no Log Analytics, é possível criar exibições personalizadas que são exibidas no Portal do Azure e conter uma variedade de visualizações de dados no workspace do Log Analytics. Este artigo contém uma visão geral do Designer de Exibição e apresenta procedimentos para criar e editar exibições personalizadas.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: ec56e21a989fb0e8db7b8bafb1357c6ed64eae75
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192236"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Criar exibições personalizadas usando o Designer de Exibição no Log Analytics
Ao usar o Designer de Exibição no [Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md), é possível criar uma variedade de exibições personalizadas no Portal do Azure que podem ajudá-lo a visualizar dados no workspace do Log Analytics. Este artigo apresenta uma visão geral do Designer de Exibição e procedimentos para criar e editar exibições personalizadas.

Para obter mais informações sobre o Designer de Exibição, consulte:

* [Referência de bloco](view-designer-tiles.md): Fornece um guia de referência das configurações de cada um dos blocos disponíveis nas exibições personalizadas.
* [Referência de parte da visualização](view-designer-parts.md): Fornece um guia de referência das configurações das partes de visualização disponíveis nas exibições personalizadas.


## <a name="concepts"></a>Conceitos
Modos de exibição são exibidos na página **Visão geral** do seu workspace do Log Analytics no portal do Azure. Os blocos em cada exibição personalizada são exibidos em ordem alfabética e os blocos para as soluções são instalados no mesmo workspace.

![Página de visão geral](media/view-designer/overview-page.png)

As exibições criadas com o Designer de Exibição contêm os elementos descritos na tabela a seguir:

| Parte | DESCRIÇÃO |
|:--- |:--- |
| Blocos | São exibidos na página **Visão Geral** do workspace do Log Analytics. Cada bloco exibe um resumo visual da exibição personalizada que representa. Cada tipo de bloco fornece uma visualização diferente de seus registros. Você seleciona um bloco para exibir uma exibição personalizada. |
| Exibição personalizada | Exibida quando você seleciona um bloco. Cada exibição contém uma ou mais partes de visualização. |
| Partes de visualização | Apresenta uma visualização de dados no workspace do Log Analytics com base em uma ou mais [pesquisas de logs](../../azure-monitor/log-query/log-query-overview.md). A maioria das partes inclui um cabeçalho, que fornece uma visualização de alto nível e uma lista que exibe os melhores resultados. Cada tipo de parte fornece uma visualização diferente dos registros no workspace do Log Analytics. Você seleciona elementos na parte para executar uma pesquisa de log que fornece registros detalhados. |


## <a name="work-with-an-existing-view"></a>Trabalhar com um modo de exibição existente
As exibições que foram criadas com o Designer de Exibição exibem as opções a seguir:

![Menu de visão geral](media/view-designer/overview-menu.png)

As opções estão descritas na tabela a seguir:

| Opção | DESCRIÇÃO |
|:--|:--|
| Atualizar   | Atualiza a exibição com os dados mais recentes. | 
| Análise | Abre o [portal do Advanced Analytics](../../azure-monitor/log-query/portals.md) para analisar dados com consultas de log. |
| Editar       | Abre a exibição no Designer de Exibição para editar o conteúdo e a configuração.  |
| Clone      | Cria uma nova exibição e abre no Designer de Exibição. O nome da nova exibição é o mesmo que o nome original, mas com *Copiar* acrescentado a ele. |
| Intervalo de datas | Define um filtro de data e tempo para os dados incluídos na exibição. Este intervalo de datas é aplicado antes de quaisquer intervalos de datas definidos nas consultas na exibição.  |
| +          | Define um filtro personalizado que é definido para a exibição. |


## <a name="create-a-new-view"></a>Criar um novo modo de exibição
É possível criar uma nova exibição no Designer de Exibição, selecionando **Designer de Exibição** no menu do seu workspace do Log Analytics.

![Bloco Designer de modo de exibição](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Trabalhar com o Designer de Exibição
O Designer de Exibição é utilizado para criar novas exibições ou editar as existentes. 

O Designer de Exibição tem três painéis: 
* **Design**: Contém a exibição personalizada que você está criando ou editando. 
* **Controles**: Contém os blocos e as partes que você adiciona ao painel **Design**. 
* **Propriedades**: Exibe as propriedades das partes ou dos blocos selecionados.

![Criador de Modos de Exibição](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurar o bloco de exibição
Um modo de exibição personalizado pode ter apenas um único bloco. Para exibir o bloco atual ou selecionar um alternativo, selecione a guia **Bloco** no painel**Controle**. O painel **Propriedades** exibe as propriedades do bloco atual. 

Você pode configurar as propriedades do bloco de acordo com as informações na [Referência de bloco](view-designer-tiles.md) e, em seguida, clique em **Aplicar** para salvar as alterações.

### <a name="configure-the-visualization-parts"></a>Configurar as partes de visualização
Um modo de exibição pode incluir qualquer número de partes de visualização. Para adicionar partes a uma exibição, selecione a **Exibição** e, em seguida, selecione uma parte de visualização. O painel **Propriedades** exibe as propriedades da parte selecionada. 

Você pode configurar as propriedades da exibição, de acordo com as informações na [Referência da parte de visualização](view-designer-parts.md) e, em seguida, clique em **Aplicar** para salvar as alterações.

As exibições têm apenas uma linha de partes de visualização. É possível reorganizar as partes existentes, arrastando-as para um novo local.

Além disso, é possível remover uma parte de visualização da exibição, selecionando **X** no canto superior direito da parte.


### <a name="menu-options"></a>Opções de menu
As opções para trabalhar com exibições no modo de edição são descritas na tabela a seguir.

![Editar menu](media/view-designer/edit-menu.png)

| Opção | DESCRIÇÃO |
|:--|:--|
| Salvar        | Salva as alterações e fecha a exibição. |
| Cancelar      | Descarta as alterações e fecha a exibição. |
| Excluir Exibição | Exclui a exibição. |
| Exportação      | Exporta a exibição para um [modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) que você poderá importar para outro workspace. O nome do arquivo é o nome da exibição e tem uma extensão *omsview*. |
| Importar      | Importa o arquivo *omsview* que foi exportado de outro workspace. Essa ação substitui a configuração da exibição existente. |
| Clone       | Cria uma nova exibição e abre no Designer de Exibição. O nome da nova exibição é o mesmo que o nome original, mas com *Copiar* acrescentado a ele. |

## <a name="next-steps"></a>Próximas etapas
* Adicione [Blocos](view-designer-tiles.md) ao modo de exibição personalizado.
* Adicione as [Partes de visualização](view-designer-parts.md) à sua exibição personalizada.
