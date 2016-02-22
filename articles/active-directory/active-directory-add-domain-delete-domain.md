<properties
	pageTitle="Excluir um domínio personalizado no Active Directory do Azure | Microsoft Azure"
	description="Como excluir um domínio personalizado no Active Directory do Azure."
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
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>


# Excluindo um nome de domínio personalizado

Você poderá excluir um nome de domínio personalizado que não precisa mais usar com seu diretório do AD do Azure. Por exemplo, se tiver um novo nome de empresa ou se estiver usando outro diretório do AD do Azure, para citar alguns cenários. Você também poderá excluir um domínio não verificado, por exemplo, se perceber após adicioná-lo que você digitou incorretamente o nome ou se não tiver definido o valor corretamente de como o domínio será federado.

## Antes de começar

Antes de remover um nome de domínio, recomendamos que você leia as seguintes informações:

- O nome de domínio contoso.onmicrosoft.com original que foi fornecido ao diretório quando você se inscreveu não pode ser removido.
- Qualquer domínio de nível superior que tenha subdomínios associados a ele não pode ser removido até que os subdomínios sejam removidos. Por exemplo, não é possível remover o adatum.com se você tiver o corp.adatum.com ou outro subdomínio que usa o nome de domínio de nível superior. Para obter mais informações, consulte o artigo de suporte ["O domínio tem subdomínios associados" ou o erro "Não é possível remover um domínio que tem subdomínios" ao tentar remover um domínio do Office 365](https://support.microsoft.com/kb/2787792/).
- Você ativou a sincronização do diretório? Se ativou, um domínio semelhante a este foi adicionado automaticamente à sua conta: contoso.mail.onmicrosoft.com. Esse nome de domínio não pode ser removido.
- Antes de remover um nome de domínio, você deve primeiro remover o nome de domínio de todas as contas de usuário ou de email associadas ao domínio. Você pode remover todas as contas, ou você pode editar contas de usuário em massa para alterar informações de nomes de domínio e endereços de email. Para obter mais informações, consulte [Criar ou editar usuários no AD do Azure](active-directory-create-users.md). Lembre-se de remover:

	-   Qualquer usuário que tenha o domínio em seu nome de usuário ou endereço de email

	-   Qualquer grupo habilitado para email que tenha o domínio em seu endereço de email

	-   Qualquer aplicativo que tenha o domínio como parte de sua URL de resposta

- Se você estiver hospedando um site SharePoint Online em um nome de domínio que está sendo usado para um conjunto de sites do SharePoint Online, deve excluir o conjunto de sites antes de remover o nome de domínio.

## Para excluir um domínio

1.  Entre no portal clássico do Azure usando uma conta com privilégios de administrador global para aquele diretório.

2.  Abra o diretório e selecione **Domínios**.

3.  Selecione o domínio e clique em Excluir.

## Próximas etapas

- [Como usar nomes de domínio personalizados para simplificar a experiência de conexão para os usuários](active-directory-add-domain.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Adicionar e verificar um nome de domínio personalizado no Active Directory do Azure](active-directory-add-domain-add-verify-general.md)
- [Atribuir usuários a um domínio personalizado](active-directory-add-domain-add-users.md)
- [Alterar o registrador DNS para o nome de domínio personalizado](active-directory-add-domain-change-registrar.md)

<!---HONumber=AcomDC_0211_2016-->