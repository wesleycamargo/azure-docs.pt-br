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
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Serviços de Domínio do AD do Azure *(Visualização)* - Habilitar a sincronização de senhas nos Serviços de Domínio do AD do Azure

## Tarefa 5: Habilitar a sincronização de senhas para os Serviços de Domínio do AAD para um diretório do AD do Azure somente na nuvem
Depois de habilitar os Serviços de Domínio do AD do Azure para seu locatário do AD do Azure, a próxima etapa será habilitar as credenciais para sincronizar os Serviços de Domínio do AD do Azure. Isso permite que os usuários façam logon no domínio gerenciado usando suas credenciais corporativas.

As etapas envolvidas são diferentes com base em sua organização ter um diretório do AD do Azure somente na nuvem ou estar configurado para sincronizar com seu diretório local usando o Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Diretório do AD do Azure somente na nuvem](active-directory-ds-getting-started-password-sync.md)
- [Diretório do AD do Azure sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Habilitar a geração de hashes de credencial Kerberos e NTLM para um diretório do AD do Azure somente na nuvem
Se a sua organização tiver um diretório do AD do Azure somente na nuvem, os usuários que precisam usar os Serviços de Domínio do AD do Azure precisarão alterar suas senhas. Esse processo de alteração de senhas faz com que os hashes de credencial requeridos pelos Serviços de Domínio do AD do Azure para a autenticação Kerberos e NTLM sejam gerados no AD do Azure. Você também pode expirar senhas para todos os usuários no locatário que precisam usar os Serviços de Domínio do AD do Azure ou instruir esses usuários para alterar suas senhas.

Aqui estão as instruções que você precisa fornecer aos usuários finais para eles alterarem suas senhas:

1. Navegue até a página Painel de Acesso do AD do Azure de sua organização. Normalmente está disponível em [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Selecione a guia **perfil** nesta página.

3. Clique no bloco **Alterar senha** dessa página para iniciar uma alteração de senha.

    ![Criar uma rede virtual para os Serviços de Domínio do AD do Azure.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Isso abre a página **Alterar senha**. Usuários podem inserir sua senha existente (antiga) e continuar para alterar sua senha.

    ![Criar uma rede virtual para os Serviços de Domínio do AD do Azure.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Depois que os usuários tiverem alterado suas senhas, a nova senha poderá ser usada nos serviços de domínio do AD do Azure em breve. Depois de alguns minutos, os usuários podem entrar para computadores que ingressaram no domínio gerenciado usando sua senha alterada recentemente.


<br>

## Conteúdo relacionado

- [Habilitar a sincronização de senhas para os Serviços de Domínio do AAD para um diretório do AD do Azure sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Ingressar em uma máquina virtual do Windows para um domínio gerenciado dos Serviços de Domínio do AD do Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Ingressar em uma máquina virtual do Red Hat Enterprise Linux para um domínio gerenciado dos Serviços de Domínio do AD do Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0706_2016-->