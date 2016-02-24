<properties
	pageTitle="Visualização do Active Directory B2C do Azure: Multi-Factor Authentication | Microsoft Azure"
	description="Como habilitar o Multi-Factor Authentication em aplicativos voltados para o consumidor protegidos pelo Active Directory B2C do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: habilitar o Multi-Factor Authentication em seus aplicativos voltados para o consumidor

O Active Directory (AD) B2C do Azure se integra diretamente ao [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) para facilitar a adição de uma segunda camada de segurança para as experiências de inscrição e entrada em seus aplicativos voltados para o consumidor. E você pode fazer isso sem escrever uma única linha de código. Atualmente damos suporte a chamada telefônica e mensagem de texto como opções de verificação. Se você já criou as políticas de inscrição e de entrada (conforme descrito [neste artigo]()), pode ativar a autenticação multifator conforme mostrado nas seções a seguir.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]O Multi-Factor Authentication pode também ser ativada durante a criação de políticas de inscrição e de entrada, e não somente na edição de políticas existentes.

Ao utilizar esse recurso, os aplicativos podem manipular cenários da seguinte forma, em que:

- Não é necessária a autenticação multifator para acessar um aplicativo, mas o é para acessar outro. Por exemplo, o consumidor pode entrar em um aplicativo de seguro automático com uma conta local ou social, mas precisa verificar o número de telefone antes de acessar o aplicativo de seguro residencial registrado no mesmo diretório.
- Não é necessária a autenticação multifator para acessar um aplicativo em geral, mas o é para acesso às partes confidenciais dele. Por exemplo, o consumidor pode entrar em um aplicativo bancário com uma conta local ou social e verificar o saldo da conta, mas precisa verificar o número de telefone antes de tentar uma transferência bancária.

## Modificar a política de inscrição para habilitar o Multi-Factor Authentication

1. [Siga estas etapas para navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Políticas de inscrição**.
3. Abra a política de inscrição (por exemplo, "B2C\_1\_SiUp") clicando nela.
4. Clique em **Autenticação multifator** e ative o **Estado** colocando em **ON**. Clique em **OK**.
5. Clique em **Salvar** na parte superior da folha. Pronto!

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Isso é o que você deve esperar ver:

Uma conta de consumidor é criada no diretório antes de ocorrer a etapa de autenticação multifator. Durante a etapa, o consumidor é solicitado a fornecer seu número de telefone e a verificá-lo. Se a verificação for bem-sucedida, o número de telefone será anexado à conta de consumidor para uso posterior. Mesmo que o consumidor cancele ou não saia, ele pode ser solicitado a verificar um número de telefone novamente durante o próximo logon (com autenticação multifator habilitada; consulte a próxima seção).

## Modificar a política de entrada para habilitar o Multi-Factor Authentication

1. [Siga estas etapas para navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Políticas de entrada**.
3. Abra a sua política de entrada (por exemplo, "B2C\_1\_SiIn") clicando nela. Clique em **Editar** na parte superior da folha.
4. Clique em **Autenticação multifator** e ative o **Estado** colocando em **ON**. Clique em **OK**.
5. Clique em **Salvar** na parte superior da folha. Pronto!

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Isso é o que você deve esperar ver:

Quando o consumidor entra (usando uma conta local ou social), se um número de telefone verificado está anexado à conta de consumidor, ele deve verificá-lo. Se nenhum número de telefone estiver anexado, o consumidor é solicitado a fornecer um e a verificá-lo; após a verificação bem-sucedida, o número de telefone é anexado à conta de consumidor para uso posterior.

<!---HONumber=AcomDC_0107_2016-->