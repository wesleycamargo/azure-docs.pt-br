<properties
	pageTitle="Recursos e configurações do serviço de sincronização do Azure AD Connect | Microsoft Azure"
	description="Descreve os recursos do serviço de sincronização do Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell;markvi"/>

# Recursos do serviço de sincronização do Azure AD Connect

O recurso de sincronização do Azure AD Connect tem dois componentes:

- O componente local denominado **Sincronização do Azure AD Connect**, também chamado de **mecanismo de sincronização**.
- O serviço que reside no Azure AD, também conhecido como **Serviço de sincronização do Azure AD Connect**

Este tópico explica como os seguintes recursos do **Serviço de sincronização do Azure AD Connect** funcionam e como você pode configurá-los usando o Windows PowerShell.

Essas configurações são definidas pelo [Módulo Azure Active Directory para Windows PowerShell](http://aka.ms/aadposh) e você precisa baixá-lo e instalá-lo separadamente do Azure AD Connect para definir as configurações. Os cmdlets documentados foram introduzidos na [versão de março de 2016 (build 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se não tiver os cmdlets documentados neste tópico ou se eles não produzirem o mesmo resultado, certifique-se de executar a versão mais recente.

Para ver a configuração em seu diretório do Azure AD, execute `Get-MsolDirSyncFeatures`. ![Resultado de Get-MsolDirSyncFeatures](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Muitas dessas configurações podem ser alteradas somente pelo Azure AD Connect.

As configurações a seguir podem ser definidas por `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentário
--- | ---
 [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Permitirá que um atributo seja colocado em quarentena se ele for uma duplicata de outro objeto, em vez de causar falha de todo o objeto durante a exportação.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Permite que os objetos se unam em userPrincipalName além do endereço SMTP primário.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Permite que o mecanismo de sincronização atualize o atributo userPrincipalName para usuários gerenciados/licenciados (não federados).

Depois que você habilitar um recurso, ele não poderá ser desabilitado novamente.

As configurações a seguir são definidas pelo Azure AD Connect e não podem ser modificadas por `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentário
--- | ---
DeviceWriteback | [Azure AD Connect: habilitando o write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Sincronização do Azure AD Connect: extensões do Directory](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Implementação de sincronização de senha com a sincronização do Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Visualização: write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | Não há suporte no momento.

## Resiliência do atributo duplicado
Em vez de falhar ao provisionar objetos com UPNs/proxyAddresses duplicados, o atributo duplicado é "colocado em quarentena" e um valor temporário é atribuído, se necessário. Quando o conflito é resolvido, o UPN temporário é corrigido para o valor correto automaticamente. Esse comportamento pode ser habilitado para o UPN e o proxyAddress separadamente. Para obter mais detalhes, consulte [Sincronização de identidades e resiliência do atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## Correspondência suave de UserPrincipalName
Quando este recurso é habilitado, a correspondência suave é aplicada no UPN, bem como no [endereço SMTP primário](https://support.microsoft.com/kb/2641663), que está sempre habilitado. A correspondência suave é usada para fazer a correspondência de usuários na nuvem existentes no Azure AD com usuários locais.

Habilitar esse recurso será particularmente útil se você precisar fazer a correspondência de contas locais do AD com contas existentes criadas na nuvem e não estiver usando o Exchange Online. Nesse cenário, você normalmente não tem um motivo para definir o atributo SMTP na nuvem.

O recurso fica ativado por padrão para diretórios recém-criados do Azure AD. Você pode ver se ele está habilitado executando:
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se o recurso não estiver habilitado para seu diretório do Azure AD, você poderá habilitá-lo executando:
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## Sincronizar atualizações de userPrincipalName
Historicamente, atualizações do atributo UserPrincipalName usando o serviço de sincronização local são bloqueadas, a menos que estas duas condições sejam verdadeiras:

- O usuário é gerenciado (não federado).
- Não foi atribuída uma licença ao usuário.

Para obter mais detalhes, consulte [Os nomes de usuário no Office 365, Azure ou Intune não coincidem com o UPN local ou ID de logon alternativo](https://support.microsoft.com/kb/2523192).

Habilitar o recurso permite que o mecanismo de sincronização atualize o userPrincipalName quando ele é alterado localmente e você usa a sincronização da senha. Se você usar a federação, esse recurso não funcionará.

O recurso fica ativado por padrão para diretórios recém-criados do Azure AD. Você pode ver se ele está habilitado executando:
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se o recurso não estiver habilitado para seu diretório do Azure AD, você poderá habilitá-lo executando:
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Depois de habilitar esse recurso, os valores existentes de userPrincipalName permanecerão os mesmos. Na próxima alteração do atributo userPrincipalName local, a sincronização delta normal dos usuários atualizará o UPN.

## Alterações futuras
Essas configurações serão habilitadas para todos os diretórios do Azure AD no futuro.

## Consulte também

- [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integração de suas identidades locais com o Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->