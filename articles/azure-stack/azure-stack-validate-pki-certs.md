---
title: Validar os certificados de infraestrutura de chave pública do Azure pilha para implantação de sistemas de pilha do Azure integradas | Microsoft Docs
description: Descreve como validar os certificados PKI de pilha do Azure para sistemas de pilha do Azure integradas. Aborda como usar o verificador de certificado de pilha do Azure.
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
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605603"
---
# <a name="validate-azure-stack-pki-certificates"></a>Validar certificados PKI de pilha do Azure

A ferramenta do verificador de preparação do Azure pilha descrita neste artigo está disponível [da Galeria do PowerShell](https://aka.ms/AzsReadinessChecker). Você pode usar a ferramenta para validar que o [gerado certificados PKI](azure-stack-get-pki-certs.md) são adequadas para antes da implantação. Você deve validar certificados deixando tempo suficiente para testar e emitir novamente os certificados, se necessário.

O verificador de preparação executa as validações de certificado a seguir:

- **Ler PFX**  
    Verifica o arquivo PFX, senha correta e avisa se informações públicas não estão protegidas por senha. 
- **Algoritmo de assinatura**  
    Verifica se o algoritmo de assinatura não é SHA1.
- **Chave privada**  
    Verifica se a chave privada está presente e é exportada com o atributo do computador local. 
- **Cadeia de certificados**  
    Verificações de cadeia de certificados está intacta incluindo uma verificação para certificados autoassinados.
- **Nomes de DNS**  
    Verifica a SAN contém nomes DNS relevantes para cada ponto de extremidade, ou se um suporte curinga está presente.
- **Uso de chave**  
    Verifica se o uso da chave contém a assinatura digital e a codificação de chave e uso avançado de chave contém a autenticação do servidor e autenticação de cliente.
- **Tamanho da chave**  
    Verifica se o tamanho da chave é de 2.048 ou maior.
- **Ordem da cadeia**  
    Verifica a ordem dos certificados validar que a ordem é correta.
- **Outros certificados**  
    Certifique-se de que não há outros certificados foram agrupados em PFX que não seja o certificado de folha relevantes e sua cadeia.
- **Nenhum perfil**  
    Verifica que um novo usuário pode carregar os dados PFX sem um perfil de usuário carregado, imitando o comportamento de gMSA contas durante a manutenção do certificado.

> [!IMPORTANT]  
> O certificado PKI é um arquivo PFX e senha deve ser tratada como informações confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

O sistema deve atender aos seguintes pré-requisitos antes de validar os certificados PKI para uma implantação de pilha do Azure:

- Verificador de preparação de pilha do Microsoft Azure
- Certificado SSL exportado seguindo o [instruções de preparação](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Executar a validação do certificado de serviços de núcleo

Siga estas etapas para preparar e validar os certificados PKI de pilha do Azure para a implantação e a rotação de segredo:

1. Instalar **AzsReadinessChecker** de um prompt do PowerShell (5.1 ou superior), executando o seguinte cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. Crie a estrutura de diretórios do certificado. No exemplo a seguir, você pode alterar `<c:\certificates>` para um novo caminho de diretório de sua escolha.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > O AD FS e gráfico são necessários se você estiver usando o AD FS como seu sistema de identidade.
    
     - Coloque seu certificado nas pastas apropriadas criadas na etapa anterior. Por exemplo:   
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. Na janela do PowerShell, altere os valores de **RegionName** e **FQDN** apropriado para o ambiente de pilha do Azure e execute o seguinte:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. Verifique a saída e todos os certificados passarem em todos os testes. Por exemplo: 

    ````PowerShell
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
    ````

### <a name="known-issues"></a>Problemas conhecidos

**Sintoma**: testes são ignorados

**Causa**: AzsReadinessChecker ignorará certos testes se uma dependência não for atendida:

 - Outros certificados são ignorados se falhar de cadeia de certificados.

    ````PowerShell  
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
    ````

**Resolução**: siga a orientação da ferramenta na seção de detalhes em cada conjunto de testes para cada certificado.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Executar plataforma como uma validação de certificado de serviço

Use estas etapas para preparar e validar os certificados PKI de pilha do Azure para plataforma como certificados de serviço (PaaS), se planejados implantações de SQL/MySQL ou serviços de aplicativos.

1.  Instalar **AzsReadinessChecker** de um prompt do PowerShell (5.1 ou superior), executando o seguinte cmdlet:

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  Crie uma tabela de hash aninhada que contêm caminhos e uma senha para cada certificado de PaaS necessidade da validação. Na janela do PowerShell que execute:

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Alterar os valores de **RegionName** e **FQDN** para coincidir com seu ambiente de pilha do Azure para iniciar a validação. Em seguida, execute:

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

Depois que os certificados foram validados pelo AzsReadinessChecker, você está pronto para usá-los em sua implantação do Azure pilha ou para rotação de segredo de pilha do Azure. 

 - Para implantação, transfira com segurança seus certificados para seu engenheiro de implantação para que eles podem ser copiada para o host de implantação como especificado no [documentação de requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md).
 - Para rotação secreta, você pode usar os certificados para atualizar certificados antigos para pontos de extremidade de infraestrutura pública do seu ambiente de pilha do Azure seguindo o [documentação de rotação de segredo do Azure pilha](azure-stack-rotate-secrets.md).
 - Para serviços de PaaS, você pode usar os certificados para instalar o SQL, MySQL e provedores de recursos de serviços de aplicativo na pilha do Azure seguindo o [visão geral da oferta de serviços na documentação do Azure pilha](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Próximas etapas

[Integração de identidade do Datacenter](azure-stack-integrate-identity.md)