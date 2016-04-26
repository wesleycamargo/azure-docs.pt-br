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
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Adicionar seu nome de domínio personalizado para simplificar a entrada usando o Azure Active Directory

Você pode usar seu próprio nome ou nomes de domínio personalizado para melhorar e simplificar a experiência de entrada do usuário e outras experiências no Azure Active Directory (Azure AD). Por exemplo, se sua organização tiver o nome de domínio "contoso.com", os usuários poderão entrar com nomes de usuário com os quais estão familiarizados, como "pedro@contoso.com". Você deve ter acesso a uma conta de usuário que seja um administrador global do diretório do AD do Azure.

Quando você obtiver o diretório de locatário no Azure Active Directory pela primeira vez, seus usuários entrarão com nomes de usuário como 'alice@contoso.onmicrosoft.com'. Neste exemplo, contoso.onmicrosoft.com é um nome de domínio interno inicial que você pode usar até verificar seu nome de domínio personalizado. Uma das suas próximas etapas, então, seria adicionar um nome de domínio personalizado da sua organização, como ‘contoso.com’. Isso permite que você atribua nomes familiares aos usuários, como 'alice@contoso.com'.

Para obter informações sobre como os nomes de domínio são usados no AD do Azure, leia [Conceitos de gerenciamento de domínio no AD do Azure](active-directory-add-domain-concepts.md). A maioria dos administradores executa tarefas de gerenciamento de nomes de domínio no AD do Azure usando o portal clássico do Azure. No entanto, você pode usar o [PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou a [visualização da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) para executar suas tarefas de gerenciamento, se você preferir.

## Adicionar um nome de domínio personalizado ao diretório

Para adicionar um nome de domínio personalizado ao diretório:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta de usuário que seja um administrador global do diretório do AD do Azure.

2. Selecione **Active Directory** na barra de navegação esquerda.

3. Abra seu diretório.

4. Selecione a guia **Domínios**.

5. Na barra de comandos, selecione **Adicionar.**

6. Insira o nome do seu domínio personalizado, como ‘contoso.com’. Inclua .com, .net ou outra extensão de nível superior.

7. Se você planeja configurar esse domínio para [entrada federada](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) em seu Active Directory local, marque a caixa de seleção.

8. Selecione **Adicionar**.

Antes de atribuir nomes de usuário que incluam o nome de domínio personalizado, o AD do Azure deverá verificar se sua organização possui o nome de domínio. Para executar essa verificação, você deve atualizar as entradas DNS no registrador de nomes de domínio para o domínio.

## Obter as entradas DNS que o AD do Azure usará para verificar o nome de domínio

Se você tiver selecionado a opção de configurar o domínio para federação quando adicionou o domínio, você verá uma orientação para baixar a ferramenta Azure AD Connect. Execute a ferramenta Azure AD Connect para [obter as entradas DNS que você precisa adicionar a seu registrador de nome de domínio](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se você não tiver selecionado a opção para entrada federada com o AD do Windows Server, verá as entradas DNS na segunda página do assistente**Adicionar domínio**.

## Adicione as entradas DNS ao arquivo de zona DNS para o domínio

Para adicionar as entradas DNS exigidas pelo AD do Azure:

1.  Entre no registrador de nome de domínio para o domínio. Se você não tiver permissões suficientes para atualizar o arquivo de zona DNS para o domínio, compartilhe as entradas DNS com a pessoa ou a equipe na sua organização que tenha esse acesso e peça a eles para atualizá-lo.

2.  Atualize o arquivo de zona DNS para o domínio, adicionando as entradas DNS fornecidas pelo AD do Azure. Essas entradas DNS permitirão que o AD do Azure verifique sua propriedade do domínio. Elas não alteram qualquer comportamento, como o roteamento de emails ou a hospedagem na Web.

[Instruções para adicionar entradas DNS a registradores de DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificar o domínio no AD do Azure

Depois de adicionar as entradas DNS, você poderá verificar o domínio com o AD do Azure.

Se você tiver selecionado a opção para federar o nome de domínio personalizado, a verificação será feita automaticamente pela ferramenta Azure AD Connect. Execute a ferramenta assim que concluir os pré-requisitos. Caso contrário, verifique o domínio no portal clássico do Azure. Se você ainda tiver o assistente **Adicionar domínio** aberto, poderá clicar no botão Verificar na terceira página do assistente. Pode levar alguns minutos até a propagação dos registros DNS.

Se o assistente **Adicionar domínio** não estiver mais aberto, você poderá verificar o domínio no [portal clássico do Azure](https://manage.windowsazure.com/):

1.  Entre com uma conta de usuário que seja um administrador global do diretório do AD do Azure.

2.  Selecione **Active Directory** na barra de navegação esquerda.

3.  Abra seu diretório.

4.  Selecione a guia **Domínios**.

5.  Na lista de domínios, selecione o domínio que você deseja verificar.

6.  Selecione **Adicionar** na barra de comandos.

7.  Selecione **Verificar** na caixa de diálogo.

Agora você pode atribuir nomes de usuário que incluam o nome de domínio personalizado.

## Adicionar mais nomes de domínio personalizados

Se sua organização usar mais de um nome de domínio personalizado, como 'contoso.com' e 'contosobank.com', você poderá adicionar cada um ao seu diretório do AD do Azure, até um máximo de 900 domínios. Use as mesmas etapas listadas acima para adicionar cada nome de domínio subsequente.

Próximas etapas

-   [Atribuir nomes de usuário com um nome de domínio personalizado](active-directory-add-domain-add-users.md)

-   [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md)

-   [Mostrar a identidade visual da sua empresa quando os usuários entrarem](active-directory-add-company-branding.md)

-   [Conceitos de gerenciamento de domínio no AD do Azure](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0420_2016-->