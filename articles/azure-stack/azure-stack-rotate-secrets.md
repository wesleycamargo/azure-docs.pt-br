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
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 54bc6bc105dab2831df6e48a64a6f766582a3fb9
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917553"
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
- KeyVault
- Host de extensão de administração
- ACS (incluindo o armazenamento de fila, tabela e blob)
- ADFS *
- Gráfico *

\* Aplicável somente se o provedor de identidade do ambiente é o Active Directory Federated Services (AD FS).

> [!Note]
> Todos os outros proteger chaves e cadeias de caracteres, incluindo o BMC e alternar as senhas, as senhas de contas de usuário e administrador são atualizadas manualmente pelo administrador.

> [!Important]
> Começando com o lançamento do Azure da pilha 1811, rotação do segredo foi separada para certificados internos e externos.

Para manter a integridade da infraestrutura do Azure Stack, operadores precisam ser capazes de girar periodicamente os segredos da sua infra-estrutura em frequências que são consistentes com os requisitos de segurança de sua organização.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Girando segredos com certificados externos de uma nova autoridade de certificação

O Azure Stack oferece suporte a rotação secreta com certificados externos de um novo (autoridade de certificação) nos seguintes contextos:

|Certificação instalada|Autoridade de certificação para girar|Com suporte|O Azure Stack versões com suporte|
|-----|-----|-----|-----|
|De autoassinado|To Enterprise|Sem suporte||
|De autoassinado|Para autoassinado|Sem suporte||
|De autoassinado|Para o público<sup>*</sup>|Com suporte|1803 & mais tarde|
|Da empresa|To Enterprise|Suporte desde que os clientes usam a mesma autoridade de certificação como usados na implantação corporativa|1803 & mais tarde|
|Da empresa|Para autoassinado|Sem suporte||
|Da empresa|Para o público<sup>*</sup>|Com suporte|1803 & mais tarde|
|Do público<sup>*</sup>|To Enterprise|Sem suporte|1803 & mais tarde|
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
> Ambientes de pilha do Azure em versões pré-1811 poderão ver os alertas para o certificado interno ou expirações secretas pendentes.
> Esses alertas são imprecisos e devem ser ignorados sem executar a rotação secreta interna.
> Alertas de expiração de segredo interno imprecisos são um problema conhecido que é resolvido no 1811 – interno segredos não irá expirar, a menos que o ambiente esteve ativo por dois anos.

## <a name="pre-steps-for-secret-rotation"></a>Pré-etapas para a rotação secreta

   > [!IMPORTANT]
   > Se a rotação do segredo já foi executada em seu ambiente do Azure Stack, em seguida, você deve atualizar o sistema para versão 1811 ou posterior antes de executar novamente a rotação secreta.
   > Rotação do segredo deve ser executada por o [ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) e exige credenciais de operador do Azure Stack.
   > Se seu ambiente do Azure Stack es não souber se a rotação do segredo foi executada em seu ambiente, atualize o 1811 antes de executar a rotação secreta novamente.

1. É altamente recomendável que você atualize sua instância do Azure Stack para versão 1811.

    > [!Note] 
    > Para versões de pré-1811 não seja necessário girar segredos para adicionar certificados de host de extensão. Você deve seguir as instruções no artigo [preparar para o host de extensão para o Azure Stack](azure-stack-extension-host-prepare.md) para adicionar certificados de host de extensão.

2. Os operadores podem perceber que alertas aparecem e desaparecem automaticamente durante a rotação de segredos do Azure Stack.  Esse comportamento é esperado e os alertas podem ser ignorados.  Operadores podem verificar a validade desses alertas executando **AzureStack teste**.  Para operadores usando o SCOM para monitorar sistemas do Azure Stack, colocando um sistema no modo de manutenção impedirão que esses alertas de alcançar seus sistemas ITSM, mas continuarão a alertar se o sistema do Azure Stack torna-se inacessível.

3. Notifique os usuários de qualquer operação de manutenção. Agende janelas de manutenção normal, tanto quanto possíveis, fora do horário comercial. Operações de manutenção podem afetar as cargas de trabalho do usuário e operações do portal.

    > [!Note]
    > As próximas etapas se aplicam somente quando a rotação de segredos externos do Azure Stack.

