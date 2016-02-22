<properties
	pageTitle="Atribuir usuários a um domínio personalizado no Active Directory do Azure | Microsoft Azure"
	description="Como preencher um domínio personalizado no Active Directory do Azure com contas de usuário."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="curtand;jeffsta"/>

# Atribuir usuários a um domínio personalizado

Após ter adicionado seu domínio personalizado ao Active Directory do Azure, você deve adicionar as contas de usuário a esse domínio para que possa começar a autenticá-los.

## Usuários sincronizados de um diretório em sua rede corporativa

Se você já configurou uma conexão entre seu Active Directory local e o Active Directory do Azure, a sincronização poderá preencher as contas. Para obter mais informações sobre como sincronizar o Active Directory do Azure com o Active Directory local, consulte [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

## Usuários adicionados e gerenciados na nuvem

Para alterar o domínio para uma conta de usuário:

1.  Abra o portal clássico do Azure usando uma conta que seja um administrador global ou administrador de usuário.

2.  Abra seu diretório.

3.  Clique a guia **Usuários**.

4.  Selecione o usuário na lista.

5.  Altere o domínio para o usuário e selecione **Salvar**.

Isso também pode ser feito usando o [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou a [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## Selecione um domínio personalizado ao criar um novo usuário

1.  Abra o portal clássico do Azure usando uma conta que seja um administrador global ou administrador de usuário.

2.  Abra seu diretório.

3.  Clique a guia **Usuários**.

4.  Na barra de comandos, selecione **Adicionar**.

5.  Quando adicionar o nome de usuário, escolha o domínio personalizado na lista de domínios.

6.  Selecione **Salvar**.

## Próximas etapas

- [Como usar nomes de domínio personalizados para simplificar a experiência de conexão para os usuários](active-directory-add-domain.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Adicionar e verificar um nome de domínio personalizado no Active Directory do Azure](active-directory-add-domain-add-verify-general.md)
- [Alterar o registrador DNS para o nome de domínio personalizado](active-directory-add-domain-change-registrar.md)
- [Excluir um domínio personalizado no Active Directory do Azure](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->