<properties
	pageTitle="Visão geral conceitual dos nomes de domínio personalizados no Azure Active Directory | Microsoft Azure"
	description="Explica a estrutura conceitual para usar nomes de domínio personalizados no Azure Active Directory, incluindo federação para logon único"
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
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Visão geral conceitual dos nomes de domínio personalizados no Azure Active Directory

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: ele é parte de um nome de usuário ou endereço de email para um usuário, parte do endereço para um grupo e pode ser parte do URI da ID do aplicativo para um aplicativo. Um recurso no Azure AD (Azure Active Directory) pode incluir um nome de domínio já verificado como pertencente ao diretório que contém o recurso. Somente um administrador global pode executar tarefas de gerenciamento de domínio no Azure AD.

Nomes de domínio no Azure AD são globalmente exclusivos. Um nome de domínio pode ser usado por um único Azure AD. Se um diretório do Azure AD verificar um nome de domínio, nenhum outro diretório do Azure AD poderá verificar ou usar o mesmo nome de domínio.

## Nomes de domínio iniciais e personalizados

Todo nome de domínio no Azure AD é um nome de domínio inicial ou um nome de domínio personalizado.

Todo Azure AD vem com um nome de domínio inicial no formato contoso.onmicrosoft.com. O terceiro nível do nome do domínio, que neste exemplo é "contoso.com", foi estabelecido quando o diretório foi criado, normalmente pelo administrador que criou o diretório. O nome de domínio inicial de um diretório não pode ser alterado ou excluído. O nome de domínio inicial, embora seja totalmente funcional, destina-se principalmente a ser usado como um mecanismo de inicialização.

Na maioria dos ambientes de produção, um diretório tem pelo menos um domínio personalizado verificado, como "contoso.com", e é esse domínio personalizado que fica visível para os usuários finais. Um nome de domínio personalizado é um recurso adicionado por um administrador global de um diretório. Um nome de domínio personalizado é um domínio que pertence à organização, como "contoso.com". A maioria das organizações já possui um nome de domínio que usa para hospedar seu site. Esse nome de domínio é familiar aos funcionários porque faz parte do nome de usuário que eles usam para entrar na rede corporativa ou para enviar e recuperar emails. Antes de poder ser usado por um diretório, o nome de domínio personalizado deve ser verificado.

## Nomes de domínio verificados e não verificados

O nome de domínio inicial de um diretório é avaliado implicitamente como verificado pelo Azure AD. Quando um administrador adiciona um nome de domínio personalizado a um Azure AD, ele está inicialmente em um estado não verificado. O Azure AD não permite que nenhum recurso do diretório use um domínio não verificado. Isso garante que somente um diretório possa usar um nome de domínio personalizado e que a organização que possui o Azure AD seja a organização que de fato possui o nome de domínio.

O Azure AD verifica a propriedade de um nome de domínio procurando entradas específicas nos registros DNS (serviço de nomes de domínio) do domínio. Para verificar a propriedade de um domínio, um administrador obtém as entradas DNS do Azure AD pelas quais o Azure AD procurará e adiciona essas entradas aos registros DNS do nome de domínio que são mantidos pelo registrador de nomes de domínio para o domínio em questão. As etapas para verificar um domínio são descritas no artigo para [adicionar um domínio personalizado ao seu diretório do Azure AD](active-directory-add-domain.md).

Adicionar entradas DNS ao Azure AD para verificar a propriedade do domínio não afeta outros serviços de domínio, como emails ou hospedagem na Web.

## Nomes de domínio federados e gerenciados

Um nome de domínio personalizado no Azure AD pode ser configurado para fornecer aos usuários uma experiência de logon federado entre o Active Directory local e o Azure AD. Configurar um domínio para federação requer atualizações para recursos privilegiados no Azure AD e também para o Active Directory local. A configuração de um domínio federado deve ser realizada por meio do Azure AD Connect ou usando o PowerShell. A federação de um domínio personalizado não pode ser iniciada no portal clássico do Azure. [Assista a este vídeo para saber mais sobre como configurar o AD FS para entrada do usuário com o Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domínios que não são federados algumas vezes são chamados de domínios gerenciados. O domínio inicial de um diretório do Azure AD é avaliado implicitamente como um domínio gerenciado.

## Nomes de domínio primários

O nome de domínio primário de um diretório é o nome de domínio que é pré-selecionado como valor padrão para a parte de "domínio" do nome de usuário quando um administrador cria um novo usuário no [portal clássico do Azure](https://manage.windowsazure.com/) ou em outro portal, como o portal de administração do Office 365. Um diretório do Azure AD pode ter apenas um nome de domínio primário. Um administrador pode alterar o nome de domínio primário para qualquer domínio personalizado verificado não federado ou para o domínio inicial.

## Nomes de domínio no Azure AD e outros Microsoft Online Services

Um nome de domínio deve ser verificado no Azure AD antes que possa ser usado por outros Microsoft Online Services, como Exchange Online, SharePoint Online e Intune. Normalmente, esses outros serviços exigem que um administrador adicione uma ou mais entradas DNS que são específicas ao serviço.

Um aplicativo Web do Azure usa seu próprio mecanismo para verificar a propriedade de um domínio. Um domínio deve ser verificado para ser usado com o Azure AD, mesmo que tenha sido verificado anteriormente para ser usado por um aplicativo Web do Azure em uma assinatura que se baseia no Azure AD. Um aplicativo Web do Azure pode usar um nome de domínio que foi verificado em um diretório diferente do Azure AD do que o diretório que mantém o aplicativo Web.

## Gerenciando nomes de domínio

Tarefas de gerenciamento de domínio podem ser realizadas no portal clássico do Azure e do PowerShell. Muitas tarefas podem ser realizadas usando a API do Graph do Azure AD (em preview pública).

-   [Adicionando e verificando um nome de domínio personalizado](active-directory-add-domain.md)

-   [Gerenciando domínios no portal clássico do Azure](active-directory-add-manage-domain-names.md)

-   [Como usar o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Usando a API do Graph do Azure AD para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

<!---HONumber=AcomDC_0420_2016-->