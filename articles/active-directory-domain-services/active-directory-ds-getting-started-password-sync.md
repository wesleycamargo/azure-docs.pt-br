<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Introdução | Microsoft Azure"
	description="Introdução aos Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="maheshu"/>

# Serviços de Domínio do AD do Azure *(Visualização)* - Introdução

## Etapa 5: Habilitar a sincronização de senha
Depois de habilitar os Serviços de Domínio do AD do Azure para seu locatário do AD do Azure, a próxima etapa é habilitar a sincronização de senhas. Isso permite que os usuários façam logon no domínio usando suas credenciais corporativas.

As etapas envolvidas são diferentes dependendo se sua organização é um locatário do AD do Azure somente na nuvem ou se está configurado para sincronizar com seu diretório local usando o AD do Azure Connect.

### Habilitar a sincronização de senha para locatários somente na nuvem
Se a sua organização tem um locatário do AD do Azure somente na nuvem, os usuários que precisam usar os Serviços de Domínio do AD do Azure precisarão alterar suas senhas. Essa etapa faz com que hashes de credencial exigidos pelos Serviços de Domínio do AD do Azure para autenticação NTLM e Kerberos sejam gerados no AD do Azure. Você também pode expirar senhas para todos os usuários no locatário que precisam usar os Serviços de Domínio do AD do Azure ou instruir esses usuários finais para alterar suas senhas.

Aqui estão as instruções que você precisa fornecer aos usuários finais para alterar as senhas:

1. Navegue até a página Painel de Acesso do AD do Azure de sua organização. Normalmente está disponível em [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Selecione a guia **perfil** nesta página.
3. Clique no bloco **Alterar senha** dessa página para iniciar uma alteração de senha.

    ![Criar uma rede virtual para os Serviços de Domínio do AD do Azure.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Isso abre a página **Alterar senha**. O usuário pode inserir sua senha existente (antiga) e continuar para alterar a senha.

    ![Criar uma rede virtual para os Serviços de Domínio do AD do Azure.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Depois que os usuários já alteraram as senhas, a nova senha será sincronizada com os Serviços de Domínio do AD do Azure em breve. Depois que a sincronização de senha estiver concluída, os usuários podem então fazer logon no domínio usando sua senha alterada recentemente.


### Habilitar a sincronização de senha para locatários sincronizados
Se o locatário do AD do Azure para sua organização está configurado para sincronizar com seu diretório local usando o AD do Azure Connect, você precisará configurar o AD do Azure Connect para sincronizar os hashes de credencial necessários para a autenticação NTLM e Kerberos. Esses hashes não são sincronizados com o AD do Azure por padrão, e as etapas a seguir o ajudarão a habilitar a sincronização de hashes para seu locatário do AD do Azure.

#### Instalar o AD do Azure Connect

Você precisará instalar a versão GA do AD do Azure Connect em um computador ingressado no domínio. Se você tiver uma instância existente da instalação do AD do Azure Connect, você precisará atualizá-la para usar a compilação GA do AD do Azure Connect.

  [Baixe o AD do Azure Connect aqui – versão GA](http://download.microsoft.com/download/B/0/0/B00291D0-5A83-4DE7-86F5-980BC00DE05A/AzureADConnect.msi).

> [AZURE.WARNING]Você DEVE instalar a versão GA do AD do Azure Connect para habilitar credenciais de senha herdadas (necessárias para a autenticação NTLM e Kerberos) para sincronizar seu locatário do AD do Azure. Essa funcionalidade não está disponível nas versões anteriores do AD do Azure Connect.

Instruções de instalação para o AD do Azure Connect estão disponíveis no seguinte artigo - [Introdução ao AD do Azure Connect](../active-directory/active-directory-aadconnect.md)


#### Habilitar a sincronização de credenciais herdadas no AD do Azure

Habilite a sincronização de credenciais herdadas necessárias para a autenticação NTLM nos Serviços de Domínio do AD do Azure. Faça isso criando a seguinte chave de registro no computador onde o Azure AD Connect foi instalado.

Crie a seguinte chave de registro DWORD e atribua a ela o valor 1.

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MSOLCoExistence\PasswordSync\EnableWindowsLegacyCredentialsSync

Set its value to 1.
```

#### Forçar a sincronização de senha completa para o AD do Azure

Para forçar a sincronização de senha completa e habilitar os hashes de senha de todos os usuários locais (incluindo os hashes de credenciais necessários para a autenticação NTLM/Kerberos) para sincronizar com seu locatário do AD do Azure, execute o seguinte script do PowerShell em cada floresta do AD.

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

Dependendo do tamanho do seu diretório (número de usuários, grupos etc.), a sincronização de credenciais com o AD do Azure e depois com os Serviços de Domínio do AD do Azure pode ser demorada.

<!---HONumber=Oct15_HO4-->