---
title: Encaminhamento de syslog de pilha do Azure
description: Saiba como integrar o Azure Stack com soluções de monitoramento usando o encaminhamento de syslog
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/28/2019
keywords: ''
ms.openlocfilehash: 3694425ac72d3b75d66d870e3746bc1738ba0138
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481902"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Integração do datacenter do Azure Stack - encaminhamento de syslog

Este artigo mostra como usar o syslog para integrar a infraestrutura do Azure Stack com soluções de segurança externa já implantadas em seu datacenter. Por exemplo, um sistema de gerenciamento de eventos de informações de segurança (SIEM). O canal de syslog expõe auditorias, alertas e logs de segurança de todos os componentes da infraestrutura do Azure Stack. Usar o encaminhamento de syslog para integrar soluções de monitoramento de segurança e/ou para recuperar todas as auditorias, alertas e segurança logs para armazená-los para a retenção.

Começando com a atualização 1809, o Azure Stack tem um cliente integrado de syslog que, uma vez configurado, emite mensagens de syslog com a carga no formato de evento comum (CEF).

O diagrama a seguir descreve a integração do Azure Stack com um SIEM externo. Há dois padrões de integração que precisam ser considerados: o primeiro um (uma em azul) é que a infraestrutura do Azure Stack que abrange as máquinas virtuais de infraestrutura e os nós do Hyper-V. As auditorias, logs de segurança e alertas a partir desses componentes centralmente são coletadas e expostas por meio do syslog com carga CEF. Esse padrão de integração é descrito nesta página do documento.
O segundo padrão de integração é descritos em laranja e aborda os controladores de gerenciamento de placa-base (BMC), o host de ciclo de vida de hardware (HLH), as máquinas virtuais e/ou soluções de virtualização que executam o parceiro de hardware de monitoramento e gerenciamento software e a parte superior de comutadores de rack (TOR). Como esses componentes são contato específico, de parceiros de hardware do seu hardware parceiro para obter a documentação sobre como integrá-los com um SIEM externo.

