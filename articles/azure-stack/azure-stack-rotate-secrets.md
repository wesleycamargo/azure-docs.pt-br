---
title: Girar segredos na pilha do Azure | Microsoft Docs
description: Saiba como girar seus segredos na pilha do Azure.
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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a3dfce6ce1b136e39047cfd47b336b2fb2a35af9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Girar segredos na pilha do Azure

*Essas instruções se aplicam somente ao Azure pilha integrado sistemas versão 1803 e mais tarde. Não tente secreta rotação em versões de pilha do pré-1802 do Azure*

Pilha do Azure usa vários segredos para manter a comunicação segura entre os recursos de infraestrutura de pilha do Azure e serviços.

- **Segredos internos**  
Todos os os certificados, senhas, cadeias de caracteres seguras e as chaves usadas pela infraestrutura de pilha do Azure sem a intervenção do operador de pilha do Azure. 

- **Segredos externos**  
Certificados de serviço de infraestrutura de serviços externo que são fornecidos pelo operador de pilha do Azure. Isso inclui os certificados para os seguintes serviços: 
    - Portal do administrador 
    - Portal público 
    - Gerenciador de recursos do Azure do administrador 
    - Gerenciador de recursos global do Azure 
    - Administrador Keyvault 
    - Keyvault 
    - ACS (incluindo o blob, tabela e o armazenamento de fila) 
    - ADFS<sup>*</sup>
    - Gráfico<sup>*</sup>

    > <sup>*</sup> Aplicável somente se o provedor de identidade do ambiente Active Directory Federated Services (AD FS).

> [!NOTE]
> Todos os outros proteger as chaves e cadeias de caracteres, incluindo o BMC e alternar senhas, senhas de conta de usuário e administrador são atualizadas manualmente pelo administrador. 

Para manter a integridade da infra-estrutura da pilha do Azure, operadores precisam da capacidade para girar periodicamente os segredos da sua infraestrutura em frequências que são consistentes com os requisitos de segurança da sua organização.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Girando segredos com certificados externos de um novo autoridade de certificação

A pilha do Azure dá suporte a rotação secreta com certificados externos de um novo (autoridade de certificação) nos seguintes contextos:

|Certificação instalada|Autoridade de certificação para girar para|Com suporte|Versões de pilha do Azure com suporte|
|-----|-----|-----|-----|-----|
|De autoassinado|A empresa|Sem suporte||
|De autoassinado|Para autoassinado|Sem suporte||
|De autoassinado|Para o público<sup>*</sup>|Com suporte|1803 & posterior|
|Da empresa|A empresa|Com suporte, contanto que os clientes usam a mesma empresa conforme usado na implantação da autoridade de certificação|1803 & posterior|
|Da empresa|Para autoassinado|Sem suporte||
|Da empresa|Para o público<sup>*</sup>|Com suporte|1803 & posterior|
|Do público<sup>*</sup>|A empresa|Sem suporte|1803 & posterior|
|Do público<sup>*</sup>|Para autoassinado|Sem suporte||
|Do público<sup>*</sup>|Para o público<sup>*</sup>|Com suporte|1803 & posterior|

<sup>*</sup> Aqui as autoridades de certificação pública são aqueles que fazem parte do programa de raiz confiável do Windows. Você pode encontrar a lista completa de [Microsoft Trusted Root Certificate Program: participantes (a partir de 27 de junho de 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Correção de alerta

Quando os segredos são dentro de 30 dias de expiração, os seguintes alertas são gerados no Portal do administrador: 

- Expiração de senha da conta de serviço pendente 
- Expiração do certificado interno pendente 
- Expiração do certificado externo pendente 

Executar o segredo rotação usando as instruções a seguir corrigirá esses alertas.

## <a name="pre-steps-for-secret-rotation"></a>Pré-etapas para rotação secreta

1.  Notifique os usuários de qualquer operação de manutenção. Agende as janelas de manutenção normal, tanto quanto possíveis, fora do horário comercial. As operações de manutenção podem afetar as operações de portal e cargas de trabalho do usuário.

    > [!note]  
    > As próximas etapas se aplicam somente quando a rotação de segredos externos da pilha do Azure.

2.  Prepare um novo conjunto de substituição de certificados externos. O novo conjunto coincide com as especificações de certificado apresentadas no [requisitos de certificado PKI de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
3.  Armazene um backup de até os certificados usados para rotação em um local de backup seguro. Se a rotação é executado e, em seguida, falha, substitua os certificados no compartilhamento de arquivos com as cópias de backup antes de executar novamente a rotação. Observe, manter cópias de backup no local de backup seguro.
3.  Crie um compartilhamento de arquivos que você pode acessar de VMs ERCS. O compartilhamento de arquivos deve ser legível e gravável para o **CloudAdmin** identidade.
4.  Abra um console do ISE do PowerShell em um computador em que você tem acesso ao compartilhamento de arquivos. Navegue até o compartilhamento de arquivos. 
5.  Executar **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** para criar os diretórios necessários para seus certificados externos.

## <a name="rotating-external-and-internal-secrets"></a>Girando segredos internos e externos

Para girar ambos externo um segredo interno:

1. Dentro do recém-criado **/certificados** diretório criado nas etapas anteriores ao, colocar o novo conjunto de certificados externos de substituição da estrutura de diretórios de acordo com o formato descrito na seção certificados obrigatório do [requisitos de certificado PKI de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Criar uma sessão do PowerShell com a [privilegiada do ponto de extremidade](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) usando o **CloudAdmin** conta e armazenar as sessões como uma variável. Você usará essa variável como o parâmetro na próxima etapa.

    > [!IMPORTANT]  
    > Não insira a sessão, armazenar a sessão como uma variável.
    
3. Executar  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Passar a variável de sessão do PowerShell com privilégios de ponto de extremidade como o **sessão** parâmetro. 
4. Executar **SecretRotation início** com os seguintes parâmetros:
    - **PfxFilesPath**  
    Especifique o caminho de rede para o diretório de certificados criado anteriormente.  
    - **PathAccessCredential**  
    Um objeto PSCredential credenciais para o compartilhamento. 
    - **CertificatePassword**  
    Uma cadeia de caracteres da senha usada para todos os arquivos de certificado pfx criados.
4. Aguarde enquanto sua segredos girar.  
Quando a rotação secreta for concluído com êxito, o console exibirá **status geral da ação: êxito**. 
5. Após a conclusão bem-sucedida da rotação secreta, remover os certificados do compartilhamento criado na etapa anterior ao e armazená-los em seu local de backup seguro. 

## <a name="walkthrough-of-secret-rotation"></a>Instruções passo a passo de rotação secreta

O exemplo a seguir do PowerShell demonstra os cmdlets e parâmetros para executar a fim de girar os segredos.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Somente internos segredos de rotação

Para girar apenas segredos internos da pilha do Azure:

1. Criar uma sessão do PowerShell com a [privilegiada do ponto de extremidade](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Na sessão do ponto de extremidade com privilégios, execute **SecretRotation início** sem argumentos.

## <a name="start-secretrotation-reference"></a>Referência de SecretRotation de início

Gira os segredos de um sistema de pilha do Azure. Só é executado no Azure pilha privilegiado ponto de extremidade.

### <a name="syntax"></a>Sintaxe

Caminho (padrão)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>DESCRIÇÃO

O cmdlet Start-SecretRotation gira os segredos de infraestrutura de um sistema de pilha do Azure. Por padrão que ele gira todos os segredos expostos na rede de infraestrutura interna, com a entrada do usuário ele também gira os certificados de todos os pontos de extremidade de infraestrutura de rede externa. Quando a rotação de pontos de extremidade de infraestrutura de rede externa, início SecretRotation deve ser executado por meio de um bloco de script Invoke-Command com a sessão do ponto de extremidade com privilégios do ambiente Azure pilha passado como o parâmetro de sessão.
 
### <a name="parameters"></a>parâmetros

| Parâmetro | type | Obrigatório | Position | Padrão | DESCRIÇÃO |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Cadeia de caracteres  | Falso  | nomeado  | Nenhum  | O caminho de compartilhamento de arquivos para o **\Certificates** diretório contendo externa de todos os certificados do ponto de extremidade de rede. Necessário apenas quando a rotação de segredos internos e externos. Diretório de término deve ser **\Certificates**. |
| CertificatePassword | SecureString | Falso  | nomeado  | Nenhum  | A senha para todos os certificados fornecidas a PfXFilesPath. Valor obrigatório se PfxFilesPath é fornecida quando segredos internos e externos são girados. |
|

### <a name="examples"></a>Exemplos
 
**Girar apenas os segredos de infraestrutura interna**

```powershell  
PS C:\> Start-SecretRotation  
```

Este comando gira todos os segredos de infraestrutura expostos à rede interna da pilha do Azure. Iniciar SecretRotation gira todos os segredos da pilha gerada, mas como não há nenhum certificado fornecido, certificados de ponto de extremidade externo não serão girados.  

**Girar os segredos de infraestrutura interna e externa**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Este comando gira todos os segredos de infraestrutura expostos à rede interna da pilha do Azure, bem como os certificados TLS usados para pontos de extremidade de infraestrutura de rede externa da pilha do Azure. Iniciar SecretRotation gira todos os segredos da pilha gerada e porque recebem certificados, certificados de ponto de extremidade externo também serão girados.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Atualize a senha do controlador BMC de gerenciamento BMC

Baseboard management controller (BMC) monitora o estado físico dos seus servidores. As especificações e instruções sobre como atualizar a senha do BMC variam com base no seu fornecedor de hardware do fabricante (OEM). Você deve atualizar as senhas para os componentes da pilha do Azure em um ritmo regular.

1. Atualize o BMC em servidores físicos da pilha do Azure seguindo as instruções do OEM. A senha para cada BMC em seu ambiente deve ser o mesmo.
2. Abra um ponto de extremidade com privilégios em sessões de pilha do Azure. Para obter instruções, consulte [usando o ponto de extremidade com privilégios na pilha do Azure](azure-stack-privileged-endpoint.md).
3. Após o PowerShell prompt foi alterado para **[endereço IP ou ERCS VM name]: PS >** ou **[azs ercs01]: PS >**, dependendo do ambiente, execute `Set-BmcPassword` executando `invoke-command`. Passe a variável de sessão com privilégios de ponto de extremidade como um parâmetro. Por exemplo: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Você também pode usar a versão do PowerShell estática com as senhas como linhas de código:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a segurança da pilha do Azure](azure-stack-security-foundations.md)
