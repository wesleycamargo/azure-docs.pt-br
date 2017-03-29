---
title: "Serviços de Domínio do Azure AD: habilitar a sincronização de senha | Microsoft Docs"
description: "Introdução aos Serviços de Domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/17/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 4969b43831a3813a4e76c6447c252a9c458f371a
ms.lasthandoff: 03/18/2017


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Habilitar a sincronização de senhas para os Serviços de Domínio do Azure AD
Nas tarefas anteriores, você habilitou os serviços de domínio do Azure AD para seu locatário do Azure AD. A próxima tarefa é habilitar a sincronização de senhas nos Serviços de Domínio do Azure AD. Depois que a sincronização de credenciais é configurada, os usuários podem entrar no domínio gerenciado usando suas credenciais corporativas.

As etapas envolvidas são diferentes dependendo de sua organização ser um locatário do Azure AD somente na nuvem ou estar configurada para sincronização com seu diretório local usando o Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Locatário do Azure AD somente na nuvem](active-directory-ds-getting-started-password-sync.md)
> * [Locatário do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tarefa 5: Habilitar a sincronização de senhas para os Serviços de Domínio do AAD para um locatário do AD do Azure sincronizado
Um locatário do Azure AD sincronizado é configurado para ser sincronizado com o diretório do local de sua organização usando o Azure AD Connect. O Azure AD Connect não sincroniza hashes de credenciais NTLM e Kerberos com o Azure AD por padrão. Para usar os serviços de domínio do Azure AD, você precisa configurar o Azure AD Connect para sincronizar os hashes de credenciais necessários para a autenticação NTLM e Kerberos. 

> [!WARNING]
> É OBRIGATÓRIO habilitar a sincronização de senha com os Serviços de Domínio do AAD sempre que você habilitar os Azure AD Domain Services. Talvez você tenha ativado e desativado os Azure AD Domain Services para o seu diretório do Azure AD. No entanto, continua sendo preciso habilitar a sincronização de senha na próxima vez que você habilitar os Azure AD Domain Services para o diretório.
>
>

As etapas a seguir habilitam a sincronização dos hashes de credenciais necessários para seu locatário do Azure AD.

### <a name="install-or-update-azure-ad-connect"></a>Instalar ou atualizar o Azure AD Connect
Você precisa instalar a versão mais recente do Azure AD Connect recomendada em um computador ingressado no domínio associado. Se tiver uma instância existente do programa de instalação do Azure AD Connect, você precisará atualizá-lo para usar a última versão do Azure AD Connect. Para evitar problemas/erros conhecidos que talvez já tenham sido corrigidos, use sempre a versão mais recente do Azure AD Connect.

**[Baixar o Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versão recomendada: **1.1.281.0** - publicada em 7 de setembro de 2016.

> [!WARNING]
> Você DEVE instalar a versão GA do Azure AD Connect para habilitar as credenciais de senha herdadas (necessárias para a autenticação NTLM e Kerberos) para sincronizar seu locatário do Azure AD. Essa funcionalidade não está disponível em versões anteriores do Azure Connect AD ou com a ferramenta DirSync herdada.
>
>

Instruções de instalação para o AD do Azure Connect estão disponíveis no seguinte artigo - [Introdução ao AD do Azure Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Habilitar a sincronização dos hashes das credenciais NTLM e do Kerberos para o Azure AD
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

## <a name="related-content"></a>Conteúdo relacionado
* [Habilitar a sincronização de senhas nos Serviços de Domínio do AAD para um diretório do AD do Azure somente na nuvem](active-directory-ds-getting-started-password-sync.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Ingressar em uma máquina virtual do Windows para um domínio gerenciado dos Serviços de Domínio do AD do Azure](active-directory-ds-admin-guide-join-windows-vm.md)
* [Ingressar em uma máquina virtual do Red Hat Enterprise Linux para um domínio gerenciado dos Serviços de Domínio do AD do Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

