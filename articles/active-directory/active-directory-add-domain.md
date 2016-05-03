<properties
	pageTitle="Adicionar seu nome de domínio personalizado para simplificar a entrada usando o Azure Active Directory | Microsoft Azure"
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

# Adicionar seu nome de domínio personalizado para simplificar a entrada usando o Azure Active Directory

Quando você obtém pela primeira vez o diretório no Azure Active Directory (Azure AD), uma das primeiras tarefas importantes é verificar um nome de domínio personalizado que sua organização usa, como ‘contoso.com’. Completar esta tarefa permite que você atribua nomes familiares aos usuários, como 'alice@contoso.com'. Até que você verifique seu nome de domínio personalizado, os usuários devem entrar com nomes de usuário, como 'alice@contoso.onmicrosoft.com', que usem o nome de domínio inicial para seu diretório.

## Adicionar um nome de domínio personalizado ao diretório

Para adicionar um nome de domínio personalizado ao diretório:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta de usuário que seja um administrador global do diretório do Azure AD.

2. Selecione **Active Directory** na barra de navegação à esquerda e abra seu diretório.

4. Selecione a guia **Domínios**.

5. Na barra de comandos, selecione **Adicionar.**

6. Insira o nome do seu domínio personalizado, como ‘contoso.com’. Inclua .com, .net ou outra extensão de nível superior.

7. Se você planeja configurar esse domínio para a [entrada federada](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) em seu Active Directory local, marque a caixa de seleção.

8. Selecione **Adicionar**.

Antes de atribuir nomes de usuário que incluam o nome de domínio personalizado, o AD do Azure deverá verificar se sua organização possui o nome de domínio. Para executar essa verificação, você deve adicionar uma entrada DNS no arquivo de zona DNS para o nome de domínio. Essa tarefa pode ser concluída no site do registrador de nome de domínio para o nome de domínio.

## Obter as entradas DNS para o nome de domínio

Se você optou por configurar o domínio da federação, será direcionado para baixar a ferramenta Azure AD Connect. Execute a ferramenta Azure AD Connect para [obter as entradas DNS que você precisa adicionar em seu registrador de nome de domínio](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se você não optar por configurar o domínio da federação, as entradas DNS estarão na segunda página do assistente **Adicionar domínio**.

## Adicionar a entrada DNS ao arquivo de zona DNS

Para adicionar a entrada DNS requerida pelo AD do Azure:

1.  Entre no registrador de nome de domínio para o domínio. Se você não tiver permissões suficientes para atualizar a entrada DNS, peça à pessoa ou à equipe que tem esse acesso para adicionar a entrada DNS.

2.  Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo AD do Azure. Essa entrada DNS permitirá que o AD do Azure verifique sua propriedade do domínio. A entrada DNS não altera nenhum comportamento, como o roteamento de emails ou a hospedagem na Web.

[Instruções para adicionar uma entrada DNS nos registradores de DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificar o nome de domínio com o AD do Azure

Depois de adicionar a entrada DNS, você poderá verificar o nome de domínio com o AD do Azure.

Se você optou por federar o nome de domínio personalizado, a ferramenta Azure AD Connect executará uma verificação automaticamente. Execute a ferramenta assim que concluir os pré-requisitos. Caso contrário, verifique o domínio no portal clássico do Azure. Se você ainda tiver o assistente **Adicionar domínio** aberto, poderá clicar em **Verificar** na terceira página do assistente. Pode demorar até uma hora para os registros DNS propagarem-se.

Se o assistente **Adicionar domínio** não estiver mais aberto, você poderá verificar o domínio no [portal clássico do Azure](https://manage.windowsazure.com/):

1.  Entre com uma conta de usuário que seja um administrador global do diretório do AD do Azure.

2.  Selecione **Active Directory** na barra de navegação à esquerda.

3.  Abra seu diretório.

4.  Selecione a guia **Domínios**.

5.  Na lista de domínios, selecione o domínio que você deseja verificar.

6.  Selecione **Verificar** na barra de comandos.

7.  Selecione **Verificar** na caixa de diálogo.

Agora você pode [atribuir nomes de usuário que incluam o nome de domínio personalizado](active-directory-add-domain-add-users.md).

## Adicionar mais nomes de domínio personalizados

Se sua organização usar vários nomes de domínio personalizados, como 'contoso.com' e 'contosobank.com', você poderá adicioná-los ao seu diretório do AD do Azure. O máximo é de 900 nomes de domínio. Use as mesmas etapas para adicionar cada nome de domínio subsequente.

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

-   [Mostrar a identidade visual de sua empresa quando os usuários entrarem](active-directory-add-company-branding.md)

-   [Usar o PowerShell para gerenciar os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Saiba mais sobre os conceitos de gerenciamento de domínio no AD do Azure](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0427_2016-->