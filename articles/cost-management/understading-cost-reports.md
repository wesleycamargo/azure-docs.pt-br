---
title: "Noções básicas dos relatórios de custo no Gerenciamento de Custos do Azure | Microsoft Docs"
description: "Este artigo ajuda você a entender a estrutura básica e funções dos relatórios do Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 38c1313f42a58403e158cad9c2930b6541da5adc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="understanding-cost-reports"></a>Compreendendo os relatórios de custos

Este artigo ajuda você a entender a estrutura básica e funções dos relatórios do Cloudyn. A maioria dos relatórios do Cloudyn é intuitiva e tem uma aparência uniforme. Depois de ler este artigo, você estará pronto para usar todos os relatórios. Muitos recursos padrão estão disponíveis em vários relatórios, permitindo uma fácil navegação pelos relatórios. Os relatórios são personalizáveis, e você pode selecionar várias opções para calcular e exibir os resultados.

## <a name="report-fields-and-options"></a>Campos e opções do relatório

Confira aqui um exemplo do relatório de Custo ao Longo do Tempo. A maioria dos relatórios do Cloudyn tem um layout semelhante.

![exemplo de relatório](./media/understanding-cost-reports/sample-report.png)

Cada área numerada na imagem anterior é descrita detalhadamente nas seguintes informações:

1. **Intervalo de Datas**

    Use a lista de Intervalo de Datas para definir um intervalo de tempo do relatório usando uma predefinição ou personalização.
2. **Filtro Salvo**

    Use a lista Filtro Salvo para salvar os grupos atuais e os filtros que são aplicados ao relatório. Os filtros salvos estão disponíveis em relatórios de custo e desempenho, incluindo:

      - Análise de Custo
      - Alocação
      - Gerenciamento de Ativos
      - Otimização

  Digite um nome de filtro e clique em **Salvar**.

3. **Marcas**

    Use a área Marcas para agrupar por categorias de marca. As marcas listadas no menu são marcas de departamento do Azure ou do centro de custo, ou são as marcas de entidade e de assinatura de custo do Cloudyn. Selecione marcas para filtrar resultados. Você também pode digitar um nome de marca (palavra-chave) para filtrar os resultados.

    ![opções de seleção](./media/understanding-cost-reports/select-options.png)

    Clique em **Adicionar** para adicionar um novo filtro.

    ![adicionar filtro](./media/understanding-cost-reports/add-filter.png)

    O agrupamento ou filtragem de marcas não estão relacionados aos recursos do Azure ou a marcas de grupo de recursos.

    Filtragem e agrupamento de marca de alocação de custos estão disponíveis como uma opção do menu **Grupos**.

4. **Grupos nos relatórios**

    Usar grupos em relatórios de Análise de Custo para mostrar categorias padrão detalhadas dos dados de cobrança em seu relatório.  No entanto, os grupos nos relatórios de Alocação de Custo mostram categorias baseadas em marcas de exibição. Categorias baseadas em marcas são definidas no modelo de alocação de custo e em categorias detalhadas padrão dos dados de cobrança.

    ![agrupar marcas](./media/understanding-cost-reports/groups-tags01.png)

    ![agrupar marcas](./media/understanding-cost-reports/groups-tags02.png)

    Nos relatórios de Alocação de Custo, os grupos em categorias de grupo baseadas em marcas podem incluir:
      - Marcas
      - marcas do grupo de recursos
      - Marcas de entidade de custo do Cloudyn
      - Categorias de marca de assinatura para fins de alocação de custo

  Os exemplos podem incluir:
     - Centro de custo
     - department
     - Aplicativo
     - Ambiente
     - Código de custo

5. **Filtros**

    Use filtros individuais ou de seleção múltipla para definir intervalos como valores selecionados. Para definir um filtro, clique em **Adicionar** e, depois, selecione as categorias e os valores de filtro.

6. **Modelo de Custo**

    Use o Modelo de Custo para selecionar um modelo de custo que você criou anteriormente com a Alocação de Custo 360. Talvez você tenha vários modelos de custo do Cloudyn, dependendo de seus requisitos de alocação de custo. Algumas de suas equipes organizacionais podem ter requisitos de alocação de custo diferentes das outras. Cada equipe pode ter seu próprio modelo de custo dedicado.

    Para saber mais sobre como criar uma definição de modelo de alocação de custo, confira [Usar marcas personalizadas para alocar os custos](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortização**

    Use os relatórios de Amortização na Alocação de Custo para exibir taxas de serviço sem base no uso ou custos de pagamento único e distribua uniformemente os custos ao longo de sua vida útil. Entre os exemplos de taxas únicas estão:
    - Taxas de suporte anual
    - Taxas de componentes de segurança anuais
    - Taxas de compra de Instâncias Reservadas
    - Alguns itens do Azure Marketplace.

  Em Amortização, selecione **Custo amortizado** ou **Custo Real**.

8. **Resolução**

    Use Resolução para selecionar a resolução de tempo dentro do intervalo de datas selecionado. Sua resolução de tempo determina como as unidades são exibidas no relatório, e pode ser:
    - Diário
    - Semanal
    - Mensal
    - Trimestral
    - Anual

9. **Regras de alocação**

    Use as Regras de Alocação para aplicar ou desabilitar o recálculo do custo de alocação de custo. Você pode habilitar ou desabilitar o recálculo de alocação de custo para os dados de cobrança. O recálculo se aplica às categorias selecionadas no relatório. Isso permite que você avalie o impacto do recálculo de alocação de custo em relação aos dados brutos de cobrança.

10. **Não categorizado**

    Use Não categorizado para incluir ou excluir os custos não categorizados no relatório.

11. **Mostrar/ocultar campos**

    A opção Mostrar/ocultar não tem qualquer efeito nos relatórios.

12.   **Formatos de exibição**

    Use os Formatos de exibição para selecionar vários modos de exibição de tabela ou gráfico.

    ![formatos de exibição](./media/understanding-cost-reports/display-formats.png)

13. **Várias cores**

    Use Várias cores para definir a cor dos gráficos no relatório.

14. **Ações**

    Use Ações para salvar, exportar ou agendar do relatório.

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro tutorial de Gerenciamento de Custos, leia-o em [Examinar o uso e os custos](tutorial-review-usage.md).
