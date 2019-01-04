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
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 27fd02503881ef1f0587ccb0301f8490101d5bcb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720638"
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
  - Host de extensão de administração  
  - ACS (incluindo o armazenamento de fila, tabela e blob)  
  - ADFS *  
  - Gráfico *  

\* Aplicável somente se o provedor de identidade do ambiente é o Active Directory Federated Services (AD FS).

> [!Note]  
> Todos os outros proteger chaves e cadeias de caracteres, incluindo o BMC e alternar as senhas, as senhas de contas de usuário e administrador são atualizadas manualmente pelo administrador. 

> [!Note]  
> Começando com o lançamento do Azure da pilha 1811, rotação do segredo foi separada para certificados internos e externos. 

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

<sup>*</sup>Indica que as autoridades de certificação públicas são aqueles que são parte do programa de raiz confiável do Windows. Você pode encontrar a lista completa no artigo [Microsoft Trusted Root Certificate Program: Os participantes (a partir de 27 de junho de 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Correção de alertas

Quando os segredos são dentro de 30 dias de expiração, os seguintes alertas são gerados no Portal do administrador: 

- Expiração de senha da conta de serviço pendente 
- Expiração do certificado interno pendente 
- Expiração do certificado externo pendente 

Executar a rotação do segredo usando as instruções a seguir corrigirá esses alertas.

> [!Note]  
> Ambientes de pilha do Azure em versões pré-1811 poderão ver os alertas para o certificado interno ou expirações secretas pendentes. Esses alertas são imprecisos e devem ser ignorados sem executar a rotação secreta interna. Alertas de expiração de segredo interno imprecisos são um problema conhecido que é resolvido no 1811 – interno segredos não irá expirar, a menos que o ambiente esteve ativo por dois anos. 

## <a name="pre-steps-for-secret-rotation"></a>Pré-etapas para a rotação secreta

   > [!IMPORTANT]  
   > Se a rotação do segredo já foi executada em seu ambiente do Azure Stack, em seguida, você deve atualizar o sistema para versão 1811 ou posterior antes de executar novamente a rotação secreta. Rotação do segredo deve ser executada por o [ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) e exige credenciais de operador do Azure Stack. Se seu ambiente do Azure Stack es não souber se a rotação do segredo foi executada em seu ambiente, atualize o 1811 antes de executar a rotação secreta novamente.

1. É altamente recomendável que você atualize sua instância do Azure Stack para versão 1811.

    > [!Note] 
    > Para versões de pré-1811 não seja necessário girar segredos para adicionar certificados de host de extensão. Você deve seguir as instruções no artigo [preparar para o host de extensão para o Azure Stack](azure-stack-extension-host-prepare.md) para adicionar certificados de host de extensão.

2.  Operadores podem perceber alertas abrem e fecham automaticamente durante a rotação de segredos do Azure Stack.  Esse comportamento é esperado e os alertas podem ser ignorados.  Operadores podem verificar a validade desses alertas executando **AzureStack teste**.  Para operadores usando o SCOM para monitorar sistemas do Azure Stack, colocando um sistema no modo de manutenção impedirão que esses alertas de alcançar seus sistemas ITSM, mas continuarão a alertar se o sistema do Azure Stack torna-se inacessível. 
3. Notifique os usuários de qualquer operação de manutenção. Agende janelas de manutenção normal, tanto quanto possíveis, fora do horário comercial. Operações de manutenção podem afetar as cargas de trabalho do usuário e operações do portal.

    > [!Note]  
    > As próximas etapas se aplicam somente quando a rotação de segredos externos do Azure Stack.

4. Execute **[teste AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** e confirme se todas as saídas de teste estão íntegras antes de girar segredos.
5. Prepare um novo conjunto de substituição de certificados externos. O novo conjunto corresponde as especificações do certificado descritas as [requisitos de certificado PKI de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs). Você pode gerar um certificado (CSR) da solicitação de assinatura para compra ou criar novos certificados usando as etapas descritas em [gerar certificados de PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs) e prepará-los para uso em seu ambiente do Azure Stack usando as etapas [ Preparar certificados PKI do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs). Certifique-se de validar os certificados que você prepara com as etapas descritas em [validar certificados de PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs). 
6.  Store de um backup nos certificados usados para a rotação em um local de backup seguro. Se sua rotação é executado e, em seguida, falha, substitua os certificados no compartilhamento de arquivos com as cópias de backup antes de executar novamente a rotação. Observe, manter cópias de backup no local de backup seguro.
7.  Crie um compartilhamento de arquivos que você pode acessar as VMs ERCS. O compartilhamento de arquivos deve ser legível e gravável para o **CloudAdmin** identidade.
8.  Abra um console do PowerShell ISE de um computador em que você tem acesso ao compartilhamento de arquivos. Navegue até seu compartilhamento de arquivos. 
9.  Execute **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** para criar os diretórios necessários para seus certificados externos.

## <a name="rotating-external-secrets"></a>Girando segredos externos

Para girar segredos externos:

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
4. Aguarde enquanto os segredos girar. Rotação do segredo externa normalmente leva aproximadamente uma hora. Quando a rotação do segredo for concluída com êxito, o console exibirá **status da ação geral: Sucesso**. 
    > [!Note]  
    > Se a rotação secreta falhar, siga as instruções na mensagem de erro e executar novamente **início secretrotation** com o **-Rerun** parâmetro. 

    ```PowerShell  
    Start-SecretRotation -Rerun
    ```
    Contate o suporte se você enfrentar repetidas falhas de rotação do segredo.
5. Após a conclusão bem-sucedida da rotação do segredo, remover os certificados do compartilhamento criado na etapa pré e armazená-los em seu local de backup seguro. 

## <a name="walkthrough-of-secret-rotation"></a>Passo a passo de rotação do segredo

O exemplo de PowerShell a seguir demonstra os cmdlets e parâmetros para executar a fim de girar seus segredos.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Girando somente internos segredos

> [!Note]  
> Rotação do segredo interno só deve ser feita se você suspeitar de que um segredo interno foi comprometido por uma entidade mal-intencionada, ou se você receber um alerta (em compilação 1811 ou posterior), indicando que os certificados internos estão prestes a expirar. Ambientes de pilha do Azure em versões pré-1811 poderão ver os alertas para o certificado interno ou expirações secretas pendentes. Esses alertas são imprecisos e devem ser ignorados sem executar a rotação secreta interna. Alertas de expiração de segredo interno imprecisos são um problema conhecido que é resolvido no 1811 – interno segredos não irá expirar, a menos que o ambiente esteve ativo por dois anos.

Para girar apenas segredos internos do Azure Stack:

1. Criar uma sessão do PowerShell com o [ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Na sessão do ponto de extremidade com privilégios, execute **SecretRotation Start-interno**.

    > [!Note]  
    > Ambientes de pilha do Azure em versões pré-1811 não exigirá a **-interno** sinalizador. **Iniciar SecretRotation** girará somente internos segredos.

3. Aguarde enquanto os segredos girar.  
Quando a rotação do segredo for concluída com êxito, o console exibirá **status da ação geral: Sucesso**. 
    > [!Note]  
    > Se a rotação secreta falhar, siga as instruções na mensagem de erro e execute novamente **start-secretrotation** com o **– interno** e **-executar novamente** parâmetros.  

    ```PowerShell  
    Start-SecretRotation -Internal -Rerun
    ```
    Contate o suporte se você enfrentar repetidas falhas de rotação do segredo.

## <a name="start-secretrotation-reference"></a>Referência de SecretRotation de início

Gira os segredos de um sistema de pilha do Azure. Só é executada contra o ponto de extremidade do Azure Stack com privilégios.

### <a name="syntax"></a>Sintaxe

#### <a name="for-external-secret-rotation"></a>Para a rotação secreta externa

```Powershell  
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Para a rotação secreta interna 

```Powershell  
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Para rotação do segredo externa executar novamente 

```Powershell  
Start-SecretRotation [-Rerun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Para rotação secreta interna que executar novamente 

```Powershell  
Start-SecretRotation [-Rerun] [-Internal]
```
### <a name="description"></a>DESCRIÇÃO

O **SecretRotation início** cmdlet gira os segredos de infraestrutura de um sistema do Azure Stack. Por padrão, ele gira somente os certificados de todos os pontos de extremidade de infraestrutura de rede externa. Se usado com-sinalizador interno interno segredos de infraestrutura serão girados. Pontos de extremidade de infraestrutura de rede externa, para girar **Start-SecretRotation** deve ser executado com um **Invoke-Command** bloco de script com o ambiente do Azure Stack da privilegiado a sessão do ponto de extremidade passado como o parâmetro session.
 
### <a name="parameters"></a>parâmetros

| Parâmetro | Tipo | Obrigatório | Position | Padrão | DESCRIÇÃO |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Cadeia de caracteres  | Falso  | nomeado  | Nenhum  | O caminho de compartilhamento de arquivos para o **\Certificates** diretório contendo externa de todos os certificados de ponto de extremidade de rede. Só é necessário para girar segredos externos ou todos os segredos. Diretório de término deve estar **\Certificates**. |
| CertificatePassword | SecureString | Falso  | nomeado  | Nenhum  | A senha para todos os certificados fornecidos no PfXFilesPath. Valor obrigatório se PfxFilesPath é fornecida quando segredos internos e externos são girados. |
| Interna | Cadeia de caracteres | Falso | nomeado | Nenhum | Sinalizador interno deve ser usado sempre que um operador do Azure Stack deseja girar segredos de infraestrutura interna. |
| PathAccessCredential | PSCredential | Falso  | nomeado  | Nenhum  | A credencial do PowerShell para o compartilhamento de arquivos do **\Certificates** diretório contendo externa de todos os certificados de ponto de extremidade de rede. Só é necessário para girar segredos externos ou todos os segredos.  |
| Executar novamente | SwitchParameter | Falso  | nomeado  | Nenhum  | Execute novamente deve ser usado sempre que a rotação do segredo é uma nova tentativa após uma tentativa com falha. |

### <a name="examples"></a>Exemplos
 
#### <a name="rotate-only-internal-infrastructure-secrets"></a>Girar apenas os segredos de infraestrutura interna

Isso deve ser executado por meio do Azure Stack [ambiente do privilegiado ponto de extremidade](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```powershell  
PS C:\> Start-SecretRotation  -Internal
```

Esse comando gira todos os segredos de infra-estrutura expostos à rede interna do Azure Stack. 

#### <a name="rotate-only-external-infrastructure-secrets"></a>Girar apenas os segredos de infra-estrutura externo  

```powershell  
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock {  

Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }  

Remove-PSSession -Session $PEPSession
```

Esse comando gira os certificados TLS usados para pontos de extremidade de infraestrutura de rede externo do Azure Stack.   

**Girar os segredos de infraestrutura interna e externa**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Esse comando gira todos os segredos de infra-estrutura expostos à rede interna do Azure Stack, bem como os certificados TLS usados para pontos de extremidade de infraestrutura de rede externo do Azure Stack. Iniciar SecretRotation gira todos os segredos da pilha gerada, e porque recebem certificados, certificados de ponto de extremidade externo também serão girados.  


## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Atualizar a credencial do baseboard management controller (BMC)

Baseboard management controller (BMC) monitora o estado físico dos seus servidores. As especificações e instruções sobre como atualizar o nome da conta de usuário e a senha do BMC variam conforme o fornecedor de hardware do fabricante original do equipamento (OEM). Você deve atualizar suas senhas para os componentes do Azure Stack com regularidade.

1. Atualize o BMC em servidores físicos do Azure Stack, seguindo as instruções do OEM. O nome da conta de usuário e a senha para cada BMC em seu ambiente devem ser o mesmo.
2. Abra um ponto de extremidade com privilégios em sessões do Azure Stack. Para obter instruções, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).
3. Após seu PowerShell prompt foi alterado para **[endereço IP ou ERCS VM name]: PS >** ou a **[azs-ercs01]: PS >**, dependendo do ambiente, execute `Set-BmcCredemtial` executando `invoke-command`. Passe a variável de sessão com privilégios de ponto de extremidade como um parâmetro. Por exemplo: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBMCuser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BMCPassword is mandatory, while the BMCUser parameter is optional.
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Você também pode usar a versão do PowerShell estática com as senhas como linhas de código:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -Force
    $NewBMCuser = "<New BMC User name>" 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a segurança do Azure Stack](azure-stack-security-foundations.md)
