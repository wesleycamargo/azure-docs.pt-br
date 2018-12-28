---
title: Solucionar erros com recursos compartilhados do Automação do Azure
description: Aprenda a solucionar problemas com recursos compartilhados da Automação do Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848452"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Solucionar erros com recursos compartilhados

Este artigo discute soluções para resolver problemas que podem ser encontrados ao usar os recursos compartilhados na Automação do Azure.

## <a name="modules"></a>Módulos

### <a name="module-stuck-importing"></a>Cenário: Um Módulo está paralisado importando

#### <a name="issue"></a>Problema

Um módulo está paralisado no estado **Importando** quando você importa ou atualiza os módulos na automação do Azure.

#### <a name="cause"></a>Causa

A importação de módulos do PowerShell é um processo complexo de várias etapas. Este processo introduz a possibilidade de um módulo não importar corretamente. Se esse problema ocorrer, o módulo que você está importando pode estar paralisado em um estado transitório. Para saber mais sobre esse processo, consulte [importação de um módulo do PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver esse problema, você deve remover o módulo que está emperrado no estado **Importando** usando o cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Você pode, em seguida, tente importar novamente o módulo.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>Contas Executar como

### <a name="unable-create-update"></a>Cenário: Não é possível criar ou atualizar uma conta Executar como

#### <a name="issue"></a>Problema

Ao tentar criar ou atualizar uma conta Executar como, um erro semelhante à mensagem de erro a seguir é exibido:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Você não tem as permissões necessárias para criar ou atualizar a conta Executar como ou o recurso está bloqueado em um nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Executar como, você deve ter permissões apropriadas para os diversos recursos usados pela conta Executar como. Para saber mais sobre as permissões necessárias para criar ou atualizar uma conta Executar como, consulte [Permissões de conta Executar como](../manage-runas-account.md#permissions).

Se o problema ocorrer devido a um bloqueio, verifique se o bloqueio está pronto para ser removido e navegue até o recurso bloqueado, em seguida, clique com o botão direito do mouse no bloqueio e escolha **Excluir** para remover o bloqueio.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.