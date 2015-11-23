<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Introdução | Microsoft Azure"
	description="Introdução aos Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/09/2015"
	ms.author="maheshu"/>

# Serviços de Domínio do AD do Azure *(Visualização)* - Introdução

## Etapa 5: Habilitar a sincronização de senha
Depois de habilitar os Serviços de Domínio do AD do Azure para seu locatário do AD do Azure, a próxima etapa é habilitar a sincronização de senhas. Isso permite que os usuários façam logon no domínio usando suas credenciais corporativas.

As etapas envolvidas são diferentes dependendo se sua organização é um locatário do AD do Azure somente na nuvem ou se está configurado para sincronizar com seu diretório local usando o AD do Azure Connect.

### Locatários somente nuvem - possibilita a geração de hash das credenciais NTLM e Kerberos no AD do Azure
Se a sua organização tem um locatário do AD do Azure somente na nuvem, os usuários que precisam usar os Serviços de Domínio do AD do Azure precisarão alterar suas senhas. Essa etapa faz com que hashes de credencial exigidos pelos Serviços de Domínio do AD do Azure para autenticação NTLM e Kerberos sejam gerados no AD do Azure. Você também pode expirar senhas para todos os usuários no locatário que precisam usar os Serviços de Domínio do AD do Azure ou instruir esses usuários para alterar suas senhas.

Aqui estão as instruções que você precisa fornecer aos usuários para alterar as senhas:

1. Navegue até a página Painel de Acesso do AD do Azure de sua organização. Normalmente está disponível em [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Selecione a guia **perfil** nesta página.
3. Clique no bloco **Alterar senha** dessa página para iniciar uma alteração de senha.

    ![Criar uma rede virtual para os Serviços de Domínio do AD do Azure.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Isso abre a página **Alterar senha**. Usuários podem inserir sua senha existente (antiga) e continuar para alterar sua senha.

    ![Criar uma rede virtual para os Serviços de Domínio do AD do Azure.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Depois que os usuários tiverem alterado suas senhas, a nova senha poderá ser usada nos serviços de domínio do AD do Azure em breve. Depois de alguns minutos, os usuários podem entrar para computadores que ingressaram no domínio gerenciado usando sua senha alterada recentemente.


### Locatários sincronizados - habilita a sincronização dos hashes das credenciais NTLM e Kerberos para o AD do Azure
Se o locatário do AD do Azure para sua organização está configurado para sincronizar com seu diretório local usando o AD do Azure Connect, você precisará configurar o AD do Azure Connect para sincronizar os hashes de credencial necessários para a autenticação NTLM e Kerberos. Esses hashes não são sincronizados com o AD do Azure por padrão, e as etapas a seguir o ajudarão a habilitar a sincronização de hashes para seu locatário do AD do Azure.

#### Instalar ou atualizar o Azure AD Connect

Você precisará instalar a versão mais recente do Azure AD Connect recomendada em um computador de domínio associado. Se você tiver uma instância existente da instalação do AD do Azure Connect, você precisará atualizá-la para usar a compilação GA do AD do Azure Connect. Certifique-se de usar a versão atual do Azure AD Connect, para evitar problemas/erros conhecidos.

**[Conecte-se de download do AD do Azure](http://www.microsoft.com/download/details.aspx?id=47594)**

Versão mínima recomendada: **1.0.9125** - publicada em 3 de novembro de 2015.

  >[AZURE.WARNING]Você DEVE instalar a versão GA do AD do Azure Connect para habilitar as credenciais de senha herdadas (necessárias para a autenticação NTLM e Kerberos) para sincronizar seu locatário do AD do Azure. Essa funcionalidade não está disponível em versões anteriores do Azure Connect AD ou com a ferramenta DirSync herdada.

Observação: você não precisa criar a chave do Registro 'EnableWindowsLegacyCredentialsSync' com a versão mais recente do Azure Connect AD (ou seja, 1.0.9125 e acima).

Instruções de instalação para o AD do Azure Connect estão disponíveis no seguinte artigo - [Introdução ao AD do Azure Connect](../active-directory/active-directory-aadconnect.md)


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

Dependendo do tamanho do seu diretório (número de usuários, grupos etc.), a sincronização de credenciais ao AD do Azure pode ser demorada. As senhas poderão ser usadas no domínio dos serviços de domínio do AD do Azure gerenciado logo após os hashes de credencial ter sincronizado ao AD do Azure.

<!---HONumber=Nov15_HO3-->