---
title: Relatório de validação para a pilha do Azure | Microsoft Docs
description: Use o relatório de verificação de preparação de pilha do Azure para examinar os resultados de validação.
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
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937813"
---
# <a name="azure-stack-validation-report"></a>Relatório de validação de pilha do Azure
O verificador de preparação de pilha do Azure executa validações que oferecem suporte à implantação e manutenção de um ambiente de pilha do Azure. A ferramenta grava os resultados da validação para um arquivo de relatório. JSON. O relatório exibe dados resumidos e detalhados no estado de pré-requisitos para implantação da pilha do Azure e em rotação segredos para implantações de pilha do Azure existente.  

 ## <a name="where-to-find-the-report"></a>Onde encontrar o relatório
Quando a ferramenta é executada, ela registra resultados **AzsReadinessCheckerReport.json**. A ferramenta também cria um log chamado **AzsReadinessChecker.log**. Exibe o local desses arquivos com os resultados de validação no PowerShell.

![executar a validação](./media/azure-stack-validation-report/validation.png)

Os dois arquivos mantém os resultados das verificações subsequentes de validação quando executados no mesmo computador.  Por exemplo, a ferramenta pode ser executada para validar certificados, execute novamente para validar a identidade do Azure e, em seguida, uma vez para validar o registro. Os resultados de todas as validações de três estão disponíveis no relatório. JSON resultante.  

Por padrão, os dois arquivos são gravados *C:\Users\<nome de usuário > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Use o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comando de execução para especificar um local diferente do relatório.   
- Use o **- CleanReport** parâmetro no final do comando para limpar as informações de execução *AzsReadinessCheckerReport.json*. sobre as execuções anteriores da ferramenta.

## <a name="view-the-report"></a>Exibir o relatório
Para exibir o relatório no PowerShell, forneça o caminho para o relatório como um valor para **- ReportPath**. Este comando exibe o conteúdo do relatório e também identifica validações que ainda não tem resultados.

Por exemplo, para exibir o relatório em um prompt do PowerShell que está aberto para o local onde se encontra o relatório, execute: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

A saída é semelhante a imagem a seguir:

![Exibir relatório](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Exibir o relatório de resumo
Para exibir um resumo do relatório, você pode adicionar o **-resumo** alternar para o fim da linha de comando do PowerShell. Por exemplo:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

O resumo mostra as validações que não possuem resultados e indica aprovação ou reprovação para validações forem concluídas. A saída é semelhante a imagem a seguir:

![Resumo do relatório](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Exibir um relatório filtrado
Para exibir um relatório filtrado em um único tipo de validação, use o **- ReportSections** parâmetro e especifique um dos seguintes valores que correspondem ao tipo de validação você deseja exibir:
- Certificado
- AzureRegistration
- AzureIdentity
- Trabalhos   
- Todos  

Por exemplo, para exibir o relatório Resumo de certificados apenas, use a seguinte linha de comando do PowerShell: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Consulte também
[Referência de cmdlet Start-AzsReadinessChecker](azure-stack-azsreadiness-cmdlet.md)
