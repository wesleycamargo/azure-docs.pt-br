<properties
	pageTitle="Active Directory do Azure: criar tópico de suporte ao locatário | Microsoft Azure"
	description="Criando um locatário do Active Directory do Azure ou um locatário do Active Directory B2C do Azure — problemas e resoluções"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Criando um Locatário do AD do Azure ou um Locatário do AD B2C do Azure - problemas e resoluções

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criando um Locatário do AD do Azure

Se você não conseguir criar um locatário do AD do Azure na primeira vez, tente novamente. Se o problema persistir, contate o Suporte.

## Criando um Locatário do AD B2C do Azure (visualização)

Há problemas conhecidos que você pode encontrar durante a [criação de um locatário do AD B2C do Azure](active-directory-b2c-get-started.md).

- Se o locatário do AD B2C do Azure não aparecer na lista de locatários, tente novamente.
- Se o locatário do AD B2C do Azure aparecer na lista de locatários, mas você visualizar uma mensagem de erro informando "Não foi possível concluir a criação do locatário B2C 'contosob2c'. Visite este [link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) para obter mais orientações", siga um destes procedimentos:
    - Navegue até a folha de recursos do B2C no portal de visualização do Azure usando este link [https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com), em que **{tenant}** deve ser substituído pelo nome usado no momento da criação do locatário (por exemplo, contosob2c) e entre como Administrador Global. Clique em **Habilitar recursos do B2C** na parte superior da folha e clique em **OK**. Seu locatário do AD B2C do Azure deve estar pronto para uso!
	- Exclua o locatário recém-criado e tente novamente.
- Se nenhuma das resoluções acima funcionar, contate o Suporte. Leia [este artigo](active-directory-b2c-support.md) sobre como arquivar solicitações de suporte para o Azure AD B2C.

<!---HONumber=Oct15_HO1-->