---
title: Relatório de validação para o Azure Stack | Microsoft Docs
description: Use o relatório de verificador de preparação do Azure Stack para examinar os resultados da validação.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 1b2b06c02dc54c4369dd8490b714d1444d4b3b01
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986178"
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
   > `Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json` 

A saída é semelhante ao seguinte:

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters : 
````

## <a name="view-the-report-summary"></a>Exibir o relatório de resumo
Para exibir um resumo do relatório, você pode adicionar o **-resumo** alternar para o final da linha de comando do PowerShell. Por exemplo:  
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary`  

O resumo mostra as validações que não têm resultados e indica a aprovação ou reprovação para validações forem concluídas. A saída é semelhante ao seguinte:

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

    Certificate Validation found no errors or warnings.
    
############### Registration Validation Summary ###############

    Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
````


## <a name="view-a-filtered-report"></a>Exibir um relatório filtrado
Para exibir um relatório que é filtrado em um único tipo de validação, use o **- ReportSections** parâmetro com um dos seguintes valores:
- Certificado
- AzureRegistration
- AzureIdentity
- Grafo
- ADFS
- Trabalhos   
- Todos  

Por exemplo, para exibir o relatório Resumo de certificados apenas, use a seguinte linha de comando do PowerShell: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Consulte também
