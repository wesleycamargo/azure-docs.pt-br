<properties
	pageTitle="Visão geral conceitual dos nomes de domínio personalizados no Azure Active Directory | Microsoft Azure"
	description="Explica a estrutura conceitual para usar nomes de domínio personalizados no Azure Active Directory, incluindo federação para logon único"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="curtand;jeffsta"/>

# Visão geral conceitual dos nomes de domínio personalizados no Azure Active Directory

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: ele é parte de um nome de usuário ou endereço de email para um usuário, parte do endereço para um grupo e pode ser parte do URI da ID do aplicativo para um aplicativo. Um recurso no Azure AD (Azure Active Directory) pode incluir um nome de domínio já verificado como pertencente ao diretório que contém o recurso. Somente um administrador global pode executar tarefas de gerenciamento de domínio no Azure AD.

Nomes de domínio no Azure AD são globalmente exclusivos. Um nome de domínio pode ser usado por um único Azure AD. Se um diretório do Azure AD verificar um nome de domínio, nenhum outro diretório do Azure AD poderá verificar ou usar o mesmo nome de domínio.

## Nomes de domínio iniciais e personalizados

Todo nome de domínio no Azure AD é um nome de domínio inicial ou um nome de domínio personalizado.

Todo Azure AD vem com um nome de domínio inicial no formato contoso.onmicrosoft.com. Este terceiro nível do nome do domínio, que neste exemplo é "contoso.onmicrosoft.com", foi estabelecido quando o diretório foi criado, normalmente pelo administrador que criou o diretório. O nome de domínio inicial de um diretório não pode ser alterado ou excluído. O nome de domínio inicial, embora seja totalmente funcional, destina-se principalmente a ser usado como um mecanismo de inicialização até que um nome de domínio personalizado seja verificado.

Na maioria dos ambientes de produção, um diretório tem pelo menos um domínio personalizado verificado, como "contoso.com", e é esse domínio personalizado que fica visível para os usuários finais. Um nome de domínio personalizado é um nome de domínio que pertence à uma organização e é usado por ela, como "contoso.com" para ser usado para hospedar seu site da Web. Esse nome de domínio é familiar aos funcionários porque faz parte do nome de usuário que eles usam para entrar na rede corporativa ou para enviar e recuperar emails.

Antes de poder ser usado pelo Azure AD, o nome de domínio personalizado deve ser adicionado ao seu diretório e verificado.

## Nomes de domínio verificados e não verificados

O nome de domínio inicial de um diretório é avaliado implicitamente como verificado pelo Azure AD. Quando um administrador adiciona um nome de domínio personalizado a um Azure AD, ele está inicialmente em um estado não verificado. O Azure AD não permite que nenhum recurso do diretório use um nome de domínio não verificado. Isso garante que somente um diretório possa usar um nome de domínio específico e que a organização que usa o nome de domínio realmente o possui.

O Azure AD verifica a propriedade de um nome de domínio procurando por uma entrada específica nos arquivos de zona de DNS (serviço de nomes de domínio) do domínio. Para verificar a propriedade de nome de domínio, um administrador obtém a entrada DNS do Azure AD que este busca e adiciona essa entrada ao arquivo de zona DNS para o nome de domínio. O arquivo de zona DNS é mantido pelo registrador de nomes de domínio para o domínio em questão. As etapas para verificar um domínio são descritas no artigo para [adicionar um domínio personalizado ao seu diretório do Azure AD](active-directory-add-domain.md).

Adicionar uma entrada DNS ao arquivo de zona para o nome de domínio não afeta outros serviços de domínio como email ou hospedagem na Web.

## Nomes de domínio federados e gerenciados

Um nome de domínio personalizado no Azure AD pode ser configurado para fornecer aos usuários uma experiência de logon federado entre o Active Directory local e o Azure AD. Configurar um domínio para federação requer atualizações para recursos privilegiados no Azure AD e também para o Active Directory do Windows Server. A configuração de um domínio federado deve ser realizada por meio do Azure AD Connect ou usando o PowerShell. A federação de um domínio personalizado não pode ser iniciada no portal clássico do Azure. [Assista a este vídeo para saber mais sobre como configurar o AD FS para entrada do usuário com o Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domínios que não são federados algumas vezes são chamados de domínios gerenciados. O domínio inicial de um diretório do Azure AD é avaliado implicitamente como um domínio gerenciado.

## Nomes de domínio primários

O nome de domínio primário de um diretório é o nome de domínio que é pré-selecionado como valor padrão para a parte de "domínio" do nome de usuário quando um administrador cria um novo usuário no [portal clássico do Azure](https://manage.windowsazure.com/) ou em outro portal, como o portal de administração do Office 365. Um diretório pode ter apenas um nome de domínio primário. Um administrador pode alterar o nome de domínio primário para qualquer domínio personalizado verificado não federado ou para o domínio inicial.

## Nomes de domínio no Azure AD e outros Microsoft Online Services

Um nome de domínio deve ser verificado no Azure AD antes que possa ser usado por outro Microsoft Online Service, como Exchange Online, SharePoint Online e Intune. Normalmente, esses outros serviços exigem que um administrador adicione uma ou mais entradas DNS que são específicas ao serviço.

Um aplicativo Web do Azure usa seu próprio mecanismo para verificar a propriedade de um domínio. Um domínio deve ser verificado para ser usado com o Azure AD, mesmo que tenha sido verificado anteriormente para ser usado por um aplicativo Web do Azure em uma assinatura que se baseia no Azure AD. Um aplicativo Web do Azure pode usar um nome de domínio que foi verificado em um diretório diferente do diretório que mantém o aplicativo Web.

## Gerenciando nomes de domínio

Tarefas de gerenciamento de domínio podem ser realizadas no portal clássico do Azure e do PowerShell. Muitas tarefas podem ser realizadas usando a API do Graph do Azure AD (em preview pública).

-   [Adicionando e verificando um nome de domínio personalizado](active-directory-add-domain.md)

-   [Gerenciando domínios no portal clássico do Azure](active-directory-add-manage-domain-names.md)

-   [Como usar o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Usando a API do Graph do Azure AD para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

<!---HONumber=AcomDC_0928_2016-->