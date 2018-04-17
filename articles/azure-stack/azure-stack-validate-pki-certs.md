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
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
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
    Verifica a SAN contém nomes DNS relevantes para cada ponto de extremidade ou se um suporte curinga estiver presente.
- **Uso de chave**  
    Verifica se o uso da chave contém a assinatura digital e a codificação de chave e uso avançado de chave contém a autenticação do servidor e autenticação de cliente.
- **Tamanho da chave**  
    Verifica se o tamanho da chave é de 2.048 ou maior.
- **Ordem da cadeia**  
    Verifica a ordem dos certificados validar que a ordem é correta.
- **Outros certificados**  
    Certifique-se de que não há outros certificados foram agrupados em PFX que não seja o certificado de folha relevantes e sua cadeia.

> [!IMPORTANT]  
> O certificado PKI é um arquivo PFX e senha deve ser tratada como informações confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

O sistema deve atender aos seguintes pré-requisitos antes de validar os certificados PKI para uma implantação de pilha do Azure:

- Verificador de preparação de pilha do Microsoft Azure
- Certificado SSL exportado seguindo o [instruções de preparação](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-certificate-validation"></a>Executar a validação de certificado

Siga estas etapas para preparar e validar os certificados PKI de pilha do Azure:

1. Instale AzsReadinessChecker em um prompt do PowerShell (5.1 ou superior), executando o seguinte cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. Crie a estrutura de diretórios do certificado. No exemplo a seguir, você pode alterar `<c:\certificates>` para um novo caminho de diretório de sua escolha.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - Coloque seu certificado nas pastas apropriadas criadas na etapa anterior. Por exemplo:  
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - e assim por diante... 

3. Na janela do PowerShell que execute:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. Examine a saída para verificar se todos os certificados passaram nos testes. Por exemplo:

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
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
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

## <a name="using-validated-certificates"></a>Usando certificados validados

Depois que os certificados foram validados pelo AzsReadinessChecker, você está pronto para usá-los em sua implantação do Azure pilha ou para rotação de segredo de pilha do Azure. 

 - Para implantação, transfira com segurança seus certificados para seu engenheiro de implantação para que eles podem ser copiada para o host de implantação como especificado no [documentação de requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md).
 - Para rotação secreta, você pode usar os certificados para atualizar certificados antigos para pontos de extremidade de infraestrutura pública do seu ambiente de pilha do Azure seguindo o [documentação de rotação de segredo do Azure pilha](azure-stack-rotate-secrets.md).

## <a name="next-steps"></a>Próximas etapas

[Integração de identidade do Datacenter](azure-stack-integrate-identity.md)