![Diagrama de encaminhamento de syslog](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Configuração do encaminhamento de syslog

O cliente de syslog no Azure Stack oferece suporte as seguintes configurações:

1. **Syslog sobre TCP, com a autenticação mútua (cliente e servidor) e criptografia de TLS 1.2:** Nessa configuração, o servidor syslog e o cliente de syslog podem verificar a identidade do outro por meio de certificados. As mensagens são enviadas por um canal criptografado do TLS 1.2.

2. **Syslog sobre TCP com autenticação de servidor e criptografia de TLS 1.2:** Nessa configuração, o cliente de syslog pode verificar a identidade do servidor syslog por meio de um certificado. As mensagens são enviadas por um canal criptografado do TLS 1.2.

3. **Syslog sobre TCP, sem criptografia:** Nessa configuração, o cliente do syslog e identidades de servidor de syslog não são verificadas. As mensagens são enviadas em texto não criptografado por meio de TCP.

4. **Syslog através de UDP, sem criptografia:** Nessa configuração, o cliente do syslog e identidades de servidor de syslog não são verificadas. As mensagens são enviadas em texto não criptografado via UDP.

> [!IMPORTANT]
> A Microsoft recomenda usar o TCP usando a autenticação e criptografia (configuração #1 ou, em que o mínimo, #2) para ambientes de produção proteger contra ataques man-in-the-middle e interceptação de mensagens.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Cmdlets para configurar o encaminhamento de syslog
Configuração do encaminhamento de syslog requer acesso ao ponto de extremidade com privilégios (PEP). Foram adicionados dois cmdlets do PowerShell para o PEP para configurar o encaminhamento de syslog:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Parâmetros de cmdlets

Parâmetros para *SyslogServer conjunto* cmdlet:

| Parâmetro | DESCRIÇÃO | Type | Obrigatório |
|---------|---------|---------|---------|
|*ServerName* | Endereço IP ou FQDN do servidor syslog | Cadeia de caracteres | Sim|
|*ServerPort* | Número da porta do servidor syslog está escutando | Cadeia de caracteres | Sim|
|*NoEncryption*| Forçar o cliente a enviar mensagens do syslog em texto não criptografado | Sinalizador | não|
|*SkipCertificateCheck*| Ignorar a validação do certificado fornecida pelo servidor syslog durante o handshake TLS inicial | Sinalizador | não|
|*SkipCNCheck*| Ignorar a validação do valor de nome comum do certificado fornecido pelo servidor syslog durante o handshake TLS inicial | Sinalizador | não|
|*UseUDP*| Usar o syslog com UDP como protocolo de transporte |Sinalizador | não|
|*Remover*| Remover a configuração do servidor do cliente e interromper o encaminhamento de syslog| Sinalizador | não|

Parâmetros para *SyslogClient conjunto* cmdlet:

| Parâmetro | DESCRIÇÃO | Type |
|---------|---------| ---------|
| *pfxBinary* | arquivo PFX que contém o certificado a ser usado pelo cliente como a identidade para autenticar o servidor syslog  | Byte[] |
| *CertPassword* |  Senha para importar a chave privada que está associada com o arquivo pfx | SecureString |
|*RemoveCertificate* | Remover o certificado do cliente | Sinalizador|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Configuração do encaminhamento de syslog com TCP, a autenticação mútua e criptografia de TLS 1.2

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog sobre TCP, com a criptografia de TLS 1.2. Durante o handshake inicial, o cliente verificará que o servidor fornece um certificado válido e confiável; da mesma forma, o cliente também fornece um certificado para o servidor como prova de sua identidade. Essa configuração é o mais seguro como ele fornece uma validação completa da identidade do cliente e o servidor e envia mensagens em um canal criptografado. 

> [!IMPORTANT]
> A Microsoft recomenda fortemente para usar essa configuração em ambientes de produção. 

Para configurar o encaminhamento de syslog com TCP, a autenticação mútua e criptografia de TLS 1.2, execute um desses cmdlets em uma sessão PEP:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

O certificado do cliente deve ter a mesma raiz daquele fornecido durante a implantação do Azure Stack. Ele também deve conter uma chave privada.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Configuração do encaminhamento de syslog com TCP, autenticação de servidor e criptografia de TLS 1.2

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog sobre TCP, com a criptografia de TLS 1.2. Durante o handshake inicial, o cliente também verifica que o servidor fornece um certificado válido e confiável. Essa configuração impede que o cliente para enviar mensagens para destinos não confiáveis.
Usando a autenticação e criptografia de TCP é a configuração padrão e representa o nível mínimo de segurança que a Microsoft recomenda para um ambiente de produção. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

Caso você deseje testar a integração do seu servidor syslog com o cliente do Azure Stack, usando um certificado autoassinado e/ou não confiável, você pode usar esses sinalizadores para ignorar a validação executada pelo cliente durante o handshake inicial do servidor.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Microsoft não recomenda o uso do sinalizador - SkipCertificateCheck para ambientes de produção. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Configurando o encaminhamento de syslog com TCP e nenhuma criptografia

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog sobre TCP, sem criptografia. O cliente verifica a identidade do servidor nem fornece sua própria identidade para o servidor para verificação. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> A Microsoft recomenda não usar essa configuração para ambientes de produção. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Configurando o encaminhamento de syslog com UDP e nenhuma criptografia

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog através de UDP, sem criptografia. O cliente verifica a identidade do servidor nem fornece sua própria identidade para o servidor para verificação. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

Embora UDP sem criptografia seja mais fácil de configurar, ele não fornece nenhuma proteção contra ataques man-in-the-middle e interceptação de mensagens. 

> [!IMPORTANT]
> A Microsoft recomenda não usar essa configuração para ambientes de produção. 


## <a name="removing-syslog-forwarding-configuration"></a>Removendo a configuração de encaminhamento do syslog

Para remover a configuração do servidor syslog completamente e interromper o encaminhamento syslog:

**Remover a configuração do servidor syslog do cliente**

```powershell  
Set-SyslogServer -Remove
```

**Remover o certificado do cliente do cliente**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Verificando a configuração de syslog

Se você conectou com êxito o cliente de syslog para o servidor syslog, você deve começar assim que receber eventos. Se você não vir quaisquer eventos, verifique se a configuração do seu cliente de syslog, executando os seguintes cmdlets:

**Verifique se a configuração do servidor no cliente de syslog**

```powershell  
Get-SyslogServer
```

**Verifique se a configuração de certificado no cliente de syslog**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Esquema de mensagem do syslog

O encaminhamento de syslog da infraestrutura do Azure Stack envia mensagens formatadas em comum (CEF) do formato de evento.
Cada mensagem do syslog é estruturada com base nesse esquema: 

```Syslog
<Time> <Host> <CEF payload>
```

O conteúdo CEF é baseado na estrutura abaixo, mas o mapeamento para cada campo varia dependendo do tipo de mensagem (eventos do Windows, alerta criado, alerta fechada).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>Mapeamento de CEF para eventos de ponto de extremidade com privilégios

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

Tabela de eventos para o ponto de extremidade privilegiado:

| Evento | ID do evento PEP | Nome da tarefa PEP | Severity |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|

Tabela de gravidade PEP:

| Severity | Nível | Valor numérico |
|----------|-------| ----------------|
|0|Indefinido|Valor: 0. Indica os logs em todos os níveis|
|10|Crítico|Valor: 1. Indica os logs para um alerta crítico|
|8|Erro| Valor: 2. Indica os logs de erro|
|5|Aviso|Valor: 3. Indica os logs para um aviso|
|2|Informações|Valor: 4. Indica os logs para uma mensagem informativa|
|0|Detalhado|Valor: 5. Indica os logs em todos os níveis|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>Mapeamento de CEF para eventos de ponto de extremidade de recuperação

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

Tabela de eventos para o ponto de extremidade de recuperação:

| Evento | ID do evento de representante | Nome do representante de tarefa | Severity |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Tabela de representante Severity:

| Severity | Nível | Valor numérico |
|----------|-------| ----------------|
|0|Indefinido|Valor: 0. Indica os logs em todos os níveis|
|10|Crítico|Valor: 1. Indica os logs para um alerta crítico|
|8|Erro| Valor: 2. Indica os logs de erro|
|5|Aviso|Valor: 3. Indica os logs para um aviso|
|2|Informações|Valor: 4. Indica os logs para uma mensagem informativa|
|0|Detalhado|Valor: 5. Indica os logs em todos os níveis|

### <a name="cef-mapping-for-windows-events"></a>Mapeamento de CEF para eventos do Windows

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabela de gravidade para eventos do Windows:

| Valor de severidade CEF | Nível de evento do Windows | Valor numérico |
|--------------------|---------------------| ----------------|
|0|Indefinido|Valor: 0. Indica os logs em todos os níveis|
|10|Crítico|Valor: 1. Indica os logs para um alerta crítico|
|8|Erro| Valor: 2. Indica os logs de erro|
|5|Aviso|Valor: 3. Indica os logs para um aviso|
|2|Informações|Valor: 4. Indica os logs para uma mensagem informativa|
|0|Detalhado|Valor: 5. Indica os logs em todos os níveis|

Tabela de extensão personalizada para eventos do Windows no Azure Stack:

| Nome de extensão personalizada | Exemplo de evento do Windows | 
|-----------------------|---------|
|MasChannel | Sistema|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| Svchost!! 4132, G, 0!!! EseDiskFlushConsistency!! ESENT!! 0x800000|
|MasEventDescription| As configurações de diretiva de grupo do usuário foram processadas com êxito. Não houve nenhuma alteração detectada desde o último processamento da diretiva de grupo.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Êxito na auditoria|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |informações|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Criação de processo|
|MasUserData|KB4093112!! 5112!! Instalado!! 0x0!! WindowsUpdateAgent Xpath: /Event/UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mapeamento de CEF para os alertas criados

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabela de gravidade de alertas:

| Severity | Nível |
|----------|-------|
|0|Indefinido|
|10|Crítico|
|5|Aviso|

Tabela de extensão personalizada para os alertas criados no Azure Stack:

| Nome de extensão personalizada | Exemplo | 
|-----------------------|---------|
|MasEventDescription|DESCRIÇÃO: Uma conta de usuário \<TestUser\> foi criado para \<TestDomain\>. É um risco de segurança. – CORREÇÃO: Entre em contato com o suporte. Assistência ao cliente é necessária para resolver esse problema. Não tente resolver esse problema sem sua assistência. Antes de abrir uma solicitação de suporte, iniciar o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>Mapeamento de CEF para alertas fechados

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

O exemplo a seguir mostra uma mensagem de syslog com carga CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>Próximas etapas

[Política de manutenção](azure-stack-servicing-policy.md)