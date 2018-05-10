---
title: Referência de cmdlet Start-AzsReadinessChecker | Microsoft Docs
description: Ajuda de cmdlet do PowerShell para o módulo do verificador de preparação de pilha do Azure.
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
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Referência de cmdlet Start-AzsReadinessChecker

Módulo: Microsoft.AzureStack.ReadinessChecker

Esse módulo contém apenas um único cmdlet.  Esse cmdlet executa uma ou mais funções de pré-implantação ou pré-serviços de pilha do Azure.

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
O **AzsReadinessChecker início** cmdlet valida certificados, contas do Azure, as assinaturas do Azure e Active Directories do Azure. Execute a validação antes de implantar o Azure pilha ou antes de pilha do Azure ações como rotação de segredo de manutenção. O cmdlet também pode ser usado para gerar solicitações de autenticação de certificado para certificados de infraestrutura e, opcionalmente, certificados de PaaS.  Por fim, o cmdlet pode remontar os certificados PFX para corrigir problemas comuns de empacotamento.

## <a name="examples"></a>Exemplos
**Exemplo: Gerar a solicitação de assinatura de certificado**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Neste exemplo, iniciar AzsReadinessChecker gera vários certificados solicitações CSR (assinatura) para certificados adequados para uma implantação do ADFS Azure pilha com um nome de região de "east" e um FQDN externo de "azurestack.contoso.com"

**Exemplo: Validar certificados**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Neste exemplo, a senha PFX é solicitado a fornecer segurança e iniciar AzsReadinessChecker verifica a pasta relativa "Certificados" para certificados válidos para uma implantação do AAD com um nome de região de "east" e um FQDN externo de "azurestack.contoso.com" 

**Exemplo: Validar certificados com dados de implantação (implantação e suporte)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
Neste exemplo de implantação e suporte, a senha PFX é solicitado a fornecer segurança e início AzsReadinessChecker verifica a pasta relativa "Certificados" para certificados válidos para uma implantação em que identidade, região e FQDN externo são lidos a partir de arquivo de implantação dados JSON gerado para implantação. 

**Exemplo: Validar os certificados de PaaS**
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

Neste exemplo, uma tabela de hash é construída com senhas para cada certificado de PaaS e caminhos. Certificados podem ser omitidos. Iniciar AzsReadinessChecker verifica cada caminho PFX existe e valida-los usando a região 'Leste' e o FQDN externo 'azurestack.contoso.com'.

**Exemplo: Validar certificados de PaaS com dados de implantação**
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

Neste exemplo, uma tabela de hash é construída com senhas para cada certificado de PaaS e caminhos. Certificados podem ser omitidos. Iniciar AzsReadinessChecker verifica cada caminho PFX existe e valida o uso da região e FQDN externo ler o arquivo JSON de dados de implantação gerado para implantação. 

**Exemplo: Validar a identidade do Azure**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

Neste exemplo, as credenciais da conta de administrador de serviço for solicitadas com segurança e iniciar AzsReadinessChecker verifica que a conta do Azure e o Azure Active Directory são válidos para uma implantação com um nome de diretório do locatário do AAD do "azurestack.contoso.com"


**Exemplo: Validar a identidade do Azure com dados de implantação (suporte de implantação)**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

Neste exemplo, as credenciais da conta de administrador de serviço for solicitadas com segurança e iniciar AzsReadinessChecker verifica que a conta do Azure e o Azure Active Directory são válidos para uma implantação do AAD onde AzureCloud e TenantName são lidos os dados de implantação Arquivo JSON gerado para a implantação.


**Exemplo: Validar o registro do Azure**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

Neste exemplo, as credenciais do proprietário da assinatura for solicitadas com segurança e AzsReadinessChecker de início, em seguida, executa a validação em relação a conta e assinatura para garantir que ela pode ser usada para o registro de pilha do Azure. 


**Exemplo: Validar o registro do Azure com dados de implantação (equipe de implantação)**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Neste exemplo, as credenciais do proprietário da assinatura for solicitadas com segurança e AzsReadinessChecker de início, em seguida, executa a validação em relação a conta e assinatura para garantir que ela pode ser usada para o registro de pilha do Azure onde estão os detalhes adicionais ler o arquivo JSON de dados de implantação gerado para a implantação.

**Exemplo: Pacote de importação/exportação PFX**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Neste exemplo, a senha PFX é solicitada com segurança. O arquivo ssl.pfx será importado no repositório de certificados do computador local e re-exportado com a mesma senha e salvos como ssl_new.pfx.  Esse procedimento é para uso quando a validação de certificado sinalizados que uma chave privada não tem o conjunto de atributos do computador Local, a cadeia de certificados foi interrompida, irrelevantes certificados estão presentes no PFX ou a cadeia de certificados está na ordem errada.


**Exemplo: Exibir relatório de validação (suporte de implantação)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Neste exemplo, a equipe de suporte ou implantação recebe o relatório de prontidão do cliente (Contoso) e usa o início AzsReadinessChecker para exibir o status das execuções de validação executada Contoso.

**Exemplo: Exibir relatório de validação de resumo de certificado validação somente (implantação e suporte)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Neste exemplo, a equipe de suporte ou implantação recebe o relatório de prontidão do cliente Contoso e usa AzsReadinessChecker de início para exibir um status resumido das execuções de validação de certificado que Contoso executou.



## <a name="required-parameters"></a>Parâmetros necessários
> -RegionName

Especifica o nome da região da implantação de pilha do Azure.
|  |  |
|----------------------------|--------------|
|Digite:                       |Cadeia de caracteres        |
|Posição:                   |nomeado         |
|Valor padrão:              |Nenhum          |
|Aceite entrada de pipeline:      |Falso         |
|Aceite caracteres curinga: |Falso         |

