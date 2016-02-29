<properties
	pageTitle="Visualização do Active Directory B2C do Azure: suporte | Microsoft Azure"
	description="Como arquivar solicitações de suporte para o Active Directory B2C do Azure"
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
	ms.date="02/16/2016"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: como arquivar solicitações de suporte para o Active Directory B2C do Azure

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Você pode arquivar solicitações de suporte para o Active Directory (AD) B2C do Azure no Portal do Azure usando as seguintes etapas:

1. [Siga estas etapas para navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Alterne do locatário B2C para outro que tenha uma assinatura do Azure associada a ele. Normalmente, o segundo é o locatário de funcionário ou o locatário padrão criado quando você se inscreveu para uma assinatura do Azure. Leia [este artigo](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad) para saber mais sobre a relação entre as assinaturas do Azure e os locatários do AD do Azure.

    ![Suporte - Alternar locatários](./media/active-directory-b2c-support/support-switch-dir.png)

3. Depois de alternar os locatários, clique em **Ajuda + suporte**.

    ![Suporte - Ajuda + Suporte](./media/active-directory-b2c-support/support-support.png)

4. Clique em **Nova solicitação de suporte**.

    ![Suporte - Novo](./media/active-directory-b2c-support/support-new.png)

5. Na folha **Noções básicas**, use estes detalhes e clique em **Avançar**.

    - O **Tipo de emissão** é **Técnico**.
	- Escolha a **Assinatura** apropriada.
    - Escolha **Active Directory** em **Serviço**.
    - Escolha o **Plano de suporte** apropriado. Se não tiver um, inscreva-se para um [aqui](https://azure.microsoft.com/pt-BR/support/plans/).

    ![Suporte - Noções básicas](./media/active-directory-b2c-support/support-basics.png)

6. Na folha **Problema**, use estes detalhes e clique em **Avançar**.

    - Escolha o nível de **Gravidade** apropriado.
    - **Tipo de problema** é **Visualização B2C**
    - Escolha a **Categoria** apropriada.
	- Descreva seu problema no campo **Detalhes**. Forneça detalhes como o nome do locatário B2C, a descrição do problema, a mensagens de erro, as IDs de Correlação (se estiverem disponíveis) e assim por diante.
    - Forneça a data e hora (incluindo o fuso horário) de quando o problema ocorreu no campo **Período**.
    - Carregue todas as capturas de tela e arquivos que você acha que ajudariam na resolução do problema em **Carregamento do arquivo**.

    ![Suporte - Problema](./media/active-directory-b2c-support/support-problem.png)

7. Na folha **Contato**, adicione suas informações de contato. Clique em **Criar**. *Observação: durante a visualização, o suporte para o AD do Azure B2C só está disponível em inglês.*

    ![Suporte - Contato](./media/active-directory-b2c-support/support-contact.png)

8. Depois de enviar a solicitação de suporte, você poderá monitorá-la clicando em **Ajuda + suporte** no Quadro inicial e em **Gerenciar solicitações de suporte**.

## Problema conhecido: o preenchimento de uma solicitação de suporte no contexto de um locatário B2C

Se tiver ignorado a etapa 2 descrita acima e tentar criar uma solicitação de suporte no contexto do seu locatário B2C, você verá o erro a seguir.

> [AZURE.IMPORTANT]
Não tente se inscrever em uma nova assinatura do Azure em seu locatário B2C.

![Suporte — sem assinatura](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0218_2016-->