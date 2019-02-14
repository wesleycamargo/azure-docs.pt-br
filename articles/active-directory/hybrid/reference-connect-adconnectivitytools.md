---
title: 'Azure AD Connect: Referência do PowerShell ADConnectivityTools | Microsoft Docs'
description: Este documento fornece informações de referência para o módulo do PowerShell ADConnectivityTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: b90c0572de6c6e664913cd01fdf9ca959694d5dd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191938"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect:  Referência do PowerShell ADConnectivityTools
A documentação a seguir fornece informações de referência para o módulo do PowerShell ADConnectivityTools.psm1 que é incluído no Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>SINOPSE
Detecta problemas de DNS local.

### <a name="syntax"></a>SINTAXE

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Executa testes de conectividade de DNS local.
Para configurar o conector do Active Directory, o usuário deve ter a resolução de nomes da floresta em que estiver tentando se conectar, bem como em controladores de domínio associados a esta floresta.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2
```
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
Especifica o nome da floresta para o teste.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs
Especifica os DCs para o teste.

```yaml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
Retorna o resultado deste diagnóstico na forma de um PSObject.
Não é necessário durante a interação manual com essa ferramenta.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>SINOPSE
Determina se uma floresta especificada existe.

### <a name="syntax"></a>SINTAXE

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>Descrição
Consulta os endereços IP associados a uma floresta em um servidor DNS.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
Especifica o nome da floresta para o teste.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>SINOPSE
Verifica o nível funcional de floresta do AD.

### <a name="syntax"></a>SINTAXE

#### <a name="samaccount"></a>SamAccount
```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN
```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Verifica se o nível funcional de floresta do AD é igual ou maior que um determinado MinAdForestVersion (WindowsServer2003).
A conta (Domínio\Nome de usuário) e a senha podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>EXEMPLO 2
```
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPLO 3
```
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
Floresta de destino.
O valor padrão é a floresta do usuário atualmente conectado.

```yaml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN
Objeto ForestFQDN de destino.

```yaml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
A função usará as credenciais do usuário que fez logon no computador em vez de solicitar credenciais personalizadas do usuário.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SINOPSE
Detecta problemas de conectividade de rede local.

### <a name="syntax"></a>SINTAXE

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Executa testes de conectividade de rede local.

Para os testes de rede local, o AAD Connect deve ser capaz de se comunicar com os controladores de domínio nomeados nas portas 53 (DNS), 88 (Kerberos) e 389 (LDAP); a maioria das organizações executam DNS em seus controladores de domínio, por isso esse teste é atualmente integrado.
A porta 53 deverá ser ignorada se outro servidor DNS tiver sido especificado.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2
```
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-dcs"></a>-DCs
Especifica os DCs para o teste.

```yaml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort
Se o usuário não está usando os serviços DNS fornecidos pelo Site do AD/DC de Logon, talvez queira ignorar a verificação da porta 53. O usuário ainda deverá ser capaz de resolver _.ldap._tcp.\<forestfqdn\> para que a configuração Conector do Active Directory seja bem-sucedida.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
Retorna o resultado deste diagnóstico na forma de um PSObject.
Não é necessário durante a interação manual com essa ferramenta.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>SINOPSE
Determina se uma floresta especificada e seus Controladores de Domínio associados estão acessíveis.

### <a name="syntax"></a>SINTAXE

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>Descrição
Executa testes de "ping" (se o computador pode acessar um computador de destino por meio da rede e/ou da Internet)

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
Especifica o nome da floresta para o teste.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs
Especifica os DCs para o teste.

```yaml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>SINOPSE
Validar se os domínios no FQDN da floresta obtido estão acessíveis

### <a name="syntax"></a>SINTAXE

#### <a name="samaccount"></a>SamAccount
```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN
```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Valida se todos os domínios no FQDN de floresta obtido estão acessíveis por meio da tentativa de recuperar DomainGuid e DomainDN.
A conta (Domínio\Nome de usuário) e a senha podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2
```
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPLO 3
```
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
Floresta de destino.

```yaml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN
Objeto ForestFQDN de destino.

```yaml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
A função usará as credenciais do usuário que fez logon no computador em vez de solicitar credenciais personalizadas do usuário.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SINOPSE
Verifica se um usuário tem credenciais de Admin Corporativo.

### <a name="syntax"></a>SINTAXE

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Pesquisa se o usuário fornecido tem credenciais de Admin Corporativo.
A conta (Domínio\Nome de usuário) e a senha podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2
```
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
A função usará as credenciais do usuário que fez logon no computador em vez de solicitar credenciais personalizadas do usuário.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SINOPSE
Recupera um DomainFQDN de uma combinação de conta e senha.

### <a name="syntax"></a>SINTAXE

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Tenta obter um objeto domainFQDN de credenciais fornecidas.
Se o domainFQDN for válido, um DomainFQDNName ou RootDomainName será retornado, dependendo da escolha do usuário.
A conta (Domínio\Nome de usuário) e a senha podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2
```
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType
Tipo de dados desejado que será recuperado.
No momento, limitado a "DomainFQDNName" ou "RootDomainName".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
A função usará as credenciais do usuário que fez logon no computador em vez de solicitar credenciais personalizadas do usuário.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError
Parâmetro auxiliar usado pela função Start-NetworkConnectivityDiagnosisTools

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SINOPSE
Recupera um ForestFQDN de uma combinação de conta e senha.

### <a name="syntax"></a>SINTAXE

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Tenta obter um ForestFQDN das credenciais fornecidas.
A conta (Domínio\Nome de usuário) e a senha podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2
```
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
O valor forest.Default de destino é o Domínio do usuário atualmente conectado.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
A função usará as credenciais do usuário que fez logon no computador em vez de solicitar credenciais personalizadas do usuário.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SINOPSE
Função principal.

### <a name="syntax"></a>SINTAXE

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>Descrição
Executa todos os mecanismos disponíveis que verificam se as credenciais do AD são válidas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
Floresta de destino.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount
Para instalações Personalizadas: sinaliza que será $True se o usuário escolher "Criar nova conta do AD" na janela de Conta da Floresta do AD do Assistente do AADConnect.
$False se o usuário escolher "Usar conta existente do AD".
Para instalações Expressas: o valor dessa variável deve ser $True para instalações Expressas.

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
Parâmetro que pré-preenche o campo de Nome de usuário quando as credenciais do usuário são solicitadas.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SINOPSE
Função principal para testes de conectividade de rede.

### <a name="syntax"></a>SINTAXE

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>Descrição
Executa testes de conectividade de rede local.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1
```
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2
```
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Forest
Especifica o nome da floresta para o teste.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Credentials
O nome de usuário e senha do usuário que está executando o teste.
Exige o mesmo nível de permissões que é necessário para executar o Assistente do Azure AD Connect.

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation
Especifica a localização de um arquivo de log que conterá a saída dessa função.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs
Especifica os DCs para o teste.

```yaml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage
Sinalizador que permite exibir uma mensagem sobre a finalidade dessa função.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
Retorna o resultado deste diagnóstico na forma de um PSObject.
Não é necessário especificar durante a interação manual com essa ferramenta.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials
Indica se as credenciais que o usuário digitou são válidas.
Não é necessário especificar durante a interação manual com essa ferramenta.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para obter mais informações, confira about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
