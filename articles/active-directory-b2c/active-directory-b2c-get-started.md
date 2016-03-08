<properties
	pageTitle="Visualização Azure Active Directory B2C: criar um locatário do Azure Active Directory B2C | Microsoft Azure"
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
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>

# Visualização do Azure Active Directory B2C: criar um locatário do Azure AD B2C

Para começar a usar o Azure AD (Microsoft Azure Active Directory) B2C, siga as três etapas descritas neste artigo.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Etapa 1: inscrever-se para uma assinatura do Azure

Se você já tiver uma assinatura do Azure, ignore esta etapa. Se não tiver, inscreva-se para obter uma [assinatura do Azure](../active-directory/sign-up-organization.md) e receber acesso ao Azure AD B2C.

> [AZURE.NOTE]
No momento, você pode usar a visualização do Azure AD B2C gratuitamente, porém seu uso é limitado a 50.000 usuários por locatário. Uma assinatura do Azure é necessária para acessar o [Portal Clássico do Azure](http://manage.windowsazure.com/).

## Etapa 2: criar um locatário do Azure AD B2C

Use as seguintes etapas para criar um novo locatário B2C do AD do Azure. Os recursos B2C no momento não podem ser ativados em seus diretórios existentes, se houver.

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.
2. Clique em **Novo** > **Serviços de Aplicativos** > **Active Directory** > **Diretório** > **Criação Personalizada**.

    ![Captura de tela do início da criação de um locatário](./media/active-directory-b2c-get-started/new-directory.png)

3. Escolha o **Nome**, **Nome de Domínio** e **País ou Região** para seu locatário.
4. Marque a opção que indica **Este é um diretório B2C**.
5. Clique na marca de seleção para concluir a ação.

    ![Captura de tela da marca de seleção para criar um diretório do B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. O locatário foi criado e aparecerá na extensão do Active Directory. Um Administrador Global do locatário também pode ser criado. Você pode adicionar outros Administradores Globais conforme necessário.

    > [AZURE.IMPORTANT]
    Pode levar até dois minutos para o seu locatário ser criado. Se você enfrentar problemas durante a criação do locatário, consulte [Criando um locatário do Azure AD ou um locatário Azure AD B2C - problemas e resoluções](active-directory-b2c-support-create-directory.md) para ver as diretrizes.

## Etapa 3: navegar até a folha de Recursos do B2C no Portal do Azure

1. Navegue até a extensão do Active Directory na barra de navegação do lado esquerdo.
2. Encontre o locatário na guia **Diretório** e clique nele.
3. Clique na guia **Configurar**.
4. Clique no link **Gerenciar configurações de B2C** na seção **Administração de B2C**.

    ![Captura de tela da configuração do diretório do B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. O Portal do Azure mostrando a folha de recursos do B2C será aberto em uma nova janela ou guia do navegador.

    > [AZURE.IMPORTANT]
    Há um problema conhecido em que essa página não carrega corretamente (para um pequeno número de locatários). Atualizar o navegador deve corrigi-lo. Se isso não ocorrer, entre em contato com o Suporte do Azure.

6. Fixe essa folha no seu Quadro inicial para facilitar o acesso. (A ferramenta Fixar é marcada em vermelho no canto superior direito da folha de recursos).

    ![Captura de tela da folha de recursos do B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Você pode gerenciar usuários e grupos, autoatendimento para configuração de redefinição de senha e recursos de identidade visual da empresa do seu locatário no [Portal Clássico do Azure](https://manage.windowsazure.com/).

## Próximas etapas

Saiba como registrar um aplicativo com o Azure AD B2C e compilar um aplicativo de Início Rápido lendo a [Visualização do Azure Active Directory B2C: registrar seu aplicativo](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0302_2016-->