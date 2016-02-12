<properties
	pageTitle="Visualização B2C do Active Directory do Azure: Criando um locatário Azure Active Directory B2C | Microsoft Azure"
	description="Um tópico sobre como criar um locatário Active Directory B2C do Azure"
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
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: como criar um locatário AD B2C do Azure

Para começar a usar o Active Directory (AD) B2C do Azure, siga as 3 etapas descritas abaixo.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Etapa 1: fazer uma assinatura do Azure

Se você já tiver uma assinatura do Azure, passe para a próxima etapa. Se não tiver, inscreva-se para obter [uma assinatura do Azure](../active-directory/sign-up-organization.md) e tenha acesso ao Azure AD B2C.

> [AZURE.NOTE]
A visualização do B2C do AD do Azure é atualmente livre para uso, mas limitada (até 50.000 usuários por Locatário). Uma assinatura do Azure é necessária para acessar o [Portal Clássico do Azure](http://manage.windowsazure.com/).

## Etapa 2: Criar um locatário B2C do AD do Azure

Use as seguintes etapas para criar um novo locatário B2C do AD do Azure. Os recursos B2C no momento não podem ser ativados em seus diretórios existentes, se houver.

1. Entre no [Portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.
2. Clique em **Novo** > **Serviços de Aplicativos** > **Active Directory** > **Diretório** > **Criação Personalizada**.

    ![Criar locatário](./media/active-directory-b2c-get-started/new-directory.png)

3. Escolha o **nome**, **nome de domínio** e **país ou região** para seu locatário.
4. Marque a opção que diz "**Este é um diretório B2C**".
5. Clique na marca de seleção para concluir a ação.

    ![Criar locatário B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. O locatário foi criado e aparecerá na extensão do Active Directory. Um Administrador Global do locatário também pode ser criado. Você pode adicionar outros Administradores Globais conforme necessário.

    > [AZURE.IMPORTANT]
    Pode levar até dois minutos para o seu locatário ser criado. Se você enfrentar problemas durante a criação de locatário, consulte este [artigo](active-directory-b2c-support-create-directory.md) para obter orientação.

## Etapa 3: navegar até a folha dos Recursos B2C no Portal do Azure

1. Navegue até a extensão do Active Directory na barra de navegação do lado esquerdo.
2. Encontre seu locatário na guia **Diretório** e clique nele.
3. Clique na guia **Configurar**.
4. Clique no link **Gerenciar configurações B2C** na seção **Administração B2C**.

    ![Criar locatário B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. O Portal do Azure com a folha de recursos B2C será aberto em uma janela ou em uma nova guia do navegador.

    > [AZURE.IMPORTANT]
    Há um problema conhecido em que essa página não carrega corretamente (para um pequeno número de locatários). Atualizar o navegador deve corrigi-lo. Caso contrário, contate o suporte.

5. Fixe essa folha (veja o canto superior direito) no Quadro inicial para facilitar o acesso.

    ![Folha de recursos B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Você pode gerenciar usuários e grupos, autoatendimento para configuração de redefinição de senha e recursos de marcas da empresa do seu locatário no [Portal Clássico do Azure](https://manage.windowsazure.com/).

## Próximas etapas

Mover para [registrar um aplicativo com AD B2C do Azure e criar um aplicativo de início rápido](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0204_2016-->