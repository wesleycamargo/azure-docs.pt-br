---
title: Referência de cmdlet Start-AzsReadinessChecker | Microsoft Docs
description: Ajuda de cmdlet do PowerShell para o módulo do verificador de preparação do Azure Stack.
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
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 861784a2d22d15253c61884bfab62667c0560bcd
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250632"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Referência de cmdlet Start-AzsReadinessChecker

Módulo: Microsoft.AzureStack.ReadinessChecker

Esse módulo contém apenas um único cmdlet.  Esse cmdlet executa uma ou mais funções de pré-implantação ou pré-manutenção para o Azure Stack.

## <a name="syntax"></a>Sintaxe

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>DESCRIÇÃO

O **AzsReadinessChecker início** cmdlet valida os certificados, as contas do Azure, as assinaturas do Azure e Active Directories do Azure. Execute a validação antes de implantar o Azure Stack ou antes da manutenção de ações como a rotação do segredo do Azure Stack. O cmdlet também pode ser usado para gerar solicitações de assinatura de certificado para certificados de infraestrutura e, opcionalmente, certificados de PaaS.  Por fim, o cmdlet pode reempacotar os certificados PFX para corrigir problemas comuns de empacotamento.

## <a name="examples"></a>Exemplos

### <a name="example-generate-certificate-signing-request"></a>Exemplo: Gerar a solicitação de assinatura de certificado

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Neste exemplo, iniciar AzsReadinessChecker gera vários certificados solicitações CSR (assinatura) para certificados adequados para uma implantação do ADFS do Azure Stack com um nome de região de "Leste" e um FQDN externo "azurestack.contoso.com"

### <a name="example-validate-certificates"></a>Exemplo: Validar certificados

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Neste exemplo, a senha do PFX é solicitado a fornecer com segurança e início AzsReadinessChecker verifica a pasta "Certificados" relativa para certificados válidos para uma implantação de AAD com um nome de região "Leste" e um FQDN externo "azurestack.contoso.com"

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Exemplo: Validar certificados com dados de implantação (implantação e suporte)

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

Neste exemplo de implantação e suporte, a senha do PFX é solicitado a fornecer com segurança e início AzsReadinessChecker verifica a pasta "Certificados" relativa para certificados válidos para uma implantação em que a identidade, região e FQDN externo são lidas das arquivo JSON da data de implantação gerado para implantação. 

### <a name="example-validate-paas-certificates"></a>Exemplo: Validar certificados do PaaS

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

Neste exemplo, uma tabela de hash é construída com caminhos e senhas para cada certificado de PaaS. Certificados podem ser omitidos. Iniciar AzsReadinessChecker verifica cada caminho PFX existe e valida-os usando a região 'Leste' e o FQDN externo 'azurestack.contoso.com'.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Exemplo: Validar certificados de PaaS com dados de implantação

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

Neste exemplo, uma tabela de hash é construída com caminhos e senhas para cada certificado de PaaS. Certificados podem ser omitidos. Início AzsReadinessChecker verifica cada caminho PFX existe e valida-os usando a região e o FQDN externo de leitura do arquivo JSON de implantação dados gerado para a implantação. 

### <a name="example-validate-azure-identity"></a>Exemplo: Validar a identidade do Azure

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

Neste exemplo, as credenciais da conta de administrador de serviço for solicitado a fornecer com segurança e início AzsReadinessChecker verifica que a conta do Azure e o Azure Active Directory são válidos para uma implantação com um nome de diretório do locatário do AAD de "azurestack.contoso.com"

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Exemplo: Validar a identidade do Azure com os dados de implantação (suporte à implantação)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

Neste exemplo, as credenciais da conta de administrador de serviço for solicitado a fornecer com segurança e início AzsReadinessChecker verifica que a conta do Azure e o Azure Active Directory são válidos para uma implantação do AAD onde AzureCloud e TenantName são lidos os dados de implantação Arquivo JSON gerado para a implantação.

### <a name="example-validate-azure-registration"></a>Exemplo: Validar o registro do Azure

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

Neste exemplo, as credenciais do proprietário da assinatura serão solicitado a fornecer com segurança e AzsReadinessChecker de início, em seguida, executa a validação em relação a determinada conta e assinatura para assegurar que ele pode ser usada para o registro do Azure Stack. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Exemplo: Validar o registro do Azure com os dados de implantação (equipe de implantação)

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Neste exemplo, as credenciais do proprietário da assinatura for solicitadas com segurança e AzsReadinessChecker de início, em seguida, executa a validação em relação a determinada conta e assinatura para assegurar que ele pode ser usada para o registro de pilha do Azure onde estão os detalhes adicionais leitura do arquivo JSON de implantação dados gerado para a implantação.

### <a name="example-importexport-pfx-package"></a>Exemplo: Pacote de importação/exportação PFX

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Neste exemplo, a senha do PFX é solicitada com segurança. O arquivo ssl.pfx será importado no repositório de certificados do computador local e re-exportado com a mesma senha e salva como ssl_new.pfx.  Esse procedimento é para uso quando sinalizado de validação de certificado que uma chave privada não tem o conjunto de atributos de computador Local, a cadeia de certificados é interrompida, irrelevantes certificados estão presentes no PFX ou a cadeia de certificados está na ordem errada.

### <a name="example-view-validation-report-deployment-support"></a>Exemplo: Exibir relatório de validação (suporte à implantação)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Neste exemplo, a equipe de suporte ou implantação recebe o relatório de prontidão do cliente (Contoso) e usa AzsReadinessChecker inicial para exibir o status das execuções de validação executada da Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Exemplo: Exibir relatório de validação de resumo para o certificado validação somente (implantação e suporte)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Neste exemplo, a equipe de suporte ou implantação recebe o relatório de prontidão do cliente Contoso e usa AzsReadinessChecker inicial para exibir um status resumido das execuções de validação do certificado executada da Contoso.

