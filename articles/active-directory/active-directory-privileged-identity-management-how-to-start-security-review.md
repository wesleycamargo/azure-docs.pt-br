<properties
   pageTitle="Como iniciar uma análise de acesso | Microsoft Azure"
   description="Saiba como criar uma análise de acesso para identidades com privilégios com o aplicativo Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# Como iniciar uma análise de acesso no Azure AD Privileged Identity Management

As atribuições de função se tornam "obsoletas" quando os usuários têm acesso privilegiado de que não precisam mais. Para reduzir o risco associado a essas atribuições de função obsoletas, os administradores de função com privilégios devem revisar regularmente as funções que os usuários receberam. Este documento aborda as etapas para iniciar uma revisão de segurança no Azure AD PIM (Privileged Identity Management).

## Iniciar uma revisão de segurança:
> [AZURE.NOTE] Se você não adicionou o aplicativo PIM ao painel no portal do Azure, consulte as etapas em [Getting Started with Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) (Introdução ao Azure Privileged Identity Management)

Na página principal do aplicativo PIM, há três maneiras de iniciar uma revisão de segurança:

- **Revisões de acesso** > **Adicionar**
- **Funções** > botão **Examinar**
- Selecione a função específica a ser examinada na lista de funções > botão **Examinar**

Quando você clica no botão **Examinar**, a folha **Iniciar uma revisão de função** é exibida. Nessa folha, você vai configurar a análise com um nome e o limite de tempo, escolher uma função para examinar e decidir quem vai realizar a análise.

![Iniciar uma análise de acesso – captura de tela][1]

### Configurar a análise

Para criar uma análise de acesso, você precisa nomeá-la e definir uma data de início e de término.

![Configurar a análise – captura de tela][2]

Deixe a duração da análise longa o suficiente para que os usuários a concluam. Se você terminar antes da data de término, sempre poderá interromper a análise antes.

### Escolher uma função para analisar

Cada análise se concentra em apenas uma função. A menos que tenha iniciado a análise de acesso em uma folha de função específica, você precisará escolher uma função agora.

1. Navegue até **Analisar associação de função**

    ![Examinar a associação de função – captura de tela][3]

2. Escolha uma função na lista.

### Decidir quem executará a análise

Há duas opções para executar uma análise. Você pode fazer isso sozinho, aprovando ou negando acesso para todos os usuários em uma função. Ou, você pode fazer com que cada usuário examine seu próprio acesso.

1. Navegue até **Selecionar revisores**

    ![Selecionar revisores – captura de tela][4]

2. Escolha uma das opções:
    - **Me (Eu)**: útil se você deseja visualizar como as análises de acesso funcionam ou se deseja realizar a análise em nome de pessoas que não podem fazê-lo.
    - **Members review themselves (Os membros realizam a própria análise)**: use essa opção para fazer com que os usuários examinem suas próprias atribuições de função.

### Iniciar a análise

Finalmente, você terá a opção de exigir que os usuários forneçam um motivo se eles aprovarem o acesso. Adicione uma descrição da análise se desejar e selecione **Iniciar**.

Certifique-se de permitir que os usuários saibam que há uma análise de acesso esperando por eles e mostre a eles [Como executar uma análise de acesso](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## Gerenciar a análise de acesso

Você pode acompanhar o progresso conforme os revisores concluem suas análises no painel do Azure AD PIM na seção de análises de acesso. Nenhum direito de acesso será alterado no diretório até a [análise ser concluída](active-directory-privileged-identity-management-how-to-complete-review.md).

Até o período de análise terminar, você pode lembrar os usuários de concluir sua análise ou interromper a análise antes da seção de análises de acesso.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sumário do PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png

<!---HONumber=AcomDC_0706_2016-->