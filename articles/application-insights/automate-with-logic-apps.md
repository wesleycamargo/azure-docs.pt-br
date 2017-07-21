---
title: "Automatizar os processos do Azure Application Insights com um Aplicativo Lógico do Azure."
description: "Saiba como você pode automatizar rapidamente os processos repetíveis, adicionando o conector do Application Insights ao Aplicativo Lógico do Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>Automatizar os processos do Application Insights com um Aplicativo Lógico do Azure

Você está executando repetidamente as mesmas consultas nos dados telemétricos para verificar se o serviço está funcionando corretamente? Deseja automatizar essas consultas para descobrir tendências e anomalias e criar seus próprios fluxos de trabalho para elas? Bem, o conector do Application Insights (versão prévia) para Aplicativos Lógicos do Azure é exatamente o que você precisa!
Com essa integração, diversos processos agora podem ser automatizados sem a necessidade de escrever nenhuma linha de código. É possível criar seu Aplicativo Lógico com o conector do Application Insights para automatizar rapidamente qualquer processo do Application Insights. Adicione também outras ações. Aplicativos Lógicos disponibilizam centenas de ações. Por exemplo, você pode enviar automaticamente uma notificação por email ou criar um bug no Visual Studio Team Services como parte do seu Aplicativo Lógico. Além disso, é possível utilizar um dos muitos [modelos](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponíveis para Aplicativos Lógicos. Esses modelos aceleram o processo de criação do seu Aplicativo Lógico. 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>Tutorial para criar um Aplicativo Lógico do Azure para o Application Insights

Neste tutorial, você irá aprender como criar um Aplicativo Lógico do Azure que utliza o algoritmo de cluster automático do Analytics para agrupar atributos nos dados de um aplicativo Web. O fluxo envia os resultados por email automaticamente. Esse é apenas um exemplo de como é possível utilizar a Análise do Application Insights e os Aplicativos Lógicos do Azure em conjunto. 

### <a name="step-1-create-a-logic-app"></a>Etapa 1: criar um aplicativo lógico
1. Entre em:  https://portal.azure.com.
2. Crie um novo Aplicativo Lógico no menu Novo/Web + Móvel.

![Tela novo Aplicativo Lógico](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Etapa 2: criar um gatilho para seu Aplicativo Lógico
1.  No Designer do Aplicativo Lógico, em Iniciar com um gatilho comum, escolha Recorrência.
2.  Defina a Frequência para o Dia com um intervalo de 1.

![Diálogo do gatilho do Aplicativo Lógico](./media/automate-with-logic-apps/logicapp2.png)

![Diálogo da frequência do Aplicativo Lógico](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>Etapa 3: adicionar uma ação do Azure Application Insights
1. Clique em **Nova etapa** e, em seguida, em **Adicionar uma ação**.
2. Pesquise por Azure Application Insights.
3. Clique em Versão Prévia do Azure Application Insights – Visualizar consulta do Analytics.

![Tela Executar consulta do Analytics](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: conectar-se a um recurso do Application Insights

**Pré-requisito**

Você precisa de uma ID do Aplicativo e uma Chave de API do recurso para concluir esta etapa. Recupere-as no portal do Azure, conforme mostrado no seguinte diagrama:

![ID do Aplicativo no portal do Azure](./media/automate-with-logic-apps/appid.png) 

- Forneça um nome para a conexão, junto com a ID do Aplicativo e a Chave de API.

![Tela de conexão do Flow](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: especificar o tipo de consulta do Analytics e o tipo de gráfico
Este exemplo seleciona as solicitações com falha no último dia e correlaciona-as com as exceções que ocorreram como parte da operação. O Analytics correlaciona com base no identificador operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo de cluster automático. Ao criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao fluxo.

- Adicione a consulta do Analytics a seguir e selecione o tipo de gráfico de tabela HTML. 

```
requests
| where timestamp > ago(1d)
| where success == "False"
| project name, operation_Id
| join ( exceptions
    | project problemId, outerMessage, operation_Id
) on operation_Id
| evaluate autocluster()
```
![Tela de configuração de consulta do Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>Etapa 6: configurar o aplicativo para enviar email

1. Clique em **Nova etapa** e, depois, selecione **Adicionar uma ação**.
2. Pesquise por Office 365 Outlook.
3. Clique em Office 365 Outlook – Enviar um email.
![Seleção do Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. Na ação de email, adicione os seguintes dados:
 - Especifique o endereço de email do destinatário
 - Indique o assunto do email
 - Coloque o cursor no campo **Corpo** e, no menu de conteúdo dinâmico aberto à direita, selecione **Corpo**.
 - Clique em **Mostrar opções avançadas**.

 ![Configuração do Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. No menu de conteúdo dinâmico, faça o seguinte:
- Selecione **Nome do Anexo**
- Selecione **Conteúdo do Anexo**
- Selecione **Sim** no campo **É HTML**

![Tela de configuração de email do Office 365](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>Etapa 7: salvar e testar seu Aplicativo Lógico
1. Clique em **Salvar** para salvar as alterações.
1. Você pode aguardar o gatilho executar o Aplicativo Lógico ou pode executá-lo imediatamente escolhendo **Executar**.

![Tela de criação do Aplicativo Lógico](./media/automate-with-logic-apps/step7.png)

Quando o Aplicativo Lógico for executado, os destinatários especificados na lista de emails receberão um email semelhante ao seguinte:

![Email do Aplicativo Lógico](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a criação de [Consultas do Analytics](app-insights-analytics-using.md).
- Saiba mais sobre [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->






