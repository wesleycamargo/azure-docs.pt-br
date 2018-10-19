---
title: Validar certificados de infraestrutura de chave pública do Azure Stack para implantação de sistemas integrados do Azure Stack | Microsoft Docs
description: Descreve como validar os certificados PKI de pilha do Azure para sistemas integrados do Azure Stack. Aborda como usar o verificador de certificado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8c89c49c1ea88937ba2494a4d9ee7adfa38ea1c1
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408947"
---
# <a name="validate-azure-stack-pki-certificates"></a>Validar certificados PKI de pilha do Azure

A ferramenta de verificador de preparação do Azure Stack descrita neste artigo está disponível [da Galeria do PowerShell](https://aka.ms/AzsReadinessChecker). Você pode usar a ferramenta para validar que o [gerados certificados PKI](azure-stack-get-pki-certs.md) são adequadas para antes da implantação. Você deve validar certificados por deixar tempo suficiente para testar e emitir novamente os certificados, se necessário.

A ferramenta de verificação de preparação executa as validações de certificado a seguir:

- **Ler PFX**  
    Verifica se há um arquivo PFX válido senha correta e avisa se informações públicas não são protegidas por senha. 
- **Algoritmo de assinatura**  
    Verifica se o algoritmo de assinatura não é SHA1.
- **Chave privada**  
    Verifica se a chave privada está presente e é exportada com o atributo do computador local. 
- **Cadeia de certificados**  
    Verificações de cadeia de certificados está intacta, incluindo uma verificação para os certificados autoassinados.
- **Nomes DNS**  
    Verifica a SAN contém nomes DNS relevantes para cada ponto de extremidade, ou se a dar suporte a curinga está presente.
- **Uso de chave**  
    Verifica se o uso da chave contém uma assinatura digital e a codificação de chave e o uso avançado de chave contém a autenticação do servidor e autenticação de cliente.
- **Tamanho da chave**  
    Verifica se o tamanho da chave é de 2048 ou superior.
- **Ordem da cadeia**  
    Verifica a ordem dos certificados validando se o pedido está correto.
- **Outros certificados**  
    Certifique-se de que nenhum outro certificado foram empacotado em PFX que não seja o certificado de folha relevantes e sua cadeia.
- **Nenhum perfil**  
    Verifica que um novo usuário pode carregar os dados PFX sem um perfil de usuário carregado, imitando o comportamento de contas gMSA durante a manutenção do certificado.

> [!IMPORTANT]  
> O certificado PKI é um arquivo PFX e senha deve ser tratada como informações confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

O sistema deve atender aos seguintes pré-requisitos antes de validar os certificados PKI para uma implantação do Azure Stack:

- Verificador de preparação do Microsoft Azure Stack
- Certificados SSL exportado seguindo o [instruções de preparação](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Executar a validação de certificado do core services

Use estas etapas para preparar e validar os certificados PKI de pilha do Azure para a implantação e a rotação do segredo:

1. Instale **AzsReadinessChecker** em um prompt do PowerShell (5.1 ou superior), executando o seguinte cmdlet:

    ```PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Crie a estrutura de diretório de certificado. No exemplo a seguir, você pode alterar `<c:\certificates>` para um novo caminho de diretório de sua escolha.
    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS e o gráfico serão necessárias se você estiver usando o AD FS como seu sistema de identidade.
    
     - Coloque os certificados nos diretórios apropriados criados na etapa anterior. Por exemplo:   
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. Na janela do PowerShell, altere os valores das **RegionName** e **FQDN** apropriado ao ambiente do Azure Stack e execute o seguinte:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker  -extensionhostfeature -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 
    ```

4. Verifique a saída e todos os certificados passarem todos os testes. Por exemplo: 

    ```PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ```

### <a name="known-issues"></a>Problemas conhecidos

**Sintoma**: testes são ignorados

**Causa**: AzsReadinessChecker ignora determinados testes se uma dependência não for atendida:

 - Outros certificados serão ignorados se a cadeia de certificados falhará.

    ```PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ```

**Resolução**: siga as diretrizes da ferramenta na seção de detalhes em cada conjunto de testes para cada certificado.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Executar a plataforma como uma validação de certificado de serviço

Use estas etapas para preparar e validar os certificados PKI de pilha do Azure para plataforma como um certificados de serviço (PaaS), se as implantações de SQL/MySQL ou serviços de aplicativos são planejadas.

1.  Instale **AzsReadinessChecker** em um prompt do PowerShell (5.1 ou superior), executando o seguinte cmdlet:

    ```PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Crie uma tabela de hash aninhada que contêm caminhos e a senha para cada certificado de PaaS que precisam de validação. Na janela do PowerShell execute:

    ```PowerShell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Altere os valores das **RegionName** e **FQDN** para corresponder ao seu ambiente do Azure Stack para iniciar a validação. Em seguida, execute:

    ```PowerShell  
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Verifique a saída e que todos os certificados passarem todos os testes.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Usando certificados validados

Depois que os certificados foram validados pela AzsReadinessChecker, você está pronto para usá-los em sua implantação do Azure Stack ou para a rotação do segredo do Azure Stack. 

 - Para a implantação, transfira com segurança seus certificados ao seu engenheiro de implantação para que eles podem ser copiada para o host de implantação conforme especificado na [documentação de requisitos do Azure Stack PKI](azure-stack-pki-certs.md).
 - Para rotação do segredo, você pode usar os certificados para atualizar o antigos certificados para pontos de extremidade de infraestrutura pública do seu ambiente do Azure Stack, seguindo a [documentação do Azure Stack segredo rotação](azure-stack-rotate-secrets.md).
 - Para serviços de PaaS, você pode usar os certificados para instalar o SQL, MySQL e provedores de recursos de serviços de aplicativo no Azure Stack, seguindo a [visão geral da oferta de serviços na documentação do Azure Stack](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Próximas etapas

[Integração de identidade do Datacenter](azure-stack-integrate-identity.md)