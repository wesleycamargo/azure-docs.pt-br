<properties
	pageTitle="Modelo de Aplicativo v2.0 | Microsoft Azure"
	description="Como registrar um aplicativo com a Microsoft para habilitar a entrada e integrar aplicativos do modelo de aplicativo v2.0."
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
	ms.date="11/10/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: Como registrar um aplicativo com a Microsoft

Para criar um aplicativo que aceite entrada do AD do Azure e do MSA, você primeiro precisará registrar um aplicativo com a Microsoft. Você não poderá usar nenhum aplicativo existente que já tenha com o AD do Azure ou MSA; é necessário criar um novo.

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço do AD do Azure disponível ao público geral, consulte o [Guia do Desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Visite o Portal de Registro de Aplicativo da Microsoft
Primeiro as prioridades, navegue até [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Este é o novo portal de registro de aplicativo onde você pode gerenciar todos os seus aplicativos Microsoft.

Entrar com uma conta da Microsoft pessoal, profissional ou escolar. Se você não tiver uma, inscreva-se para uma nova conta pessoal. Vá em frente, não vai demorar muito. Vamos aguardar aqui.

Pronto? Você deve agora estar olhando a lista de aplicativos da Microsoft, que provavelmente está vazia. Vamos mudar isso.

<!-- TODO: Verify strings here -->
Clique em **Adicionar um aplicativo** e dê um nome a ele. O portal atribuirá ao seu aplicativo uma Id globalmente exclusiva do aplicativo que você usará posteriormente em seu código. Se seu aplicativo incluir um componente do servidor que precisa de tokens de acesso para chamar APIs (pense: Office, Azure ou terceiros), você também desejará criar um ** Segredo do Aplicativo** aqui.<!-- TODO: Link for app secrets -->

Em seguida, adicione as plataformas que seu aplicativo usará. Para aplicativos baseados na Web, forneça um **URI de redirecionamento** onde é possível enviar mensagens de entrada. Para aplicativos móveis, copie o URI de redirecionamento padrão criado automaticamente para você.

Opcionalmente, você pode personalizar a aparência de sua página de entrada na Seção do perfil. Certifique-se de clicar em **Salvar**antes de continuar.

## Criar um Aplicativo de Início Rápido
Agora que você tem um aplicativo da Microsoft, pode concluir um dos nossos tutoriais de início rápido para começar a usar o modelo de aplicativo v2.0. Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=Nov15_HO3-->