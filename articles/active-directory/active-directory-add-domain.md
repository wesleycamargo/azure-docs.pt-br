<properties
	pageTitle="Adicionar seu nome de domínio personalizado ao Azure Active Directory | Microsoft Azure"
	description="Como adicionar os nomes de domínio da sua empresa ao Azure Active Directory e como verificar o nome de domínio."
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
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="curtand;jeffsta"/>

# Adicionar seu nome de domínio personalizado ao Azure Active Directory

Você tem um ou mais nomes de domínio que sua organização usa para fazer negócios, e seus usuários entram em sua rede corporativa usando seu nome de domínio corporativo. Agora que você está usando o Azure Active Directory (Azure AD), também poderá adicionar seu nome de domínio corporativo ao AD do Azure também. Isso permite que você atribua os nomes de usuário do seu diretório que sejam familiares para seus usuários, como 'alice@contoso.com'. O processo é simples:

- Adicione seu nome de domínio ao nosso assistente **Adicionar domínio** no portal clássico do Azure

- Obter entrada de DNS no portal clássico do AD do Azure ou na ferramenta Azure AD Connect

- Adicionar a entrada DNS para o nome de domínio ao arquivo de zona DNS no site para o registrador DNS

- Verificar o nome de domínio no portal clássico do AD do Azure ou na ferramenta Azure AD Connect


Até que você verifique seu nome de domínio personalizado, os usuários devem entrar com nomes de usuário, como 'alice@contoso.onmicrosoft.com', que usem o nome de domínio inicial para seu diretório. Se você exigir vários nomes de domínio personalizados, como 'contoso.com' e 'contosobank.com', poderá adicionar até 900 nomes de domínio. Use as mesmas etapas deste artigo para adicionar cada nome de domínio.

## Adicionar um nome de domínio personalizado ao diretório

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta de usuário que seja um administrador global do diretório do AD do Azure.

2. Em **Active Directory**, abra seu diretório e selecione a guia **Domínios**.

3. Na barra de comandos, selecione **Adicionar** e insira o nome do seu domínio personalizado, como ‘contoso.com’. Inclua .com, .net ou outra extensão de nível superior.

4. Se você planeja configurar esse domínio para a [entrada federada](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) em seu Active Directory local, marque a caixa de seleção.

5. Selecione **Adicionar**.

Agora que você adicionou o nome de domínio, o AD do Azure deverá verificar se a sua organização é proprietária do nome de domínio. Antes que o AD do Azure possa executar essa verificação, você deverá adicionar uma entrada DNS ao arquivo de zona DNS para o nome de domínio. Essa tarefa pode ser concluída no site do registrador de nome de domínio para o nome de domínio.

## Obter as entradas DNS para o nome de domínio

As entradas DNS estão na segunda página do assistente **Adicionar domínio**, se você não estiver federando com um Active Directory local do Windows Server.

Se você estiver configurando o domínio para federação, será direcionado para baixar a ferramenta Azure AD Connect. Execute a ferramenta Azure AD Connect para [obter as entradas DNS que você precisa adicionar a seu registrador de nome de domínio](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). A ferramenta Azure AD Connect verificará também o nome de domínio do AD do Azure.

## Adicionar a entrada DNS ao arquivo de zona DNS

1.  Entre no registrador de nome de domínio para o domínio. Se você não tiver permissões suficientes para atualizar a entrada DNS, peça à pessoa ou à equipe que tem esse acesso para adicionar a entrada DNS.

2.  Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo AD do Azure. Essa entrada DNS permitirá que o AD do Azure verifique sua propriedade do domínio. A entrada DNS não altera nenhum comportamento, como o roteamento de emails ou a hospedagem na Web. Pode demorar até uma hora para os registros DNS propagarem-se.

[Instruções para adicionar uma entrada DNS nos registradores de DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificar o nome de domínio com o AD do Azure

Depois de adicionar a entrada DNS, você deverá garantir que o nome de domínio seja verificado pelo AD do Azure. Esta é a etapa final para o êxito.

Se você ainda tiver o assistente **Adicionar domínio** aberto, selecione **Verificar** na terceira página do assistente. Aguarde por até uma hora até que a entrada DNS se propague antes de verificar.

Se o assistente **Adicionar domínio** não estiver mais aberto, você poderá verificar o domínio no [portal clássico do Azure](https://manage.windowsazure.com/):

1.  Entre com uma conta de usuário que seja um administrador global do diretório do AD do Azure.

2.  Abra o diretório e selecione a guia **Domínios**.

3.  Selecione o domínio que você deseja verificar.

4.  Selecione **Verificar** na barra de comandos e selecione **Verificar** na caixa de diálogo.

Parabéns pelo êxito! Agora você pode [atribuir nomes de usuário que incluam o nome de domínio personalizado](active-directory-add-domain-add-users.md). Se você tiver problemas para verificar o nome de domínio, veja nossa seção [Solução de problemas](#troubleshooting).

## Solucionar problemas
Se você não puder verificar um nome de domínio personalizado, haverá algumas causas possíveis. Começaremos com as mais comuns e trabalharemos até as menos comuns.

- Você tentou verificar o nome de domínio antes da entrada DNS poder propagar-se. Espere um pouco e tente novamente.

- O registro de DNS não foi inserido. Verifique a entrada DNS e espere que ela se propague, em seguida, tente novamente.

- O nome de domínio já foi verificado em outro diretório. Localize o nome de domínio, exclua-o do outro diretório e tente novamente.

- O registro de DNS contém um erro. Corrija o erro e tente novamente.

- Você não tem permissão suficiente para atualizar os registros de DNS. Compartilhe as entradas DNS com a pessoa ou a equipe na organização que tem esse acesso e peça que adicione a entrada DNS.


## Próximas etapas

-   [Atribuir nomes de usuário que incluem o nome de domínio personalizado](active-directory-add-domain-add-users.md)
-   [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md)
-   [Saiba mais sobre os conceitos de gerenciamento de domínio no AD do Azure](active-directory-add-domain-concepts.md)
-   [Mostrar a identidade visual de sua empresa quando os usuários entrarem](active-directory-add-company-branding.md)
-   [Usar o PowerShell para gerenciar os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0504_2016-->