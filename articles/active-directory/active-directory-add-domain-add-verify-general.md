<properties
	pageTitle="Adicionar e verificar um nome de domínio personalizado no Active Directory do Azure | Microsoft Azure"
	description="Como adicionar domínios existentes ao Active Directory do Azure como parte da introdução ao AD do Azure. Configure seu domínio personalizado para sincronizar informações de conta de usuário com sua infraestrutura de identidade local."
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
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Adicionar e verificar um nome de domínio personalizado no Active Directory do Azure

Para adicionar um nome de domínio personalizado e verificá-lo para uso com o Active Directory do Azure, você deve concluir as etapas a seguir.

1.  Faça uma das opções a seguir:

    -   [Adicionar um nome de domínio personalizado que será federado com o Active Directory local](#add-a-custom-domain-name-that-will-be-federated)

    -   [Adicionar um nome de domínio personalizado que não será federado com o Active Directory local](#add-a-custom-domain-name-that-will-not-be-federated)

2.  Verificar o nome de domínio personalizado.

    -   Adicionar as entradas DNS que o AD do Azure usará para verificar se sua organização tem o nome de domínio personalizado no registrador de nome de domínio para seu domínio.

    -   Verifique o domínio no AD do Azure.

## Adicionar um nome de domínio personalizado que será federado

Para obter mais informações sobre a integração de diretório local usando o Azure AD Connect, consulte

**Para adicionar um nome de domínio personalizado ao diretório do AD do Azure**

1.  Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta que tenha privilégios de administrador global no AD do Azure.

2.  Selecione Active Directory.

3.  Abra seu diretório.

4.  Selecione a guia **Domínios**.

5.  Na barra de comandos, selecione **Adicionar**.

6.  Insira o nome do seu domínio personalizado. Certifique-se de incluir a extensão .com.

7.  Marque a caixa de seleção **Pretendo configurar esse domínio para logon único com meu Active Directory local**.

8.  Clique em **Adicionar**.

## Adicionar um nome de domínio personalizado que não será federado

A maioria dos nomes de domínio personalizados são domínios de segundo nível "contoso.com"

**Para adicionar o nome de domínio personalizado ao diretório do AD do Azure**

1.  Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta que tenha privilégios de administrador global no AD do Azure.

2.  Selecione Active Directory.

3.  Abra seu diretório.

4.  Selecione a guia **Domínios**.

5.  Na barra de comandos, selecione **Adicionar**.

6.  Insira o nome do seu domínio personalizado. Certifique-se de incluir a extensão .com.

7.  Desmarque a caixa de seleção **Pretendo configurar esse domínio para logon único com meu Active Directory local**.

8.  Selecione **Adicionar**.

9.  Consulte se seu domínio foi adicionado ao diretório.

## Verificar um domínio em qualquer registrador de nome de domínio

Para verificar seu domínio, você cria um registro DNS no registrador de nome de domínio, ou onde seu DNS estiver hospedado e, em seguida, o AD do Azure usa esse registro para confirmar que o domínio é de sua propriedade. [Instruções para adicionar entradas DNS a registradores de DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

Se você já tiver o domínio registrado com um registrador de nome de domínio, os registros DNS necessários já existirão.

Quando você adiciona um domínio personalizado, mas o domínio ainda não foi verificado, o status aparecerá como **Não verificado**.

## Verificar um nome de domínio personalizado que você não realizará federação com um diretório local
Depois que os registros que você criou para seu domínio forem adicionados com êxito por meio do sistema de DNS em seu registrador de domínio, faça o seguinte:

1.  No Active Directory do Azure no [portal clássico do Azure](https://manage.windowsazure.com/), clique em **Domínios**.

2.  Na lista **Domínios**, localize o domínio que você está verificando e, em seguida, com base no portal que você está usando, clique em **Clicar para verificar o domínio** ou **Verificar**.

3.  Siga as instruções fornecidas para completar o processo de verificação.

    -   Se a verificação for bem-sucedida, você será notificado de que o domínio foi adicionado à sua conta.

    -   Se a verificação falhar, as alterações feitas no registrador de domínio poderão precisar de mais tempo para se propagarem. Cancele a verificação e tente novamente mais tarde.

Se mais de 72 horas tiverem passado desde que você fez as alterações no domínio, entre no site do registrador de domínio e verifique se você inseriu as informações de alias corretamente. Se você inseriu as informações incorretamente, deverá remover o registro DNS incorreto e criar um novo com as informações corretas.

## Verifique seu domínio personalizado quanto à federação com o diretório local

1.  Baixar e executar o Azure AD Connect. A ferramenta Azure AD Connect [solicitará que você adicione as entradas DNS fornecidas por ela](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

## Nomes de domínio de terceiro nível

Você pode usar domínios de terceiro nível, como "europe.contoso.com", com o AD do Azure. Para adicionar e usar um domínio de terceiro nível:

1.  Adicione e verifique o domínio de segundo nível "contoso.com"

2.  Adicione todos os subdomínios, como "europe.contoso.com", ao AD do Azure. Quando você adiciona um subdomínio de um domínio de segundo nível verificado, o domínio de terceiro nível é verificado automaticamente pelo AD do Azure. Não é necessário adicionar mais entradas de DNS.

Essas etapas também podem ser realizadas usando o PowerShell e o Graph.

Depois de verificar seu domínio, você pode configurá-lo para operar com suas contas.

## Próximas etapas

- [Como usar nomes de domínio personalizados para simplificar a experiência de conexão para os usuários](active-directory-add-domain.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Atribuir usuários a um domínio personalizado](active-directory-add-domain-add-users.md)
- [Alterar o registrador DNS para o nome de domínio personalizado](active-directory-add-domain-change-registrar.md)
- [Excluir um domínio personalizado no Active Directory do Azure](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0302_2016-->