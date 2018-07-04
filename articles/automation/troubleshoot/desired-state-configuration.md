---
title: Solução de problemas com a configuração de estado desejado do DSL (Azure Automation Desired State Configuration)
description: Este artigo fornece informações sobre solução de problemas de configuração de estado desejado (DSC)
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d2eae67fcff74a7016f7f6125e31a9c8c2bda97
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064230"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Solucionar problemas de configuração de estado desejado (DSC)

Este artigo fornece informações sobre como solucionar problemas com a DSC (Configuração de Estado Desejado).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erros comuns ao trabalhar com DSC (Configuração de estado desejado)

### <a name="failed-not-found"></a>Cenário: o nó está com status de falha com um erro "Não encontrado"

#### <a name="issue"></a>Problema

O nó tem um relatório com o status **Falha** e contendo o erro:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC.WebServer).

#### <a name="resolution"></a>Resolução

* Verifique se você está atribuindo o nó com o "nome de configuração de nó" e não o "nome de configuração".
* Você pode atribuir uma configuração de nó para um nó usando o Portal do Azure ou com um cmdlet do PowerShell.

  * Para atribuir uma configuração de nó a um nó usando o portal do Azure, abra a página **Nós de DSC**, selecione um nó e clique no botão **Atribuir configuração de nó**.  
  * Para atribuir uma configuração de nó a um nó usando um cmdlet do PowerShell, use o cmdlet **Set-AzureRmAutomationDscNode**

### <a name="no-mof-files"></a> Cenário: Nenhuma configuração de nó (arquivos MOF) foi produzida quando uma configuração é compilada

#### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC é suspenso com o erro:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando a expressão após a palavra-chave **Node** na configuração do DSC for avaliada como `$null`, nenhuma configuração de nó será produzida.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Verifique se a expressão ao lado da palavra-chave **Node** na definição de configuração não está sendo avaliada como $null.
* Se você estiver passando ConfigurationData ao compilar a configuração, certifique-se de que esteja passando os valores esperados e que a configuração exige de [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Cenário: o relatório do nó DSC fica preso no estado "em progresso"

#### <a name="issue"></a>Problema

O agente DSC produz:

```
No instance found with given property values
```

#### <a name="cause"></a>Causa

Você atualizou sua versão do WMF e tem o WMI corrompido.

#### <a name="resolution"></a>Resolução

Para corrigir o problema, siga as instruções no artigo [Problemas e limitações conhecidos do DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc).

### <a name="issue-using-credential"></a> Cenário: não é possível usar uma credencial em uma configuração DSC

#### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC foi suspenso com o erro:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Você usou uma credencial em uma configuração, mas não forneceu **ConfigurationData** adequado para definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó.

#### <a name="resolution"></a>Resolução

* Transmita os **ConfigurationData** adequados para definir **PSDscAllowPlainTextPassword** como true cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [Ativos no DSC de Automação do Azure](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.