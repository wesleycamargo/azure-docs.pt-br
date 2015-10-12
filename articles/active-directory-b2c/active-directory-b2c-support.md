<properties
	pageTitle="Visualização do Active Directory B2C do Azure: suporte | Microsoft Azure"
	description="Como arquivar solicitações de suporte para o Active Directory B2C do Azure"
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
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: como arquivar solicitações de suporte para o Active Directory B2C do Azure

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Você pode arquivar solicitações de suporte para o Active Directory (AD) B2C do Azure no portal de visualização do Azure usando as seguintes etapas:

1. [Navegue até a folha de recursos do B2C no portal de visualização do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Alterne do locatário B2C para outro que tenha uma assinatura do Azure associada a ele. Normalmente, o segundo é o locatário de funcionário ou o locatário padrão criado quando você se inscreveu para uma assinatura do Azure. Leia [este artigo](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad) para saber mais sobre a relação entre as assinaturas do Azure e os diretórios do Azure AD.

    > [AZURE.IMPORTANT]Esta etapa é obrigatória. O processo falhará se você ignorar esta etapa.

    ![Suporte — alternar diretórios](./media/active-directory-b2c-support/support-switch-dir.png)

3. Depois de alternar os diretórios, clique em **Ajuda + suporte**.

    ![Suporte — suporte](./media/active-directory-b2c-support/support-support.png)

4. Siga as etapas descritas [neste artigo](http://blogs.msdn.com/b/mast/archive/2013/10/24/windows-azure-technical-support-for-msdn-technet-or-mpn-users-and-partners.aspx) para arquivar uma solicitação de suporte no Azure AD B2C. Use estes detalhes para concluir as etapas:

    - O **Tipo de solicitação** é **Técnico**.
	- O **Recurso** é **Active Directory**.
	- Na folha **Categoria do problema**, selecione **Visualização do B2C** como o **Tipo de problema** e a **Categoria** apropriada.
	- Na folha **Descrição**, descreva seu problema com detalhes. Na caixa de texto **Recurso**, forneça o nome do seu locatário B2C; por exemplo, contosob2c.onmicrosoft.com.

5. Depois de enviar a solicitação de suporte, você poderá monitorá-la clicando em **Ajuda + suporte** no Quadro inicial e em **Gerenciar solicitações de suporte**.

## Problema conhecido: o preenchimento de uma solicitação de suporte no contexto de um locatário B2C

Se tiver ignorado a etapa 2 descrita acima e tentar criar uma solicitação de suporte no contexto do seu locatário B2C, você verá o erro a seguir.

> [AZURE.IMPORTANT]Não tente se inscrever em uma nova assinatura do Azure em seu locatário B2C.

![Suporte — sem assinatura](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=Oct15_HO1-->