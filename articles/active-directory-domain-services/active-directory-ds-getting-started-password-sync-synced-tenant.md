<properties
	pageTitle="Serviços de Domínio do AD do Azure: Habilitar a sincronização de senhas | Microsoft Azure"
	description="Introdução aos Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/07/2016"
	ms.author="maheshu"/>

# Serviços de Domínio do AD do Azure *(Visualização)* - Habilitar a sincronização de senhas nos Serviços de Domínio do AD do Azure

## Tarefa 5: Habilitar a sincronização de senhas para os Serviços de Domínio do AAD para um locatário do AD do Azure sincronizado
Depois de habilitar os Serviços de Domínio do AD do Azure para seu diretório do AD do Azure, a próxima tarefa será habilitar a sincronização de senhas nos Serviços de Domínio do AD do Azure. Depois que você faz isso, os usuários podem entrar no domínio usando suas credenciais corporativas.

As etapas envolvidas são diferentes com base em sua organização ter um diretório do AD do Azure somente na nuvem ou estar configurado para sincronizar com seu diretório local usando o Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Diretório do AD do Azure somente na nuvem](active-directory-ds-getting-started-password-sync.md)
- [Diretório do AD do Azure sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Locatários sincronizados - habilita a sincronização dos hashes das credenciais NTLM e Kerberos para o AD do Azure
Um locatário do Azure AD sincronizado é configurado para ser sincronizado com o diretório do local de sua organização usando o Azure AD Connect. O Azure AD Connect não sincroniza hashes de credenciais NTLM e Kerberos com o Azure AD por padrão. Para usar os serviços de domínio do Azure AD, você precisa configurar o Azure AD Connect para sincronizar os hashes de credenciais necessários para a autenticação NTLM e Kerberos. As etapas a seguir habilitam a sincronização dos hashes de credenciais necessários para seu locatário do Azure AD.

#### Instalar ou atualizar o Azure AD Connect

Você precisa instalar a versão mais recente do Azure AD Connect recomendada em um computador de domínio associado. Se tiver uma instância existente do programa de instalação do Azure AD Connect, você precisará atualizá-lo para usar a última versão do Azure AD Connect. Para evitar problemas/erros conhecidos que talvez já tenham sido corrigidos, use sempre a versão mais recente do Azure AD Connect.

**[Baixar o Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versão recomendada: **1.1.281.0** - publicada em 7 de setembro de 2016.

  > [AZURE.WARNING] Você DEVE instalar a versão GA do Azure AD Connect para habilitar as credenciais de senha herdadas (necessárias para a autenticação NTLM e Kerberos) para sincronizar seu locatário do Azure AD. Essa funcionalidade não está disponível em versões anteriores do Azure Connect AD ou com a ferramenta DirSync herdada.

Instruções de instalação para o AD do Azure Connect estão disponíveis no seguinte artigo - [Introdução ao AD do Azure Connect](../active-directory/active-directory-aadconnect.md)


#### Forçar a sincronização de senha completa para o AD do Azure

Execute o seguinte script PowerShell em cada floresta do AD, para forçar a sincronização de senhas completa e habilitar os hashes de credenciais de todos os usuários locais para sincronização com seu locatário do Azure AD. Esse script permite que os hashes de credenciais necessários para a autenticação Kerberos/NTLM sejam sincronizados com seu locatário do Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Dependendo do tamanho do diretório (número de usuários, grupos etc.), a sincronização de credenciais com o Azure AD pode ser demorada. As senhas poderão ser usadas no domínio dos serviços de domínio do AD do Azure gerenciado logo após os hashes de credencial ter sincronizado ao AD do Azure.


<br>

## Conteúdo relacionado

- [Habilitar a sincronização de senhas nos Serviços de Domínio do AAD para um diretório do AD do Azure somente na nuvem](active-directory-ds-getting-started-password-sync.md)

- [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Ingressar em uma máquina virtual do Windows para um domínio gerenciado dos Serviços de Domínio do AD do Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Ingressar em uma máquina virtual do Red Hat Enterprise Linux para um domínio gerenciado dos Serviços de Domínio do AD do Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0914_2016-->