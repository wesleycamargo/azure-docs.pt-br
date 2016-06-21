<properties
	pageTitle="Adicionar seu nome de domínio personalizado e configurar o logon federado no Azure Active Directory | Microsoft Azure"
	description="Como adicionar nomes de domínio de sua empresa ao Azure Active Directory e como configurar o logon federado entre o Azure Active Directory e sua solução de federação local."
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
	ms.topic="get-started-article"
	ms.date="06/08/2016"
	ms.author="curtand;jeffsta"/>

# Adicionar seu nome de domínio personalizado ao Azure Active Directory

Você pode configurar um nome de domínio personalizado, como ‘contoso.com’, para que os usuários em contoso.com possam ter uma experiência de logon único federado da rede corporativa. Se você tiver os Serviços de Federação do Active Directory (AD FS) ou um servidor de federação diferente em execução na rede corporativa, poderá configurar o Azure AD para usar o nome de domínio personalizado usando a ferramenta Azure AD Connect. Você também pode usar o Azure AD Connect para implantar um novo ambiente do AD FS e configurá-lo para logon único federado ao Azure AD.

Se você não tem e não planeja implantar o AD FS ou outro servidor de federação, siga estas instruções: [Adicionar um nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md).

## Adicionar um nome de domínio personalizado ao diretório

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta de usuário que seja um administrador global do diretório do AD do Azure.

2. No **Active Directory**, abra o diretório e selecione a guia **Domínios**.

3. Na barra de comandos, selecione **Adicionar** e insira o nome do seu domínio personalizado, como ‘contoso.com’. Inclua .com, .net ou outra extensão de nível superior.

4. Marque a caixa de seleção **Pretendo configurar esse domínio para logon único com meu Active Directory local**.

5. Selecione **Adicionar**.

Execute a ferramenta Azure AD Connect para obter a entrada DNS que o Azure AD usará para verificar o domínio. Você verá a entrada DNS na etapa **Domínio do Azure AD** no assistente. Você pode ver como é essa etapa do assistente [nessas instruções](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se você não tiver a ferramenta Azure AD Connect, poderá [baixá-la aqui](http://go.microsoft.com/fwlink/?LinkId=615771).

## Adicione a entrada DNS no registrador de nome de domínio para o domínio

A próxima etapa para usar o nome de domínio personalizado com o Azure AD é atualizar o arquivo de zona DNS do domínio. Isso habilita o Azure AD a verificar se a organização possui o nome de domínio personalizado.

1. Entre no site do registrador de nome de domínio de seu nome de domínio. Se você não tiver acesso para fazer isso, peça à pessoa ou à equipe em sua organização que tem esse acesso para concluir a etapa 2 e avisá-lo quando ela for concluída.

2. Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo AD do Azure. Essa entrada DNS permitirá que o AD do Azure verifique sua propriedade do domínio. A entrada DNS não altera nenhum comportamento, como o roteamento de emails ou a hospedagem na Web.

Para obter ajuda sobre essa etapa, leia [Instruções para adicionar uma entrada DNS em registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificar o nome de domínio com o AD do Azure

Após adicionar a entrada DNS, você está pronto para verificar o nome de domínio com o Azure AD.

Para verificar o domínio, selecione **Avançar** na etapa **Domínio do Azure AD** do assistente do Azure AD Connect. O Azure AD procurará a entrada DNS no arquivo de zona DNS do domínio. O Azure AD só verifica o nome de domínio depois que os registros DNS são propagados. A propagação geralmente leva apenas alguns segundos, mas às vezes pode levar uma hora ou mais. Se a verificação não funcionar na primeira vez, tente novamente mais tarde.

Em seguida, continue com as etapas restantes no assistente do Azure AD Connect. Isso sincronizará os usuários do Windows Server AD com o Azure AD. Os usuários sincronizados no domínio que você configurou para federação poderão obter uma experiência de logon único federado da rede corporativa no Azure AD.

## Solucionar problemas

Se você não puder verificar um nome de domínio personalizado, tente o seguinte. Começaremos com as mais comuns e trabalharemos até as menos comuns.

1.	**Aguarde uma hora**. Os registros DNS precisam ser propagados para que o Azure AD possa verificar o domínio. Isso pode levar uma hora ou mais.

2.	**Verifique se o registro DNS foi inserido e se está correto**. Conclua essa etapa no site do registrador de nome de domínio do domínio. O Azure AD não poderá verificar o nome de domínio se a entrada DNS não estiver presente no arquivo de zona DNS ou se não for uma correspondência exata da entrada DNS que o Azure AD forneceu. Se você não tiver acesso para atualizar os registros DNS parado domínio no registrador de nome de domínio, compartilhe a entrada DNS com a pessoa ou equipe em sua organização que tem acesso a isso e peça-lhe que adicione a entrada DNS.

3.	**Exclua o nome de domínio de outro diretório no Azure AD**. Um nome de domínio pode ser verificado apenas em um único diretório. Se um nome de domínio tiver sido verificado anteriormente em outro diretório, deverá ser excluído de lá para poder ser verificado no novo diretório. Para saber mais sobre a exclusão de nomes de domínio, leia [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md).

## Adicionar mais nomes de domínio personalizados

Se sua organização usa vários nomes de domínio personalizados, como 'contoso.com' e 'contosobank.com', você pode adicionar até 900 nomes de domínio. Use as mesmas etapas deste artigo para adicionar cada nome de domínio.

## Próximas etapas

-   [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md)
-   [Saiba mais sobre os conceitos de gerenciamento de domínio no AD do Azure](active-directory-add-domain-concepts.md)
-   [Mostrar a identidade visual de sua empresa quando os usuários entrarem](active-directory-add-company-branding.md)
-   [Usar o PowerShell para gerenciar os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0615_2016-->