4. Execute **[teste AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** e confirme se todas as saídas de teste estão íntegras antes de girar segredos.
5. Prepare um novo conjunto de substituição de certificados externos. O novo conjunto corresponde as especificações do certificado descritas as [requisitos de certificado PKI de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs). Você pode gerar um certificado (CSR) da solicitação de assinatura para compra ou criar novos certificados usando as etapas descritas em [gerar certificados de PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs) e prepará-los para uso em seu ambiente do Azure Stack usando as etapas [ Preparar certificados PKI do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs). Certifique-se de validar os certificados que você prepara com as etapas descritas em [validar certificados de PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs).
6. Store de um backup nos certificados usados para a rotação em um local de backup seguro. Se sua rotação é executado e, em seguida, falha, substitua os certificados no compartilhamento de arquivos com as cópias de backup antes de executar novamente a rotação. Observe, manter cópias de backup no local de backup seguro.
7. Crie um compartilhamento de arquivos que você pode acessar as VMs ERCS. O compartilhamento de arquivos deve ser legível e gravável para o **CloudAdmin** identidade.
8. Abra um console do PowerShell ISE de um computador em que você tem acesso ao compartilhamento de arquivos. Navegue até seu compartilhamento de arquivos.
9. Execute **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** para criar os diretórios necessários para seus certificados externos.

> [!IMPORTANT]
> O script CertDirectoryMaker criará uma estrutura de pastas que irão aderir a:
>
> **.\Certificates\AAD** ou ***.\Certificates\ADFS*** dependendo do seu provedor de identidade usado para o Azure Stack
>
> Ele é de extrema importância que a estrutura de pastas termina com **AAD** ou **ADFS** pastas e todos os subdiretórios são nessa estrutura; caso contrário, **Start-SecretRotation**virá com:
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Como você pode ver a mensagem de erro indicaria que há um problema ao acessar o compartilhamento de arquivos, mas na realidade, é a estrutura de pasta que está sendo imposta aqui.
> Mais informações podem ser encontradas no verificador de preparação para o Microsoft AzureStack - [PublicCertHelper módulo](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)
>
> Também é importante que a estrutura de pastas de compartilhamento de arquivos começa com **certificados** pasta caso contrário, também haverá falha na validação.
> Montagem do compartilhamento de arquivos deve ter aparência **\\ \\ \<IPAddress >\\\<ShareName >\\** e ela deve conter uma pasta  **Certificates\AAD** ou **Certificates\ADFS** dentro.
>
> Por exemplo: 
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>Girando segredos externos

Para girar segredos externos:

1. Dentro do recém-criado **\Certificates\\\<IdentityProvider >** diretório criado nas pré-etapas a, coloque o novo conjunto de certificados externos de substituição na estrutura de diretórios de acordo com o formato descrito na seção de certificados obrigatórios a [requisitos de certificado PKI de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).

    Exemplo de estrutura de pasta para o provedor de identidade do AAD:
    ```powershell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. Criar uma sessão do PowerShell com o [ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) usando o **CloudAdmin** da conta e armazenar as sessões como uma variável. Você usará essa variável como o parâmetro na próxima etapa.

    > [!IMPORTANT]  
    > Não insira a sessão, armazene a sessão como uma variável.

3. Execute  **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)**. Passe a variável de sessão do PowerShell com privilégios de ponto de extremidade como o **sessão** parâmetro.

4. Execute **SecretRotation início** com os seguintes parâmetros:
    - **PfxFilesPath**  
    Especifique o caminho de rede para seu diretório de certificados que criou anteriormente.  
    - **PathAccessCredential**  
    Um objeto PSCredential, as credenciais para o compartilhamento.
    - **CertificatePassword**  
    Uma cadeia de caracteres da senha usada para todos os arquivos de certificado pfx criados.

5. Aguarde enquanto os segredos girar. Rotação do segredo externa normalmente leva aproximadamente uma hora.

    Quando a rotação do segredo for concluída com êxito, o console exibirá **status da ação geral: Sucesso**.

    > [!Note]
    > Se a rotação secreta falhar, siga as instruções na mensagem de erro e executar novamente **início SecretRotation** com o **-ReRun** parâmetro.

    ```powershell
    Start-SecretRotation -ReRun
    ```
    Contate o suporte se você enfrentar repetidas falhas de rotação do segredo.

6. Após a conclusão bem-sucedida da rotação do segredo, remover os certificados do compartilhamento criado na etapa pré e armazená-los em seu local de backup seguro.

## <a name="walkthrough-of-secret-rotation"></a>Passo a passo de rotação do segredo

O exemplo de PowerShell a seguir demonstra os cmdlets e parâmetros para executar a fim de girar seus segredos.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Girando somente internos segredos

> [!Note]
> Rotação do segredo interno só deve ser feita se você suspeitar de que um segredo interno foi comprometido por uma entidade mal-intencionada, ou se você receber um alerta (em compilação 1811 ou posterior), indicando que os certificados internos estão prestes a expirar.
> Ambientes de pilha do Azure em versões pré-1811 poderão ver os alertas para o certificado interno ou expirações secretas pendentes.
> Esses alertas são imprecisos e devem ser ignorados sem executar a rotação secreta interna.
> Alertas de expiração de segredo interno imprecisos são um problema conhecido que é resolvido no 1811 – interno segredos não irá expirar, a menos que o ambiente esteve ativo por dois anos.

1. Criar uma sessão do PowerShell com o [ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Na sessão do ponto de extremidade com privilégios, execute **SecretRotation Start-interno**.

    > [!Note]
    > Ambientes de pilha do Azure em versões pré-1811 não exigirá a **-interno** sinalizador. **Iniciar SecretRotation** girará somente internos segredos.

3. Aguarde enquanto os segredos girar.

Quando a rotação do segredo for concluída com êxito, o console exibirá **status da ação geral: Sucesso**.
    > [!Note]
    > If secret rotation fails, follow the instructions in the error message and rerun **Start-SecretRotation** with the  **–Internal** and **-ReRun** parameters.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Contate o suporte se você enfrentar repetidas falhas de rotação do segredo.

## <a name="start-secretrotation-reference"></a>Referência de SecretRotation de início

Gira os segredos de um sistema de pilha do Azure. Só é executada contra o ponto de extremidade do Azure Stack com privilégios.

### <a name="syntax"></a>Sintaxe

#### <a name="for-external-secret-rotation"></a>Para a rotação secreta externa

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Para a rotação secreta interna

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Para rotação do segredo externa executar novamente

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Para rotação secreta interna que executar novamente

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>DESCRIÇÃO

O **SecretRotation início** cmdlet gira os segredos de infraestrutura de um sistema do Azure Stack. Por padrão, ele gira somente os certificados de todos os pontos de extremidade de infraestrutura de rede externa. Se usado com-sinalizador interno interno segredos de infraestrutura serão girados. Pontos de extremidade de infraestrutura de rede externa, para girar **Start-SecretRotation** deve ser executado com um **Invoke-Command** bloco de script com o ambiente do Azure Stack da privilegiado a sessão do ponto de extremidade passado como o **sessão** parâmetro.

### <a name="parameters"></a>parâmetros

| Parâmetro | Type | Obrigatório | Position | Padrão | DESCRIÇÃO |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Cadeia de caracteres  | Falso  | nomeado  | Nenhum  | O caminho de compartilhamento de arquivos para o **\Certificates** diretório contendo externa de todos os certificados de ponto de extremidade de rede. Necessário apenas para segredos externos para girar. Diretório de término deve estar **\Certificates**. |
| CertificatePassword | SecureString | Falso  | nomeado  | Nenhum  | A senha para todos os certificados fornecidos no PfXFilesPath. Valor obrigatório se PfxFilesPath é fornecida quando segredos externos são girados. |
| Interna | Cadeia de caracteres | Falso | nomeado | Nenhum | Sinalizador interno deve ser usado sempre que um operador do Azure Stack deseja girar segredos de infraestrutura interna. |
| PathAccessCredential | PSCredential | Falso  | nomeado  | Nenhum  | A credencial do PowerShell para o compartilhamento de arquivos do **\Certificates** diretório contendo externa de todos os certificados de ponto de extremidade de rede. Necessário apenas para segredos externos para girar.  |
| ReRun | SwitchParameter | Falso  | nomeado  | Nenhum  | Execute novamente deve ser usado sempre que a rotação do segredo é uma nova tentativa após uma tentativa com falha. |

### <a name="examples"></a>Exemplos

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Girar apenas os segredos de infraestrutura interna

Isso deve ser executado por meio do Azure Stack [ambiente do privilegiado ponto de extremidade](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```powershell
PS C:\> Start-SecretRotation -Internal
```

Esse comando gira todos os segredos de infra-estrutura expostos à rede interna do Azure Stack.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Girar apenas os segredos de infra-estrutura externo  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Esse comando gira os certificados TLS usados para pontos de extremidade de infraestrutura de rede externo do Azure Stack.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Girar os segredos de infraestrutura interna e externa (**pré-1811** apenas)

> [!IMPORTANT]
> Esse comando se aplica somente ao Azure Stack **pré-1811** conforme a rotação foi dividida para certificados internos e externos.
>
> **Partir *1811 +* não é possível girar ambos interno e externo certificados mais!!!**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Esse comando gira todos os segredos de infra-estrutura expostos à rede interna do Azure Stack, bem como os certificados TLS usados para pontos de extremidade de infraestrutura de rede externo do Azure Stack. Iniciar SecretRotation gira todos os segredos da pilha gerada, e porque recebem certificados, certificados de ponto de extremidade externo também serão girados.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Atualizar a credencial do baseboard management controller (BMC)

Baseboard management controller (BMC) monitora o estado físico dos seus servidores. As especificações e instruções sobre como atualizar o nome da conta de usuário e a senha do BMC variam conforme o fornecedor de hardware do fabricante original do equipamento (OEM). Você deve atualizar suas senhas para os componentes do Azure Stack com regularidade.

1. Atualize o BMC em servidores físicos do Azure Stack, seguindo as instruções do OEM. O nome de usuário e senha para cada BMC em seu ambiente devem ser o mesmo. Observe que os nomes de usuário da BMC não podem exceder 16 caracteres.
2. Abra um ponto de extremidade com privilégios em sessões do Azure Stack. Para obter instruções, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).
3. Após seu PowerShell prompt foi alterado para **[endereço IP ou ERCS VM name]: PS >** ou a **[azs-ercs01]: PS >**, dependendo do ambiente, execute `Set-BmcCredential` executando `Invoke-Command`. Passe a variável de sessão com privilégios de ponto de extremidade como um parâmetro. Por exemplo: 

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    Você também pode usar a versão do PowerShell estática com as senhas como linhas de código:

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a segurança do Azure Stack](azure-stack-security-foundations.md)
