<properties
	pageTitle="Visualização do Active Directory B2C do Azure: criando um diretório do Active Directory B2C do Azure | Microsoft Azure"
	description="Um tópico sobre como criar um diretório do Active Directory B2C do Azure"
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

# Visualização do Active Directory B2C do Azure: como criar um diretório do Azure AD B2C

Para começar a usar o Active Directory (AD) B2C do Azure, siga as 3 etapas descritas abaixo.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Etapa 1: fazer uma assinatura do Azure

Se você já tiver uma assinatura do Azure, passe para a próxima etapa. Se não tiver, inscreva-se para obter [uma assinatura do Azure](sign-up-organization.md) e tenha acesso ao Azure AD B2C.

> [AZURE.NOTE]A visualização do Azure AD B2C é atualmente de uso gratuito, mas limitado (até 50 mil usuários por diretório). Uma assinatura do Azure é necessária para acessar o [portal do Azure](http://manage.windowsazure.com/).

## Etapa 2: criar um diretório do Azure AD B2C

Use as seguintes etapas para criar um novo diretório do Azure AD B2C. Os recursos B2C no momento não podem ser ativados em seus diretórios existentes, se houver.

1. Entre no [portal do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.
2. Clique em **Novo** > **Serviços de Aplicativos** > **Active Directory** > **Diretório** > **Criação Personalizada**.

    ![Criar diretório](./media/active-directory-b2c-get-started/new-directory.png)

3. Escolha o **nome**, o **nome de domínio** e o **país ou região** para o seu diretório.
4. Marque a opção que diz "**Este é um diretório B2C**".
5. Clique na marca de seleção para concluir a ação.

    ![Criar diretório B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. O diretório foi criado e aparecerá na extensão do Active Directory. Você também se torna um Administrador global do diretório. Você pode adicionar outros Administradores Globais conforme necessário.

    > [AZURE.IMPORTANT]Pode levar até dois minutos para o diretório ser criado. Se você tiver problemas durante a criação de diretório, consulte este [artigo](active-directory-b2c-support-create-directory.md) para obter orientação.

## Etapa 3: navegar até a folha dos Recursos B2C no Portal de Visualização do Azure

1. Navegue até a extensão do Active Directory na barra de navegação à esquerda.
2. Localize o diretório na guia **Diretório** e clique nele.
3. Clique na guia **Configurar**.
4. Clique no link **Gerenciar configurações B2C** na seção **Administração B2C**.

    ![Criar diretório B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. O [portal de visualização do Azure](https://portal.azure.com/) com a folha dos recursos B2C será aberto em uma nova janela ou guia do navegador.
5. Fixe essa folha (veja o canto superior direito) no Quadro inicial para facilitar o acesso.

    ![Folha de recursos B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]Você pode gerenciar usuários e grupos, configuração de autoatendimento para a redefinição de senha do serviço e recursos de identidade visual da empresa no [portal do Azure](https://manage.windowsazure.com/).

## Próximas etapas

Continue para [registrar um aplicativo com o Azure AD B2C e criar um Aplicativo de Início Rápido](active-directory-b2c-app-registration.md).

<!---HONumber=Sept15_HO3-->