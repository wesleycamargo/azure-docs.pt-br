---
title: Validar a integração do Graph do Azure para o Azure Stack
description: Use o verificador de preparação do Azure Stack para validar a integração do gráfico para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: fbc6e7586e116df1fa19c6c9e0c5a5b17f00d646
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096749"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Validar a integração do gráfico para o Azure Stack

Use a ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) para verificar se seu ambiente está preparado para a integração do gráfico com o Azure Stack. Valide a integração do gráfico antes de começar a integração do datacenter ou antes de uma implantação do Azure Stack.

Valida o verificador de preparação:

* As credenciais para a conta de serviço criados para integração com o gráfico tem os direitos apropriados para consultar o Active Directory.
* O *catálogo global* pode ser resolvido e é passível de ser contatada.
* O KDC pode ser resolvido e é passível de ser contatado.
* Conectividade de rede necessária está em vigor.

Para obter mais informações sobre a integração do datacenter do Azure Stack, consulte [integração de datacenter do Azure Stack – identidade](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação da prontidão

Baixar a versão mais recente da ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) a [da Galeria do PowerShell](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos devem estar em vigor.

**O computador em que a ferramenta é executada:**

* Windows 10 ou Windows Server 2016, com a conectividade do domínio.
* PowerShell 5.1 ou posterior. Para verificar sua versão, execute o seguinte comando do PowerShell e, em seguida, examine os *principais* versão e *secundárias* versões:  
   > `$PSVersionTable.PSVersion`
* Módulo do PowerShell do Active Directory.
* Versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.

**Ambiente do Active Directory:**

* Identificar o nome de usuário e senha de uma conta para o serviço do graph na instância existente do Active Directory.
* Identifique a raiz da floresta do Active Directory FQDN.

## <a name="validate-the-graph-service"></a>Validar o serviço de gráfico

1. Em um computador que atenda aos pré-requisitos, abra um prompt do PowerShell administrativo e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. No prompt do PowerShell, execute o seguinte comando para definir a *$graphCredential* variável para a conta do gráfico. Substitua `contoso\graphservice` com sua conta usando o `domain\username` formato.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. No prompt do PowerShell, execute o seguinte comando para iniciar a validação para o serviço de gráfico. Especifique o valor para **- ForestFQDN** como o FQDN para a raiz da floresta.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Depois que a ferramenta é executada, examine a saída. Confirme se o status é Okey para os requisitos de integração do gráfico. Uma validação bem-sucedida é semelhante ao exemplo a seguir:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

Em ambientes de produção, teste a conectividade de rede da estação de trabalho de um operador não é totalmente uma indicação da conectividade disponível para o Azure Stack. Rede de VIP público do carimbo de pilha do Azure precisa de conectividade para o tráfego LDAP para realizar a integração de identidade.

## <a name="report-and-log-file"></a>Arquivo de log e relatório

A cada hora de validação é executada, ela registra resultados a serem **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. O local desses arquivos é exibida com os resultados da validação no PowerShell.

Os arquivos de validação podem ajudá-lo a compartilhar o status antes de implantar o Azure Stack ou investigar problemas de validação. Os dois arquivos persistirem os resultados de cada verificação de validação subsequente. O relatório fornece sua confirmação da equipe de implantação da configuração de identidade. O arquivo de log pode ajudar sua equipe de implantação ou suporte a investigar problemas de validação.

Por padrão, os dois arquivos são gravados `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Use:

* **-OutputPath**: O *caminho* parâmetro no final do comando de execução para especificar um local diferente do relatório.
* **-CleanReport**: O parâmetro no final do comando de execução para limpar *AzsReadinessCheckerReport.json* das informações de relatório anterior. Para obter mais informações, consulte [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falha na validação

Se uma verificação de validação falhar, os detalhes sobre a falha são exibidos na janela do PowerShell. A ferramenta também registra informações a serem *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Próximas etapas

[Exibir o relatório de preparação](azure-stack-validation-report.md)  
[Considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)  
