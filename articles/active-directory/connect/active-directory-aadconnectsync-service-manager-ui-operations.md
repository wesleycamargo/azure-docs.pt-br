---
title: "Sincronização do Azure AD Connect: interface do usuário do Synchronization Service Manager | Microsoft Docs"
description: "Entenda como usar a guia Operações no Synchronization Service Manager para o Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 610dab0af17f927d86b677f647acd0dfe2569583
ms.openlocfilehash: 53b98aaf67b874b0af7d0e94e29bcbe23fc6fc5b


---
# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronização do Azure AD Connect: Synchronization Service Manager

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

A guia Operações mostra os resultados das operações mais recentes. Essa guia é o segredo para entender e solucionar problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Entender as informações visíveis na guia Operações
A metade superior mostra todas as execuções em ordem crônica. Por padrão, as operações de log mantêm informações sobre os últimos sete dias, mas essa configuração pode ser alterada com o [agendador](active-directory-aadconnectsync-feature-scheduler.md). Você deseja procurar qualquer execução que não mostre um status bem-sucedido. É possível alterar a classificação clicando nos cabeçalhos.

A coluna **Status** traz as informações mais importantes e mostra o problema mais grave de uma execução. Aqui está um resumo rápido dos status mais comuns em ordem de prioridade para investigação (em que * indica várias cadeias de caracteres de erro possíveis).

| Status | Comentário |
| --- | --- |
| stopped-* |Não foi possível concluir a execução. Por exemplo, se o sistema remoto está inoperante e não pode ser contatado. |
| stopped-error-limit |Há mais de 5.000 erros. A execução foi interrompida automaticamente devido ao grande número de erros. |
| completed-\*-errors |A execução foi concluída, mas há erros (menos de 5.000) que devem ser investigados. |
| completed-\*-warnings |A execução foi concluída, mas alguns dados não estão no estado esperado. Se houver erros, geralmente, essa mensagem indicará apenas um sintoma. Até que tenha resolvido os erros, você não deverá investigar os avisos. |
| sucesso |Nenhum problema. |

Quando você seleciona uma linha, a parte inferior é atualizada para mostrar os detalhes dessa execução. À extrema esquerda da parte inferior, talvez você veja uma lista indicando **Etapa nº**. Essa lista só será exibida se você tiver vários domínios na floresta, em que cada domínio é representado por uma etapa. O nome de domínio pode ser encontrado sob o título **Partição**. Em **Estatísticas de Sincronização**, é possível encontrar mais informações sobre o número de alterações que foram processadas. É possível clicar nos links para obter uma lista dos objetos alterados. Se você tiver objetos com erros, eles aparecerão em **erros de sincronização**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Solucionar problemas de erros na guia Operações
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Quando houver erros, o objeto com erro e o próprio erro serão exibidos como links que fornecerão mais informações.

Comece clicando na cadeia de caracteres de erro (**sync-rule-error-function-triggered** na imagem). Primeiro, você vê uma visão geral do objeto. Para ver o erro real, clique no botão **Rastreamento de Pilha**. Esse rastreamento fornece informações de nível de depuração sobre o erro.

**DICA:** clique com o botão direito do mouse na caixa **informações da pilha de chamadas**, escolha **selecionar tudo** e **copiar**. Em seguida, é possível copiar a pilha e examinar o erro em seu editor favorito, como o Bloco de Notas.

* Se o erro for proveniente de **SyncRulesEngine**, as informações da pilha de chamadas primeiro terão uma lista de todos os atributos no objeto. Role para baixo até ver o cabeçalho **InnerException =>**.  
  ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
  A linha posterior mostra o erro. Na figura acima, o erro é proveniente de uma Regra de Sincronização personalizada criada pela Fabrikam.

Se o erro em si não fornecer informações suficientes, será o momento de examinar os próprios dados. É possível clicar no link com o identificador de objeto e [Seguir um objeto e seus dados por meio do sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).



<!--HONumber=Jan17_HO2-->


