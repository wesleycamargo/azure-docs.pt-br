---
title: Relatório de validação para o Azure Stack | Microsoft Docs
description: Use o relatório de verificador de preparação do Azure Stack para examinar os resultados da validação.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 06b5660a9428e98d2e99b5d447a05700968ec884
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381906"
---
# <a name="azure-stack-validation-report"></a>Relatório de validação de pilha do Azure
Use o verificador de preparação de pilha do Azure para executar validações que dão suporte à implantação e manutenção de um ambiente do Azure Stack. A ferramenta grava os resultados em um arquivo de relatório. JSON. O relatório exibe dados resumidos e detalhados sobre o estado de pré-requisitos para implantação do Azure Stack. O relatório também exibe informações sobre a rotação de segredos para implantações de pilha do Azure existente.  

 ## <a name="where-to-find-the-report"></a>Onde encontrar o relatório
Quando a ferramenta é executada, ele registra resultados a serem **AzsReadinessCheckerReport.json**. A ferramenta também cria um log chamado **AzsReadinessChecker.log**. Exibe o local desses arquivos com os resultados de validação no PowerShell.

![executar a validação](./media/azure-stack-validation-report/validation.png)

Os dois arquivos persistirem os resultados das verificações de validação subsequente quando executados no mesmo computador.  Por exemplo, a ferramenta pode ser executada para validar certificados, execute novamente para validar a identidade do Azure e, em seguida, uma vez para validar o registro. Os resultados de todas as validações de três estão disponíveis no relatório. JSON resultante.  

Por padrão, os dois arquivos são gravados *C:\Users\<nome de usuário > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Use o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comando executar para especificar um local diferente do relatório.   
- Use o **- CleanReport** parâmetro no final do comando para limpar as informações de execução *AzsReadinessCheckerReport.json*. sobre as execuções anteriores da ferramenta.

## <a name="view-the-report"></a>Exibir o relatório
Para exibir o relatório no PowerShell, forneça o caminho para o relatório como um valor para **ReportPath -**. Esse comando exibe o conteúdo do relatório e identifica as validações que ainda não tiver os resultados.

Por exemplo, para exibir o relatório em um prompt do PowerShell que está aberto para o local onde se encontra o relatório, execute: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

A saída é semelhante a imagem a seguir:

![Exibir relatório](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Exibir o relatório de resumo
Para exibir um resumo do relatório, você pode adicionar o **-resumo** alternar para o final da linha de comando do PowerShell. Por exemplo:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

O resumo mostra as validações que não têm resultados e indica a aprovação ou reprovação para validações forem concluídas. A saída é semelhante a imagem a seguir:

![Resumo do relatório](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Exibir um relatório filtrado
Para exibir um relatório que é filtrado em um único tipo de validação, use o **- ReportSections** parâmetro com um dos seguintes valores:
- Certificado
- AzureRegistration
- AzureIdentity
- Trabalhos   
- Todos  

Por exemplo, para exibir o relatório Resumo de certificados apenas, use a seguinte linha de comando do PowerShell: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Consulte também
[Referência de cmdlet Start-AzsReadinessChecker](azure-stack-azsreadiness-cmdlet.md)