> -FQDN    

Especifica o FQDN externo da implantação de pilha do Azure, também um alias como ExternalFQDN e ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Digite:                       |Cadeia de caracteres        |
|Posição:                   |nomeado         |
|Valor padrão:              |ExternalFQDN, ExternalDomainName |
|Aceite entrada de pipeline:      |Falso         |
|Aceite caracteres curinga: |Falso         |

 

> -IdentitySystem    

Especifica sistema de identidade os valores válidos a implantação do Azure pilha, AAD ou ADFS, para o Active Directory do Azure e serviços de Federação do Active Directory, respectivamente.
|  |  |
|----------------------------|--------------|
|Digite:                       |Cadeia de caracteres        |
|Posição:                   |nomeado         |
|Valor padrão:              |Nenhum          |
|Valores válidos:                |'AAD', 'ADFS'  |
|Aceite entrada de pipeline:      |Falso         |
|Aceite caracteres curinga: |Falso         |

> -PfxPassword    

Especifica a senha associada com os arquivos de certificado PFX.
|  |  |
|----------------------------|---------|
|Digite:                       |SecureString |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -PaaSCertificates

Especifica a tabela de hash que contêm caminhos e senhas para certificados de PaaS.
|  |  |
|----------------------------|---------|
|Digite:                       |Tabela de hash |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -DeploymentDataJSONPath

Especifica o arquivo de configuração do Azure pilha implantação dados JSON. Esse arquivo é gerado para a implantação.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -PfxPath

Especifica o caminho para um certificado de um problema que exige a rotina de importação/exportação para corrigir, conforme indicado pela validação de certificado nesta ferramenta.
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

> -O assunto

Especifica um dicionário ordenado de assunto para a geração de solicitação de certificado.
|  |  |
|----------------------------|---------|
|Digite:                       |OrderedDictionary   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -RequestType

Especifica o tipo de SAN da solicitação de certificado. Valores válidos MultipleCSR, SingleCSR.
- *MultipleCSR* gera várias solicitações de certificado, uma para cada serviço.
- *SingleCSR* gera uma solicitação de certificado para todos os serviços.   

|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Valores válidos:                |'MultipleCSR', 'SingleCSR' |
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

Especifica o administrador de serviço do Azure Active Directory a ser usado para implantação de pilha do Azure.
|  |  |
|----------------------------|---------|
|Digite:                       |PSCredential   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -AADDirectoryTenantName

Especifica o nome do Active Directory do Azure a ser usado para implantação de pilha do Azure.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -AzureEnvironment

Especifica a instância dos serviços do Azure que contém as contas, diretórios e assinaturas a ser usado para registro e a implantação de pilha do Azure.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Valores válidos:                |'AzureCloud', 'AzureChinaCloud', 'AzureGermanCloud' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -RegistrationAccount

Especifica a conta do registro a ser usado para registro de pilha do Azure.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -RegistrationSubscriptionID

Especifica a ID de assinatura de registro a ser usado para registro de pilha do Azure.
|  |  |
|----------------------------|---------|
|Digite:                       |Guid     |
|Posição:                   |nomeado    |
|Valor padrão:              |Nenhum     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |

> -ReportPath

Especifica o caminho para o relatório de preparação, o padrão é nome atual do relatório de diretório e padrão.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Todos      |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |



## <a name="optional-parameters"></a>Parâmetros opcionais
> -CertificatePath     

Especifica o caminho no qual apenas o certificado necessário certificado pastas estão presentes.

Pastas necessárias para a implantação de pilha do Azure com o sistema de identidade do Active Directory do Azure são:

ACSBlob, ACSQueue, ACSTable, Portal de administração, administrador ARM ARM público, KeyVault, KeyVaultInternal, Portal público

Pasta necessária para pilha Azure implantação com o sistema de identidade de serviços de Federação do Active Directory são:

ACSBlob, ACSQueue, ACSTable, ADFS, Portal de administração, administração do ARM, ARM público, gráfico, KeyVault, KeyVaultInternal, Portal público


|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |. \Certificates |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |


> -IncludePaaS  

Especifica se os serviços de PaaS/nomes de host deve ser adicionada para as solicitações de certificado.


|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |


> -ReportSections        

Especifica se apenas Mostrar relatório de resumo, omite detalhes.
|  |  |
|----------------------------|---------|
|Digite:                       |Cadeia de caracteres   |
|Posição:                   |nomeado    |
|Valor padrão:              |Todos      |
|Valores válidos:                |'Certificado', 'AzureRegistration', 'AzureIdentity', 'Trabalhos', 'Todos' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite caracteres curinga: |Falso    |


> -Resumo 

Especifica se apenas Mostrar relatório de resumo, omite detalhes.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |


> -CleanReport  

Remove o histórico de execução e validação anterior e grava as validações para um novo relatório.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Aliases:                    |CF                |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |


> -OutputPath    

Especifica o caminho personalizado para salvar o relatório de preparação JSON e o arquivo de log detalhado.  Se o caminho não existir, a ferramenta tentará criar o diretório.
|  |  |
|----------------------------|------------------|
|Digite:                       |Cadeia de caracteres            |
|Posição:                   |nomeado             |
|Valor padrão:              |$ENV: TEMP\AzsReadinessChecker  |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |


> -Confirmar  

Solicita confirmação antes de executar o cmdlet.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Aliases:                    |CF                |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |


> -WhatIf  

Mostra o que aconteceria se o cmdlet fosse executado. O cmdlet não é executado.
|  |  |
|----------------------------|------------------|
|Digite:                       |SwitchParameter   |
|Aliases:                    |Wi                |
|Posição:                   |nomeado             |
|Valor padrão:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite caracteres curinga: |Falso             |

 
