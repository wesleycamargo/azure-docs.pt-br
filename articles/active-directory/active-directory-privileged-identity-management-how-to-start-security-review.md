<properties
   pageTitle="Como iniciar uma análise de segurança | Microsoft Azure"
   description="Saiba como criar uma revisão de segurança para identidades com privilégios com o aplicativo Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Como iniciar uma revisão de segurança no Azure AD Privileged Identity Management

As atribuições de função se tornam "obsoletas" quando os usuários têm acesso privilegiado de que não precisam mais. Para reduzir o risco associado a essas atribuições de função obsoletas, os administradores de função com privilégios devem revisar regularmente as funções que os usuários receberam. Este documento aborda as etapas para iniciar uma revisão de segurança no Azure AD PIM (Privileged Identity Management).

## Iniciar uma revisão de segurança:
> [AZURE.NOTE] Se você não adicionou o aplicativo PIM ao painel no portal do Azure, consulte as etapas em [Getting Started with Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) (Introdução ao Azure Privileged Identity Management)

Na página principal do aplicativo PIM, há três maneiras de iniciar uma revisão de segurança:

- **Revisões de segurança** > **Examinar** > botão **Examinar**
- **Funções** > botão **Examinar**
- Selecione a função a ser revisada na lista de funções > botão **Examinar**

Quando você clica no botão **Examinar**, as folhas **Iniciar revisão de uma função** e **Selecionar uma função a ser examinada** serão exibidas. O item **Selecionar uma função a ser examinada** selecionado para você.

### Selecionar a função a ser revisada

1. Selecione a função na lista de funções na folha **Selecionar uma função a ser examinada**. Você só pode escolher uma função por vez. A folha **Selecionar uma função para examinar** será substituída pela folha **Selecionar revisores**. Você tem duas opções ao selecionar revisores:
  - Eu: use essa opção se deseja visualizar como as revisões de segurança funcionam sem envolver outros administradores.
  - Autorrevisão por membros da função: use essa função para fazer os usuários revisarem suas próprias atribuições de função.
2. Selecione uma dessas opções para começar a trabalhar com os detalhes da análise. A folha **Alterar padrões** será exibida.

### Examinado por Mim

Se você selecionou a opção "Eu" como o revisor, prossiga para a análise de segurança. Para saber mais sobre como concluir a análise, consulte [Privileged Identity Management do Azure: como realizar uma análise de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### Autorrevisão por membros da função

Se você optar por fazer com que os usuários revisem suas próprias atribuições de função, siga estas etapas para configurar a revisão e enviar notificações.

1. Nomeie a análise inserindo o nome dela no campo **Nome**. Forneça à revisão um nome exclusivo que a descreva e que facilite seu acompanhamento.
2. Insira uma data de início para a análise no campo **Data de início**.
3. Insira uma data de término para a análise no campo **Data de término**. Algumas pontos que você deve considerar ao configurar a data de término da análise são:
  - Quantas pessoas estão sendo revisadas?
  - Com que rapidez os usuários poderão adicionar o aplicativo PIM ao portal do Azure e concluir a revisão?
4. Clique no botão **OK** na folha **Alterar padrões**. Ela será fechada.
5. Clique no botão **OK** na folha **Iniciar a revisão de uma função**. Ela será fechada. Uma notificação será exibida no menu principal do portal do Azure. Atualize o painel clicando no botão **Atualizar** e a revisão de segurança aparecerá na seção **Revisões de segurança**.
6. Notifique os indivíduos na função de que eles precisarão adicionar o aplicativo PIM e depois [revisar o próprio acesso administrativo](active-directory-privileged-identity-management-how-to-perform-security-review.md).  

## Gerenciar a revisão de segurança

Você pode acompanhar o progresso conforme os revisores concluem suas revisões no painel do Azure AD PIM na seção de revisões de segurança. Nenhum direito de acesso será alterado no diretório até a [revisão ser concluída](active-directory-privileged-identity-management-how-to-complete-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sumário do PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->