## <a name="required-parameters"></a>Parâmetros obrigatórios

> -RegionName

Especifica o nome da região da implantação do Azure Stack.
|  |  |
|----------------------------|--------------|
|Digite:                       |Cadeia de caracteres        |
|Posição:                   |nomeado         |
|Valor padrão:              |Nenhum          |
|Aceite entrada de pipeline:      |Falso         |
|Aceite caracteres curinga: |Falso         |

> -FQDN

Especifica o FQDN externo da implantação do Azure Stack, também tem um alias como ExternalFQDN e ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Digite:                       |Cadeia de caracteres        |
|Posição:                   |nomeado         |
|Valor padrão:              |ExternalFQDN, ExternalDomainName |
|Aceite entrada de pipeline:      |Falso         |
|Aceite caracteres curinga: |Falso         |

> -IdentitySystem

Especifica os valores válidos da implantação do Azure Stack sistema de identidade, AAD ou ADFS, para o Azure Active Directory e Active Directory Federated Services respectivamente.
|  |  |
|----------------------------|--------------|
|Digite:                       |Cadeia de caracteres        |
|Posição:                   |nomeado         |
|Valor padrão:              |Nenhum          |
|Valores válidos:                |'AAD','ADFS'  |
|Aceite entrada de pipeline:      |Falso         |
|Aceite caracteres curinga: |Falso         |

> -PfxPassword

Especifica a senha associada a arquivos de certificado PFX.
|  |  |
|----------------------------|---------|
|Digite:                       |SecureString |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -PaaSCertificates

Especifica a tabela de hash que contêm caminhos e as senhas para certificados de PaaS.
|  |  |
|----------------------------|---------|
|Digite:                       |Tabela de hash |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -DeploymentDataJSONPath

Especifica o arquivo de configuração do Azure Stack implantação dados JSON. Esse arquivo é gerado para a implantação.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -PfxPath

Especifica o caminho para um certificado de um problema que exige a rotina de importação/exportação para corrigir, conforme indicado pela validação do certificado nessa ferramenta.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -ExportPFXPath  

Especifica o caminho de destino para o arquivo PFX resultante da rotina de importação/exportação.  
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -Assunto

Especifica um dicionário ordenado do assunto para a geração de solicitação de certificado.
|  |  |
|----------------------------|---------|
|Digite:                       |OrderedDictionary   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -RequestType

Especifica o tipo de SAN da solicitação de certificado. Valid values MultipleCSR, SingleCSR.

- *MultipleCSR* gera várias solicitações de certificado, uma para cada serviço.
- *SingleCSR* gera uma solicitação de certificado para todos os serviços.

|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Valores válidos:                |'MultipleCSR','SingleCSR' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -OutputRequestPath

Especifica o caminho de destino para arquivos de solicitação de certificado, o diretório já deve existir.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -AADServiceAdministrator

Especifica o administrador do serviço de diretório do Azure Active Directory a ser usado para implantação do Azure Stack.
|  |  |
|----------------------------|---------|
|Digite:                       |PSCredential   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -AADDirectoryTenantName

Especifica o nome do Active Directory do Azure a ser usado para implantação do Azure Stack.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -AzureEnvironment

Especifica a instância dos serviços do Azure que contém as contas, diretórios e assinaturas a ser usado para registro e implantação do Azure Stack.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Valores válidos:                |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -RegistrationAccount

Especifica a conta de registro a ser usado para registro do Azure Stack.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -RegistrationSubscriptionID

Especifica a ID de assinatura de registro a ser usado para registro do Azure Stack.
|  |  |
|----------------------------|---------|
|Digite:                       |Guid     |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -ReportPath

Especifica o caminho para o relatório de preparação, o padrão é nome de relatório padrão e o diretório atual.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Todos      |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

## <a name="optional-parameters"></a>Parâmetros opcionais

> -CertificatePath

Especifica o caminho em que apenas o certificado necessário pastas de certificado estão presentes.

As pastas necessárias para a implantação do Azure Stack com sistema de identidade do Active Directory do Azure são:

ACSBlob, ACSQueue, ACSTable, Portal de administração, administração do ARM, público, KeyVault, KeyVaultInternal, Portal público de ARM

Pasta necessária para o Azure Stack implantação com o sistema de identidade de serviços de Federação do Active Directory são:

ACSBlob, ACSQueue, ACSTable, ADFS, Portal de administração, Admin do ARM, público ARM, Graph, KeyVault, KeyVaultInternal, Portal público

|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |.\Certificates |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -IncludePaaS  

Especifica se os serviços de PaaS/nomes de host deve ser adicionado para as solicitações de certificado.

|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |

> -ReportSections

Especifica se Mostrar relatório de resumo, apenas omite detalhes.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Todos      |
|Valores válidos:                |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -Resumo

Especifica se Mostrar relatório de resumo, apenas omite detalhes.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |

> -CleanReport

Remove o histórico de execução e validação anterior e grava as validações em um novo relatório.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |

> -OutputPath

Especifica o caminho personalizado para salvar o relatório de prontidão para JSON e o arquivo de log detalhado.  Se o caminho não existir, a ferramenta tentará criar o diretório.

|  |  |
|----------------------------|------------------|
|Digite:                       |Cadeia de caracteres            |
|Posição:                   |nomeado             |
|Valor padrão:              |$ENV:TEMP\AzsReadinessChecker  |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |

> -Confirm

Solicita sua confirmação antes de executar o cmdlet.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |

> -WhatIf

Mostra o que aconteceria se o cmdlet fosse executado. O cmdlet não é executado.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Aliases:                    |wi                |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |
