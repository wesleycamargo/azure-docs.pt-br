---
title: Girar os segredos no Azure Stack | Microsoft Docs
description: Saiba como girar seus segredos no Azure Stack.
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
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cc7b1b9e96e32b090c0ec9ec9ab029588e5ec4ce
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166960"
---
# <a name="rotate-secrets-in-azure-stack"></a>Girar os segredos no Azure Stack

*Essas instruções se aplicam somente a pilha do Azure integrado sistemas versão 1803 e posterior. Não tente rotação do segredo em versões de pilha do Azure anteriores à 1802*

Pilha do Azure usa vários segredos para manter uma comunicação segura entre os serviços e recursos de infraestrutura do Azure Stack.

- **Segredos internos**  
Todos os os certificados, senhas, cadeias de caracteres seguras e as chaves usadas pela infraestrutura do Azure Stack sem a intervenção do operador do Azure Stack. 

- **Segredos externos**  
Certificados de serviço de infraestrutura para serviços de face externa que são fornecidos pelo operador de pilha do Azure. Isso inclui os certificados para os seguintes serviços: 
    - Portal do administrador 
    - Portal público 
    - Administrador do Azure Resource Manager 
    - Global do Azure Resource Manager 
    - Cofre de chaves de administrador 
    - Keyvault 
    - ACS (incluindo o armazenamento de fila, tabela e blob) 
    - AD FS<sup>*</sup>
    - Gráfico<sup>*</sup>

   <sup>*</sup> Aplicável somente se o provedor de identidade do ambiente é o Active Directory Federated Services (AD FS).

> [!NOTE]
> Todos os outros proteger chaves e cadeias de caracteres, incluindo o BMC e alternar as senhas, as senhas de contas de usuário e administrador são atualizadas manualmente pelo administrador. 

Para manter a integridade da infraestrutura do Azure Stack, operadores precisam ser capazes de girar periodicamente os segredos da sua infra-estrutura em frequências que são consistentes com os requisitos de segurança de sua organização.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Girando segredos com certificados externos de uma nova autoridade de certificação

O Azure Stack oferece suporte a rotação secreta com certificados externos de um novo (autoridade de certificação) nos seguintes contextos:

|Certificação instalada|Autoridade de certificação para girar|Com suporte|O Azure Stack versões com suporte|
|-----|-----|-----|-----|
|De autoassinado|Para o Enterprise|Sem suporte||
|De autoassinado|Para autoassinado|Sem suporte||
|De autoassinado|Para o público<sup>*</sup>|Com suporte|1803 & mais tarde|
|Da empresa|Para o Enterprise|Suporte desde que os clientes usam a mesma autoridade de certificação como usados na implantação corporativa|1803 & mais tarde|
|Da empresa|Para autoassinado|Sem suporte||
|Da empresa|Para o público<sup>*</sup>|Com suporte|1803 & mais tarde|
|Do público<sup>*</sup>|Para o Enterprise|Sem suporte|1803 & mais tarde|
|Do público<sup>*</sup>|Para autoassinado|Sem suporte||
|Do público<sup>*</sup>|Para o público<sup>*</sup>|Com suporte|1803 & mais tarde|

