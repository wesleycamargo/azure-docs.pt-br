<properties
	pageTitle="Adicionar seu nome de domínio personalizado à visualização do Azure Active Directory | Microsoft Azure"
	description="Como adicionar os nomes de domínio da sua empresa ao Azure Active Directory e como verificar o nome de domínio."
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
	ms.date="09/12/2016"
	ms.author="curtand"/>

# Adicionar um nome de domínio personalizado à visualização do Azure Active Directory

Você tem um ou mais nomes de domínio que sua organização usa para fazer negócios, e seus usuários entram em sua rede corporativa usando seu nome de domínio corporativo. Agora que você está usando a visualização do Azure Active Directory (Azure AD), também poderá adicionar seu nome de domínio corporativo ao AD do Azure também. [O que está na visualização?](active-directory-preview-explainer.md) Isso permite que você atribua os nomes de usuário do seu diretório que sejam familiares para seus usuários, como 'alice@contoso.com'. O processo é simples:

1. Adicionar o nome de domínio personalizado ao diretório
2. Adicionar uma entrada DNS para o nome de domínio no registrador de nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

## Como adicionar um nome de domínio?

1.  Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global para o diretório.

2.  Selecione **Mais serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.

    ![Abrindo o gerenciamento de usuários](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Na folha ***nome do diretório***, selecione **Nomes de domínio**.

4. Na folha ***nome do diretório* -nomes de domínio**, selecione o comando **Adicionar**.

  ![Selecionando o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)

5. Na folha **Nome de domínio**, insira o nome do seu domínio personalizado na caixa, como "contoso.com" e, em seguida, selecione **Adicionar domínio**. Inclua .com, .net ou outra extensão de nível superior.

6. Na folha ***domainname*** (ou seja, a folha que é aberta com o novo nome de domínio no título), obtenha informações de entrada de DNS que o Azure AD usará para verificar se sua organização possui o nome de domínio personalizado.

  ![obter informações de entrada DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Agora que você adicionou o nome de domínio, o AD do Azure deverá verificar se a sua organização é proprietária do nome de domínio. Antes que o AD do Azure possa executar essa verificação, você deverá adicionar uma entrada DNS ao arquivo de zona DNS para o nome de domínio. Essa tarefa pode ser concluída no site do registrador de nome de domínio para o nome de domínio.

## Adicione a entrada DNS no registrador de nome de domínio para o domínio

A próxima etapa para usar o nome de domínio personalizado com o Azure AD é atualizar o arquivo de zona DNS do domínio. Isso habilita o Azure AD a verificar se a organização possui o nome de domínio personalizado.

1.  Entre no registrador de nome de domínio para o domínio. Se você não tem acesso para atualizar a entrada DNS, peça à pessoa ou à equipe que tem esse acesso para concluir a etapa 2 e avisá-lo quando ela for concluída.

2.  Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo AD do Azure. Essa entrada DNS permitirá que o AD do Azure verifique sua propriedade do domínio. A entrada DNS não altera nenhum comportamento, como o roteamento de emails ou a hospedagem na Web.

Para obter ajuda sobre essa adição da entrada DNS, leia [Instruções para adicionar uma entrada DNS em registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificar o nome de domínio com o AD do Azure

Após adicionar a entrada DNS, você está pronto para verificar o nome de domínio com o Azure AD.

Um nome de domínio só poderá ser verificado depois que os registros DNS forem propagados. Essa propagação geralmente leva apenas alguns segundos, mas às vezes pode levar uma hora ou mais. Se a verificação não funcionar na primeira vez, tente novamente mais tarde.

1.  Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global para o diretório.

2.  Selecione **Procurar**, insira Gerenciamento de Usuário na caixa de texto e, em seguida, selecione **Enter**.

    ![Abrindo o gerenciamento de usuários](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Na folha **Gerenciamento de usuário – Nomes de domínio**, selecione o nome de domínio não verificado que você deseja verificar.

4. Na folha ***domainname*** (ou seja, a folha que é aberta com o novo nome de domínio no título), selecione **Verificar** para concluir a verificação.

Agora você pode [atribuir nomes de usuário que incluam o nome de domínio personalizado](active-directory-create-users-azure-portal.md).

## Solucionar problemas

Se você não puder verificar um nome de domínio personalizado, tente o seguinte. Começaremos com as mais comuns e trabalharemos até as menos comuns.

1.	**Aguarde uma hora**. Os registros DNS precisam ser propagados para que o Azure AD possa verificar o domínio. Isso pode levar uma hora ou mais.

2.	**Verifique se o registro DNS foi inserido e se está correto**. Conclua essa etapa no site do registrador de nome de domínio do domínio. O Azure AD não poderá verificar o nome de domínio se a entrada DNS não estiver presente no arquivo de zona DNS ou se não for uma correspondência exata da entrada DNS que o Azure AD forneceu. Se você não tiver acesso para atualizar os registros DNS parado domínio no registrador de nome de domínio, compartilhe a entrada DNS com a pessoa ou equipe em sua organização que tem acesso a isso e peça-lhe que adicione a entrada DNS.

3.	**Exclua o nome de domínio de outro diretório no Azure AD**. Um nome de domínio pode ser verificado apenas em um único diretório. Se um nome de domínio tiver sido verificado anteriormente em outro diretório, deverá ser excluído de lá para poder ser verificado no novo diretório. Para saber mais sobre a exclusão de nomes de domínio, leia [Gerenciar nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md).

## Adicionar mais nomes de domínio personalizados

Se sua organização usa vários nomes de domínio personalizados, como 'contoso.com' e 'contosobank.com', você pode adicionar até 900 nomes de domínio. Use as mesmas etapas deste artigo para adicionar cada nome de domínio.

## Próximas etapas

[Gerenciar nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->