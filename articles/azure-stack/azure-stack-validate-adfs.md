---
title: Validar a integração do AD FS para o Azure Stack
description: Use o verificador de preparação do Azure Stack para validar a integração do AD FS para o Azure Stack.
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
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 87e3f03ce5d4c65d5c4b1754300f5d57feca2a49
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416504"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>Validar a integração do AD FS para o Azure Stack

Use a ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) para verificar se seu ambiente está preparado para a integração de serviços de Federação do Active Directory (AD FS) com o Azure Stack. Valide a integração do AD FS antes de começar a integração do datacenter ou antes de uma implantação do Azure Stack.

Valida o verificador de preparação:

* O *metadados de Federação* contém os elementos XML válidos para a federação.
* O *certificado SSL do AD FS* pode ser recuperado e uma cadeia de confiança pode ser criado. No carimbo do AD FS devem confiar a cadeia de certificados SSL. O certificado deve ser assinado pelo mesmo *autoridade de certificação* como os certificados de implantação do Azure Stack ou por um parceiro de autoridade raiz confiável. Para obter a lista completa dos parceiros de autoridade raiz confiável, consulte [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* O *certificado de autenticação do AD FS* é confiável e não está de expiração.

Para obter mais informações sobre a integração do datacenter do Azure Stack, consulte [integração de datacenter do Azure Stack – identidade](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação da prontidão

Baixar a versão mais recente da ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) a [da Galeria do PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos devem estar em vigor.

**O computador em que a ferramenta é executada:**

* Windows 10 ou Windows Server 2016, com a conectividade do domínio.
* PowerShell 5.1 ou posterior. Para verificar sua versão, execute o seguinte comando do PowerShell e, em seguida, examine os *principais* versão e *secundárias* versões:  
   > `$PSVersionTable.PSVersion`
* Versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.

**Ambiente de serviços de Federação do Active Directory:**

Você precisa de pelo menos um dos seguintes formatos de metadados:

* A URL de metadados de Federação do AD FS. Um exemplo é `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* O arquivo XML de metadados de Federação. Um exemplo é federationmetadata.

## <a name="validate-ad-fs-integration"></a>Validar a integração do AD FS

1. Em um computador que atenda aos pré-requisitos, abra um prompt do PowerShell administrativo e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. No prompt do PowerShell, execute o seguinte comando para iniciar a validação. Especifique o valor para **- CustomADFSFederationMetadataEndpointUri** como o URI para os metadados de Federação.

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Depois que a ferramenta é executada, examine a saída. Confirme se o status é Okey para requisitos de integração do AD FS. Uma validação bem-sucedida é semelhante ao exemplo a seguir:

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

Em ambientes de produção, teste as cadeias de certificados de confiança da estação de trabalho de um operador não é totalmente uma indicação da postura de relação de confiança PKI na infraestrutura do Azure Stack. Rede de VIP público do carimbo de pilha do Azure precisa a conectividade com a CRL para a infraestrutura de PKI.

## <a name="report-and-log-file"></a>Arquivo de log e relatório

A cada hora de validação é executada, ela registra resultados a serem **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. O local desses arquivos é exibida com os resultados da validação no PowerShell.

Os arquivos de validação podem ajudá-lo a compartilhar o status antes de implantar o Azure Stack ou investigar problemas de validação. Os dois arquivos persistirem os resultados de cada verificação de validação subsequente. O relatório fornece sua confirmação da equipe de implantação da configuração de identidade. O arquivo de log pode ajudar sua equipe de implantação ou suporte a investigar problemas de validação.

Por padrão, os dois arquivos são gravados `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Use:

* **-OutputPath**: O *caminho* parâmetro no final do comando de execução para especificar um local diferente do relatório.
* **-CleanReport**: O parâmetro no final do comando de execução de limpar AzsReadinessCheckerReport.json das informações de relatório anterior. Para obter mais informações, consulte [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falha na validação

Se uma verificação de validação falhar, os detalhes sobre a falha são exibidos na janela do PowerShell. A ferramenta também registra informações a serem *AzsReadinessChecker.log*.

Os exemplos a seguir fornecem orientação sobre as falhas comuns de validação.

### <a name="command-not-found"></a>Comando não encontrado

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Causa**: Autoload PowerShell Falha ao carregar o módulo do verificador de preparação corretamente.

**Resolução**: importar o módulo do verificador de preparação explicitamente. Copie e cole o código a seguir no PowerShell e atualização \<versão\> com o número da versão atualmente instalada.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Próximas etapas

[Exibir o relatório de preparação](azure-stack-validation-report.md)  
[Considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)  
