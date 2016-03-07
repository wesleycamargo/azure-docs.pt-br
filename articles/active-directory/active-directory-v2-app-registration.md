<properties
	pageTitle="Registro de aplicativo v2.0 | Microsoft Azure"
	description="Como registrar um aplicativo na Microsoft para habilitar a entrada e acessar os serviços da Microsoft usando o ponto de extremidade v2.0"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Como registrar um aplicativo com o ponto de extremidade v2.0

Para criar um aplicativo que aceite entrada do AD do Azure e do MSA, você primeiro precisará registrar um aplicativo com a Microsoft. Você não poderá usar nenhum dos seus aplicativos existentes com o Azure AD ou MSA, será necessário criar um novo.

> [AZURE.NOTE]
	Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).

## Visite o Portal de Registro de Aplicativos da Microsoft
Primeiro as prioridades, navegue até [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Este é o novo portal de registro de aplicativos em que você pode gerenciar todos os seus aplicativos Microsoft.

Entrar com uma conta da Microsoft pessoal, profissional ou escolar. Se você não tiver uma, inscreva-se para uma nova conta pessoal. Vá em frente, não vai demorar muito. Vamos aguardar aqui.

Pronto? Você deve agora estar olhando a lista de aplicativos da Microsoft, que provavelmente está vazia. Vamos mudar isso.

Clique em **Adicionar um aplicativo** e dê um nome a ele. O portal atribuirá ao seu aplicativo uma Id globalmente exclusiva do aplicativo que você usará posteriormente em seu código. Se seu aplicativo incluir um componente do servidor que precisa de tokens de acesso para chamar APIs (tais como: Office, Azure ou sua própria API Web), também convém criar um **Segredo de Aplicativo** aqui. <!-- TODO: Link for app secrets -->

Em seguida, adicione as Plataformas que seu aplicativo usará.

- Para aplicativos baseados na Web, forneça um **URI de Redirecionamento** em que é possível enviar mensagens de entrada.
- Para aplicativos móveis, copie o URI de redirecionamento criado automaticamente para você.

Opcionalmente, você pode personalizar a aparência de sua página de entrada na Seção do perfil. Certifique-se de clicar em **Salvar**antes de continuar.

> [AZURE.NOTE] Quando você cria um aplicativo usando [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com), ele será registrado no locatário inicial da conta que você usa para entrar no portal. Isso significa que não é possível registrar um aplicativo no seu locatário do Azure AD usando uma conta pessoal da Microsoft. Se você quiser registrar um aplicativo em um determinado locatário explicitamente, entre com uma conta criada originalmente no locatário em questão.

## Compilar um aplicativo de início rápido
Agora que você tem um aplicativo da Microsoft, poderá concluir um dos nossos tutoriais de início rápido da v2.0. Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_0224_2016-->