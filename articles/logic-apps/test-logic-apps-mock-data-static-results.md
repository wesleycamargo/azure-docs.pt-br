---
title: Testar o aplicativo lógico com dados fictícios - aplicativos lógicos do Azure
description: Configurar resultados estáticos para aplicativos lógicos com dados fictícios de teste sem afetar os ambientes de produção
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 03/18/2019
ms.openlocfilehash: 0fbe56ceeeba71bcbb5ef358cd66de15e36508fc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165089"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testar o aplicativo lógico com dados fictícios, configurando resultados estáticos

Ao testar seus aplicativos lógicos, talvez você não esteja pronto para chamar, na verdade, ou acessar a aplicativos, serviços e sistemas por vários motivos. Normalmente, nesses cenários, você talvez precise executar caminhos de condição diferente, forçar os erros, fornecem corpos de resposta de mensagem específica ou mesmo tentar ignorar algumas etapas. Configurando resultados estáticos para uma ação em seu aplicativo lógico, você pode simular dados de saída de ação. Permitindo que os resultados estáticos em uma ação não executa a ação, mas retorna os dados fictícios em vez disso.

Por exemplo, se você configurar resultados de estáticos para o Outlook 365 enviar ação de email, o mecanismo dos aplicativos lógicos retorna apenas os dados fictícios que você especificou como resultados estáticos, em vez de chamar o Outlook e envie um email.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja configurar resultados estáticos

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurar resultados estáticos

1. Se você ainda não fez isso, nos [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativos lógicos.

1. Na ação onde você deseja configurar resultados estáticos, siga estas etapas: 

   1. No canto superior direito da ação, escolha as reticências (*...* ) botão e, em seguida, selecione **resultado estático**, por exemplo:

      ![Selecione "Resultado estático" > "Habilitar estáticos resultado"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Escolher **habilitar estático resultado**. Para as propriedades necessárias (*), especifique os valores de saída fictício para a resposta da ação que você deseja retornar.

      Por exemplo, aqui estão as propriedades necessárias para a ação de HTTP:

      | Propriedade | DESCRIÇÃO |
      |----------|-------------|
      | **Status** | O status da ação para retornar |
      | **Código de status** | O código de status específico para retornar |
      | **Cabeçalhos** | Para retornar o conteúdo do cabeçalho |
      |||

      ![Selecione "Habilitar resultado estático"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Para inserir os dados fictícios em formato JavaScript Object Notation (JSON), escolha **alternar para modo JSON** (![escolher "Alternar para modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Para propriedades opcionais, abra o **selecionar campos opcionais** lista e, em seguida, selecione as propriedades que você deseja simular.

      ![Selecione as propriedades opcionais](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando você estiver pronto para salvar, escolha **feito**.

   No canto superior direito da ação, a barra de título agora mostra um ícone de proveta de teste (![ícone para resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), que indica que você habilitou os resultados estáticos.

   ![Mostrar ícone habilitado resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Para localizar as execuções anteriores que usam dados fictícios, consulte [encontrar execuções que usam resultados estáticos](#find-runs-mock-data) mais adiante neste tópico.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Reutilizar saídas anteriores

Se seu aplicativo lógico tem um anteriores executar com saídas, que você pode reutilizar como saídas fictícias, você pode copiar e colar as saídas do que são executados.

1. Se você ainda não fez isso, nos [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativos lógicos.

1. No menu de principal do aplicativo lógico, selecione **visão geral**.

1. No **histórico de execuções** seção, selecione o aplicativo lógico executar você deseja.

1. Fluxo de trabalho do aplicativo lógico, localize e expanda a ação que tem as saídas que você deseja.

1. Escolha o **Mostrar saídas brutas** link.

1. Copie o objeto completo do objeto notação JSON (JavaScript) ou a subseção específica que você deseja usar, por exemplo, a seção de saídas ou até mesmo apenas a seção de cabeçalhos.

1. Siga as etapas para abrir o **resultado estático** caixa para a ação no [configurar resultados estáticos](#set-up-static-results).

1. Após o **resultado estático** caixa abre, escolha qualquer etapa:

   * Para colar um objeto JSON completo, escolha **alternar para modo JSON** (![escolher "Alternar para modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Escolha "Alternar para modo JSON" para o objeto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Para colar apenas uma seção JSON, ao lado do rótulo da seção, escolha **alternar para modo JSON** para essa seção, por exemplo:

     ![Escolha "Alternar para modo JSON" para saídas](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. No editor de JSON, cole o JSON copiado anteriormente.

   ![Modo JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quando tiver terminado, escolha **Concluído**. Ou, para retornar ao designer, escolha **modo do Editor de comutador** (![escolher "Modo do Editor de Switch"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Localizar execuções que usam resultados estáticos

Histórico de execuções do aplicativo lógico identifica as execuções em que as ações de usam resultados estáticos. Para localizar essas execuções, siga estas etapas:

1. No menu de principal do aplicativo lógico, selecione **visão geral**. 

1. No painel direito, sob **histórico de execuções**, localize o **resultados estático** coluna. 

   As execuções que incluem ações com resultados tem a **resultados estáticos** coluna definida como **habilitado**, por exemplo:

   ![Executar histórico - coluna estática de resultados](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Para exibir as ações que usam estáticos resultados, selecione a execução onde você deseja a **resultados estáticos** coluna é definida como **habilitado**.

   Ações que usam resultados estáticos mostram proveta o teste (![ícone para resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) ícone, por exemplo:

   ![Histórico - de execução de ações que usam resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Desabilitar estáticos resultados

Desativar resultados estáticos não jogar fora os valores de sua última configuração. Portanto, quando você ativa de resultados estáticos na próxima vez, você pode continuar usando seus valores anteriores.

1. Encontre a ação onde você deseja desabilitar estáticas saídas. No canto superior direito da ação, escolha o ícone de proveta de teste (![ícone para resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Desabilitar estáticos resultados](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Escolher **desabilitar estático resultado** > **feito**.

   ![Desabilitar estáticos resultados](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)