<properties
   pageTitle="Como concluir uma revisão de segurança | Microsoft Azure"
   description="Saiba como concluir uma revisão com o aplicativo Azure Privileged Identity Management."
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

# Como concluir uma revisão de segurança no Azure AD Privileged Identity Management


os administradores de função com privilégios podem revisar o acesso privilegiado após uma [revisão de segurança ter sido iniciada](active-directory-privileged-identity-management-how-to-start-security-review.md). O Azure AD PIM (Privileged Identity Management) automaticamente enviará um email solicitando que os usuários revisem seu acesso. Se um usuário não recebeu um email, você pode enviar para ele as instruções em [Como executar uma análise de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Depois do período de revisão de segurança, ou todos os usuários terminarem a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## Gerenciar revisões de segurança

1. Acesse o [portal do Azure](https://portal.azure.com/) e selecione o aplicativo **Azure AD Privileged Identity Management** no painel.
2. Clique na seção **Revisões de segurança** do painel. A folha **Análises de segurança** será exibida.
3. Selecione a revisão de segurança que você deseja gerenciar. A folha de detalhes da análise de segurança será exibida.

Quando estiver na folha de detalhes da revisão de segurança, há três opções para o gerenciamento da revisão. Você pode concluir a revisão, exportar os detalhes ou excluí-la.

### Concluir ou interromper uma revisão

Se os usuários tiverem o acesso negado, você poderá concluir a revisão para remover essas atribuições de função de usuário no diretório. O botão **Parar revisão** arquivará a revisão.

### Exportar uma revisão

Se você desejar aplicar os resultados da revisão de segurança manualmente, poderá exportar a revisão. O botão **Exportar** começará a baixar um arquivo CSV. Você pode gerenciar os resultados no Excel ou em outros programas que abrem arquivos CSV.

### Excluir uma revisão

> [AZURE.IMPORTANT] Você não receberá nenhum aviso antes da exclusão, portanto, certifique-se de que deseja excluir a revisão.

Se você não estiver mais interessado na revisão, exclua-a. O botão **Excluir** remove a revisão do aplicativo PIM.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->