---
title: "Automatizar a remoção de grupos de recursos | Microsoft Docs"
description: "Versão do Fluxo de Trabalho do PowerShell de um cenário de Automação do Azure, incluindo runbooks para remover todos os grupos de recursos de sua assinatura."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
ms.assetid: b848e345-fd5d-4b9d-bc57-3fe41d2ddb5c
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: magoedte
ms.openlocfilehash: cb7183cbec1c3efafe58f4508042d329be5dcecf
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Cenário da Automação do Azure - automatize a remoção de grupos de recursos
Muitos clientes criam mais de um grupo de recursos. Alguns podem ser usados para gerenciar aplicativos de produção e outros podem ser usados como ambientes de desenvolvimento, teste e preparo. A automatização da implantação desses recursos é uma coisa, mas poder encerrar um grupo de recursos com um clique do botão é outra. Você pode simplificar essa tarefa comum de gerenciamento usando a Automação do Azure. Isso é útil se você está trabalhando com uma assinatura do Azure com um limite de gastos por meio de uma oferta de membro como o MSDN ou o programa Microsoft Partner Network Cloud Essentials.

Este cenário se baseia em um runbook do PowerShell e foi projetado para remover da sua assinatura um ou mais grupos de recursos especificados por você. A configuração padrão do runbook é para testar antes de continuar. Isso faz com que você não exclua o grupo de recursos acidentalmente antes de estar pronto para concluir o procedimento.   

## <a name="getting-the-scenario"></a>Obtendo o cenário
Este cenário consiste em um runbook do PowerShell que você pode baixar da [Galeria do PowerShell](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript). Você também pode importá-la diretamente da [Galeria de runbooks](automation-runbook-gallery.md) no portal do Azure.<br><br>

| Runbook | DESCRIÇÃO |
| --- | --- |
| Remove-ResourceGroup |Remove um ou mais grupos de recursos do Azure e seus recursos associados da assinatura. |

<br>
Os parâmetros de entrada a seguir são definidos para este runbook:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| NameFilter (Obrigatório) |Especifica um filtro de nome para limitar os grupos de recursos que você pretende excluir. Você pode passar vários valores usando uma lista separada por vírgulas.<br>O filtro não diferencia maiúsculas de minúsculas e corresponde a qualquer grupo de recursos que contenha a cadeia de caracteres. |
| PreviewMode (opcional) |Executa o runbook para ver quais grupos de recursos seriam excluídos, mas não realiza nenhuma ação.<br>O padrão é **true** para ajudar a evitar a exclusão acidental de um ou mais grupos de recursos passados para o runbook. |

## <a name="install-and-configure-this-scenario"></a>Instalar e configurar esse cenário
### <a name="prerequisites"></a>pré-requisitos
Esse runbook se autentica usando a [conta Executar como do Azure](automation-sec-configure-azure-runas-account.md).    

### <a name="install-and-publish-the-runbooks"></a>Instalar e publicar os runbooks
Depois de baixar o runbook, você poderá importá-lo usando o procedimento em [Procedimentos para importar runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation). Publique o runbook depois que ele for importado com êxito em sua conta de Automação.

## <a name="using-the-runbook"></a>Usando o runbook
As etapas a seguir orientam você pela execução deste runbook e o ajudarão a se familiarizar com o funcionamento dele. Você testará o runbook neste exemplo, sem excluir o grupo de recursos.  

1. No Portal do Azure, abra sua conta da Automação e clique no bloco **Runbooks**.
2. Selecione o runbook **Remove-ResourceGroup** e clique em **Iniciar**.
3. Quando você inicia o runbook, a página **Iniciar Runbook** é aberta e você pode configurar valores a seguir para os parâmetros. Digite os nomes dos grupos de recursos em sua assinatura que você pode usar para testar e não causem danos se forem excluídos acidentalmente.

   > [!NOTE]
   > Verifique se **Previewmode** está definido como **true** para evitar a exclusão de grupos de recursos selecionados. Este runbook não remove o grupo de recursos com a conta da Automação que está executando esse runbook.  
   >
   >
1. Depois de configurar todos os valores de parâmetro, clique em **OK** e o runbook será enfileirado para execução.  

Para exibir os detalhes do trabalho do runbook **Remove-ResourceGroup** no Portal do Azure, em **Recurso** selecione **Trabalhos** no runbook. Selecione o trabalho que você deseja exibir. O resumo do trabalho exibe os parâmetros de entrada e o fluxo de saída, além de informações gerais sobre o trabalho e todas as exceções ocorridas.<br> ![Status de trabalho do runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

O **Resumo do Trabalho** inclui mensagens de fluxos de saída, de aviso e de erro. Selecione **Saída** para exibir os resultados detalhados da execução do runbook.<br> ![Resultados da saída do runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Próximas etapas
* Para começar a criar seu próprio runbook, confira [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md).
* Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