<sup>*</sup> Aqui a autoridades de certificação públicas são aqueles que são parte do programa de raiz confiável do Windows. Você pode encontrar a lista completa [programa de certificado de raiz confiável do Microsoft: participantes (a partir de 27 de junho de 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Correção de alertas

Quando os segredos são dentro de 30 dias de expiração, os seguintes alertas são gerados no Portal do administrador: 

- Expiração de senha da conta de serviço pendente 
- Expiração do certificado interno pendente 
- Expiração do certificado externo pendente 

Executar a rotação do segredo usando as instruções a seguir corrigirá esses alertas.

## <a name="pre-steps-for-secret-rotation"></a>Pré-etapas para a rotação secreta

   > [!IMPORTANT]  
   > Certifique-se de rotação do segredo ainda não foi executada com êxito em seu ambiente. Se a rotação do segredo já foi executada, atualização do Azure Stack para versão 1807 ou posterior antes de executar a rotação do segredo. 
1.  Operadores podem perceber alertas abrem e fecham automaticamente durante a rotação de segredos do Azure Stack.  Esse comportamento é esperado e os alertas podem ser ignorados.  Operadores podem verificar a validade desses alertas executando o teste AzureStack.  Para operadores usando o SCOM para monitorar sistemas do Azure Stack, colocando um sistema no modo de manutenção impedirão que esses alertas de alcançar seus sistemas ITSM, mas continuarão a alertar se o sistema do Azure Stack torna-se inacessível. 
2. Notifique os usuários de qualquer operação de manutenção. Agende janelas de manutenção normal, tanto quanto possíveis, fora do horário comercial. Operações de manutenção podem afetar as cargas de trabalho do usuário e operações do portal.
    > [!note]  
    > As próximas etapas se aplicam somente quando a rotação de segredos externos do Azure Stack.
3. Prepare um novo conjunto de substituição de certificados externos. O novo conjunto corresponde as especificações do certificado descritas as [requisitos de certificado PKI de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
4.  Store de um backup nos certificados usados para a rotação em um local de backup seguro. Se sua rotação é executado e, em seguida, falha, substitua os certificados no compartilhamento de arquivos com as cópias de backup antes de executar novamente a rotação. Observe, manter cópias de backup no local de backup seguro.
5.  Crie um compartilhamento de arquivos que você pode acessar as VMs ERCS. O compartilhamento de arquivos deve ser legível e gravável para o **CloudAdmin** identidade.
6.  Abra um console do PowerShell ISE de um computador em que você tem acesso ao compartilhamento de arquivos. Navegue até seu compartilhamento de arquivos. 
7.  Execute **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** para criar os diretórios necessários para seus certificados externos.

## <a name="rotating-external-and-internal-secrets"></a>Girando segredos internos e externos

Para girar os dois externo um segredo interno:

1. Dentro do recém-criado **/certificados** diretório criado nas pré-etapas a, coloque o novo conjunto de certificados externos de substituição na estrutura de diretórios de acordo com o formato descrito na seção certificados obrigatórios dos [requisitos de certificado PKI de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Criar uma sessão do PowerShell com o [ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) usando o **CloudAdmin** da conta e armazenar as sessões como uma variável. Você usará essa variável como o parâmetro na próxima etapa.

    > [!IMPORTANT]  
    > Não insira a sessão, armazene a sessão como uma variável.
    
3. Execute  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Passe a variável de sessão do PowerShell com privilégios de ponto de extremidade como o **sessão** parâmetro. 
4. Execute **SecretRotation início** com os seguintes parâmetros:
    - **PfxFilesPath**  
    Especifique o caminho de rede para seu diretório de certificados que criou anteriormente.  
    - **PathAccessCredential**  
    Um objeto PSCredential, as credenciais para o compartilhamento. 
    - **CertificatePassword**  
    Uma cadeia de caracteres da senha usada para todos os arquivos de certificado pfx criados.
4. Aguarde enquanto os segredos girar.  
Quando a rotação do segredo for concluída com êxito, o console exibirá **status da ação geral: êxito**. 
    > [!note]  
    > Se a rotação secreta falhar, siga as instruções na mensagem de erro e execute novamente o start-secretrotation com o **-Rerun** parâmetro. Contate o suporte se você enfrentar repetidas falhas de rotação do segredo. 
5. Após a conclusão bem-sucedida da rotação do segredo, remover os certificados do compartilhamento criado na etapa pré e armazená-los em seu local de backup seguro. 

## <a name="walkthrough-of-secret-rotation"></a>Passo a passo de rotação do segredo

O exemplo de PowerShell a seguir demonstra os cmdlets e parâmetros para executar a fim de girar seus segredos.

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
## <a name="rotating-only-internal-secrets"></a>Girando somente internos segredos

Para girar apenas segredos internos do Azure Stack:

1. Criar uma sessão do PowerShell com o [ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Na sessão do ponto de extremidade com privilégios, execute **SecretRotation início** sem argumentos.
3. Aguarde enquanto os segredos girar.  
Quando a rotação do segredo for concluída com êxito, o console exibirá **status da ação geral: êxito**. 
    > [!note]  
    > Se a rotação secreta falhar, siga as instruções na mensagem de erro e execute novamente o start-secretrotation com o **-executar novamente** parâmetro. Contate o suporte se você enfrentar repetidas falhas de rotação do segredo. 

## <a name="start-secretrotation-reference"></a>Referência de SecretRotation de início

Gira os segredos de um sistema de pilha do Azure. Só é executada contra o ponto de extremidade do Azure Stack com privilégios.

### <a name="syntax"></a>Sintaxe

Caminho (padrão)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>DESCRIÇÃO

O cmdlet Start-SecretRotation gira os segredos de infraestrutura de um sistema do Azure Stack. Por padrão que ele gira todos os segredos expostos à rede de infraestrutura interna, com a entrada do usuário-lo também gira os certificados de todos os pontos de extremidade de infraestrutura de rede externa. Pontos de extremidade de infraestrutura de rede externa para girar, SecretRotation de início deve ser executado por meio de um bloco de script Invoke-Command com a sessão do ponto de extremidade com privilégios do ambiente do Azure Stack passado como o parâmetro session.
 
### <a name="parameters"></a>parâmetros

| Parâmetro | Tipo | Obrigatório | Position | Padrão | DESCRIÇÃO |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Cadeia de caracteres  | Falso  | nomeado  | Nenhum  | O caminho de compartilhamento de arquivos para o **\Certificates** diretório contendo externa de todos os certificados de ponto de extremidade de rede. Só é necessário para girar segredos externos ou todos os segredos. Diretório de término deve estar **\Certificates**. |
| CertificatePassword | SecureString | Falso  | nomeado  | Nenhum  | A senha para todos os certificados fornecidos no PfXFilesPath. Valor obrigatório se PfxFilesPath é fornecida quando segredos internos e externos são girados. |
| PathAccessCredential | PSCredential | Falso  | nomeado  | Nenhum  | A credencial do PowerShell para o compartilhamento de arquivos do **\Certificates** diretório contendo externa de todos os certificados de ponto de extremidade de rede. Só é necessário para girar segredos externos ou todos os segredos.  |
| Executar novamente | SwitchParameter | Falso  | nomeado  | Nenhum  | Execute novamente deve ser usado sempre que a rotação do segredo é tentada novamente após uma tentativa com falha. |

### <a name="examples"></a>Exemplos
 
**Girar apenas os segredos de infraestrutura interna**

```powershell  
PS C:\> Start-SecretRotation  
```

Esse comando gira todos os segredos de infra-estrutura expostos à rede interna do Azure Stack. Iniciar SecretRotation gira todos os segredos da pilha gerada, mas como não há nenhum certificado fornecido, os certificados de ponto de extremidade externo não serão girados.  

**Girar os segredos de infraestrutura interna e externa**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Esse comando gira todos os segredos de infra-estrutura expostos à rede interna do Azure Stack, bem como os certificados TLS usados para pontos de extremidade de infraestrutura de rede externo do Azure Stack. Iniciar SecretRotation gira todos os segredos da pilha gerada, e porque recebem certificados, certificados de ponto de extremidade externo também serão girados.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Atualizar a senha do baseboard management controller (BMC)

Baseboard management controller (BMC) monitora o estado físico dos seus servidores. As especificações e instruções sobre como atualizar a senha do BMC variam conforme o fornecedor de hardware do fabricante original do equipamento (OEM). Você deve atualizar suas senhas para os componentes do Azure Stack em uma cadência regular.

1. Atualize o BMC em servidores físicos do Azure da pilha, seguindo as instruções do OEM. A senha para cada BMC em seu ambiente deve ser o mesmo.
2. Abra um ponto de extremidade com privilégios em sessões de pilha do Azure. Para obter instruções, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).
3. Após seu PowerShell prompt foi alterado para **[endereço IP ou ERCS VM name]: PS >** ou a **[azs-ercs01]: PS >**, dependendo do ambiente, execute `Set-BmcPassword` executando `invoke-command`. Passe a variável de sessão com privilégios de ponto de extremidade como um parâmetro. Por exemplo: 

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

[Saiba mais sobre a segurança do Azure Stack](azure-stack-security-foundations.md)
