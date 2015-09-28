<properties
	pageTitle="Active Directory do Azure: criar tópico de suporte ao diretório | Microsoft Azure"
	description="Criando um diretório do Active Directory do Azure ou um diretório do Active Directory B2C do Azure — problemas e resoluções"
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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Criando um diretório do Azure AD ou um diretório Azure AD B2C - problemas e resoluções

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criando um diretório Azure AD

Se você não conseguir criar um diretório do Azure AD na primeira vez, tente novamente. Se o problema persistir, contate o Suporte.

## Criando um diretório do Azure AD B2C (visualização)

Há problemas conhecidos que você pode encontrar durante a [criação de um diretório do Azure AD B2C](active-directory-b2c-get-started).

- Se o diretório do Azure AD B2C não aparecer na lista de diretórios, tente novamente.
- Se o diretório do Azure AD B2C aparecer na lista de diretórios, mas você visualizar uma mensagem de erro informando "Não foi possível concluir a criação do diretório B2C 'contosob2c'. Visite este [link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) para obter mais orientações", siga um destes procedimentos:
    - Navegue até a folha de recursos do B2C no portal de visualização do Azure usando este link [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com), em que **{directory}** deve ser substituído pelo nome usado no momento da criação do diretório (por exemplo, contosob2c) e entre como Administrador Global. Clique em **Habilitar recursos do B2C** na parte superior da folha e clique em **OK**. Seu diretório do AD B2C do Azure deve estar pronto para uso!
	- Exclua o diretório recém-criado e tente novamente.
- Se nenhuma das resoluções acima funcionar, contate o Suporte. Leia [este artigo](active-directory-b2c-support.md) sobre como arquivar solicitações de suporte para o Azure AD B2C.

<!---HONumber=Sept15_HO3-->