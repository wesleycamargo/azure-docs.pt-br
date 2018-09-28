---
title: Preparar para o host de extensão para o Azure Stack | Microsoft Docs
description: Aprenda a se preparar para o host de extensão, que é habilitado automaticamente por meio de um pacote de atualização da pilha do Azure futuras.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 09/26/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: 3e35b0d9ba697b54b0fb85096caceeaa024b1f3d
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405233"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Preparar para o host de extensão para o Azure Stack

O host de extensão protege o Azure Stack, reduzindo o número de portas TCP/IP. Este artigo examina a preparação do Azure Stack para o host de extensão, que é habilitado automaticamente por meio de um pacote de atualização de pilha do Azure após a atualização 1808.

## <a name="certificate-requirements"></a>Requisitos de certificado

O host de extensão implementa dois novos namespaces de domínios para garantir entradas de host exclusivo para cada extensão do portal. Os novos namespaces do domínio exigir dois certificados curinga adicionais para garantir uma comunicação segura.

A tabela mostra os novos namespaces e os certificados associados:

| Pasta de implantação | Assunto do certificado necessário e nomes alternativos de entidade (SAN) | Escopo (por região) | Namespace de subdomínio |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Host de extensão de administração | *.adminhosting. \<região >. \<fqdn > (os certificados SSL curinga) | Host de extensão de administração | adminhosting. \<região >. \<fqdn > |
| Host de extensão público | * .hosting. \<região >. \<fqdn > (os certificados SSL curinga) | Host de extensão público | hospedagem. \<região >. \<fqdn > |

Os requisitos de certificado detalhadas podem ser encontrados na [requisitos de certificado de infra-estrutura de chave pública do Azure Stack](azure-stack-pki-certs.md) artigo.

## <a name="create-certificate-signing-request"></a>Criar solicitação de assinatura de certificado

A ferramenta de verificador de preparação do Azure Stack fornece a capacidade de criar um certificado de assinatura de solicitação para os dois certificados SSL novos, é necessários. Siga as etapas no artigo [geração de solicitação de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> Você pode ignorar esta etapa, dependendo de como você solicitou que os certificados SSL.

## <a name="validate-new-certificates"></a>Validar os novos certificados

1. Abra o PowerShell com permissão elevada no host de ciclo de vida do hardware ou a estação de trabalho de gerenciamento do Azure Stack.
2. Execute o seguinte cmdlet para instalar a ferramenta de verificador de preparação do Azure Stack.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Execute o script a seguir para criar a estrutura de pastas necessária:

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Se você implantar com o Azure Active Directory Federated Services (AD FS) os diretórios a seguir devem ser adicionados ao **$directories** no script: `ADFS`, `Graph`.

4. Execute os seguintes cmdlets para iniciar a verificação do certificado:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD -ExtensionHostFeature
    ```

5. Coloque os certificados nos diretórios apropriados.

6. Verifique a saída e todos os certificados passarem todos os testes.


## <a name="import-extension-host-certificates"></a>Importar certificados de host de extensão

Use um computador que possa se conectar ao ponto de extremidade do Azure Stack com privilégios para as próximas etapas. Verifique se que você tem acesso para os novos arquivos de certificado do computador em questão.

1. Use um computador que possa se conectar ao ponto de extremidade do Azure Stack com privilégios para as próximas etapas. Verifique se que você acessar para os novos arquivos de certificado do computador em questão.
2. Abra o ISE do PowerShell para executar os próximo blocos de script
3. Importe o certificado para hospedar o ponto de extremidade. Ajuste o script para corresponder ao seu ambiente.
4. Importe o certificado para o ponto de extremidade de hospedagem de administrador.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
5. Importe o certificado para o ponto de extremidade de hospedagem.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```



### <a name="update-dns-configuration"></a>Atualizar a configuração de DNS

> [!Note]  
> Essa etapa não será necessária se você tiver usado a delegação de zona DNS para a integração do DNS.
Se os registros de host individuais foram configurados para publicar pontos de extremidade do Azure Stack, você precisa criar dois registros de host adicionais:

| IP | Nome do host | Tipo |
|----|------------------------------|------|
| \<IP &GT; | Adminhosting. <Region>.<FQDN> | O  |
| \<IP &GT; | Hospedagem. <Region>.<FQDN> | O  |

IPs alocado pode ser recuperado usando o ponto de extremidade com privilégios executando o cmdlet **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Portas e protocolos

O artigo [integração de datacenter do Azure Stack – publicar pontos de extremidade](azure-stack-integrate-endpoints.md), aborda as portas e protocolos que exigem a comunicação de entrada para publicar o Azure Stack antes da distribuição de host de extensão.

### <a name="publish-new-endpoints"></a>Novos pontos de extremidade de publicação

Há dois novos pontos de extremidade necessários para ser publicado por meio de seu firewall. Os IPs alocado do pool de VIP público podem ser recuperados usando o cmdlet **Get-AzureStackStampInformation**.

> [!Note]  
> Faça essa alteração antes de habilitar o host de extensão. Isso permite que os portais do Azure Stack esteja continuamente acessível.

| Ponto de extremidade (VIP) | Protocolo | Portas |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Atualizar regras de publicação existentes (habilitação de postagem de host de extensão)

> [!Note]  
> O 1808 pacote de atualização de pilha do Azure faz **não** habilitar host de extensão ainda. Ele permite preparar para o host de extensão, importando os certificados necessários. Não feche todas as portas antes de host de extensão é habilitado automaticamente por meio de um pacote de atualização de pilha do Azure após a atualização 1808.

As seguintes portas de ponto de extremidade existentes devem ser fechadas nas regras de firewall existentes.

> [!Note]  
> É recomendável fechar essas portas após validação bem-sucedida.

| Ponto de extremidade (VIP) | Protocolo | Portas |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portal (administrador) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| Portal (usuário) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| O Azure Resource Manager (administrador) | HTTPS | 30024 |
| O Azure Resource Manager (usuário) | HTTPS | 30024 |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [integração do Firewall](azure-stack-firewall.md).
- Saiba mais sobre [geração de solicitação de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md)
