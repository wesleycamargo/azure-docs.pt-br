---
title: "Usar a instrução Switch em Aplicativos Lógicos do Azure | Microsoft Docs"
description: "A instrução switch permite que você execute com facilidade ações diferentes com base no valor de uma expressão em Aplicativos Lógicos"
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd


---
# <a name="use-switch-statement-in-logic-apps"></a>Usar a instrução switch em Aplicativos Lógicos
Ao criar um fluxo de trabalho, você geralmente precisa executar ações diferentes com base no valor de um objeto ou uma expressão. Por exemplo, convém que seu Aplicativo Lógico se comporte de maneira diferente com base no código de status de uma solicitação HTTP, ou a opção selecionada de um email de aprovação.

Esses cenários podem ser obtidos usando uma instrução switch: o Aplicativo Lógico avalia um token ou uma expressão e escolhe o caso com o mesmo valor para executar ações. Apenas um caso deve corresponder à instrução switch.

 > [!TIP]
 > Como todas as linguagens de programação, a instrução switch só oferece suporte a operadores de igualdade. Se você precisar de outros operadores relacionais (por exemplo, maior que), use uma instrução de condição.
 >
 > Para garantir o comportamento de execução determinística, casos devem conter um valor exclusivo e estático em vez de tokens dinâmicos ou uma expressão.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura ativa do Azure.
    - Se você não tiver uma assinatura ativa do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/), ou tente [Aplicativos Lógicos gratuitamente](https://tryappservice.azure.com/).
- [Conhecimento básico de Aplicativos Lógicos](logic-apps-what-are-logic-apps.md).

## <a name="working-with-switch-statement-in-designer"></a>Trabalhando com a instrução switch no designer
Para demonstrar o uso da instrução switch, vamos criar um aplicativo lógico que monitora os arquivos carregados para a pasta de recados. O aplicativo lógico enviará um email de aprovação para determinar se devem ser transferido para o SharePoint. Nós usaremos a instrução switch para executar ações diferentes dependendo do aprovador de valor selecionado.

1. Iniciar, crie um aplicativo lógico e selecione **Dropbox - quando um arquivo é criado** disparador.

 ![Use Dropbox - quando um arquivo é criado um gatilho](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Acompanhar o disparador com um **Outlook.com - enviar email de aprovação** ação.

 > [!TIP]
 > O Aplicativos Lógicos também oferece suporte ao cenário de email de aprovação de uma conta do Outlook do Office 365.

 - Caso não tenha uma conexão existente, será solicitado que você crie uma.
 - Preencher campos obrigatórios, nós enviaremos um email para approvers@contoso.com.
 - Em *User Options*, digite `Approve, Reject`.

 ![Configurar conexão](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Adicione uma instrução switch.
 - Selecione **+ Nova Etapa**, **... Mais**, **adicionar uma instrução switch**.
 - Queremos selecionar o que deve ser executado com base em `SelectedOptions` de saída do *enviar email de aprovação* ação, você pode encontrar no **adicionar conteúdo dinâmico** seletor.
 - Use *caso 1* para controlar quando o usuário selecionou `Approve`.
    - Se aprovada, copie o arquivo original no SharePoint Online com **SharePoint Online - criar arquivo** ação.
    - Adicione outra ação dentro do gabinete para notificar os usuários que um novo arquivo está disponível no SharePoint.
 - Adicionar outro caso para controlar quando o usuário selecionou `Reject`.
    - Se rejeitada, envie um email de notificação informando que o arquivo será rejeitado e nenhuma ação adicional é necessária a outros aprovadores.
 - Sabemos `SelectedOptions` somente duas forneceu opções, *padrão* caso pode ser deixado em branco.

 ![Instrução switch](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > Instrução switch precisa de pelo menos um caso no adicional para o caso padrão.

4. Após a instrução switch, exclua o arquivo original carregado no recados com **Dropbox - excluir arquivo** ação.

5. Salve o Aplicativo Lógico e teste-o carregando um arquivo no Dropbox. Você deve receber um email de aprovação logo após, selecione uma opção e observar o comportamento.
 > [!TIP]
 > Confira como [monitorar seus Aplicativos Lógicos](logic-apps-monitor-your-logic-apps.md).

## <a name="understanding-code-behind"></a>Compreender o código por trás
Agora você criou com êxito um aplicativo lógico usando a instrução switch. Vamos examinar o código por trás da seguinte maneira.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

`"Switch"`é o nome da instrução switch, ele pode ser renomeado para facilitar a leitura. `"type": "Switch"`indica que a ação é uma instrução switch. `"expression"`, nesse caso, a opção selecionada do usuário, é avaliado em cada caso declarado posteriormente na definição de. `"cases"`pode conter qualquer número de casos, e se nenhum dos casos corresponde à expressão de comutador, ações no `"default"` é executado.

Pode haver qualquer número de casos dentro de `"cases"`. Para cada caso, `"Case 1"` é o nome do caso, ele pode ser renomeado para facilitar a leitura. `"case"`Especifica o rótulo case, que compara a expressão de comutador com, que deve ser um valor constante e exclusivo.  

## <a name="next-steps"></a>Próximas etapas
- Tente outros [recursos de Aplicativos Lógicos](logic-apps-use-logic-app-features.md).
- Saiba mais sobre [tratamento de exceções e de erros](logic-apps-exception-handling.md).
- Explore mais [os recursos de idioma do fluxo de trabalho](logic-apps-author-definitions.md).
- Deixe um comentário com suas perguntas ou [Conte-nos como podemos aprimorar os Aplicativos Lógicos](https://feedback.azure.com/forums/287593-logic-apps).


<!--HONumber=Feb17_HO2-->


