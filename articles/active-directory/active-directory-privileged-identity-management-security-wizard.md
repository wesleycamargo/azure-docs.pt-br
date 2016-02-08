<properties
   pageTitle="O Assistente de Segurança do Privileged Identity Management do Azure"
   description="Na primeira vez que você usar a extensão Privileged Identity Management do Azure, verá um assistente de segurança. Este artigo descreve as etapas para usar o assistente."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# O assistente de segurança do Privileged Identity Management do Azure

Na primeira vez que você executar o PIM (Privileged Identity Management), verá um assistente. O assistente ajuda você a entender os riscos à segurança de identidades com privilégios e a usar o Privileged Identity Management para reduzir esses riscos.

Há três seções a serem examinadas: **SEU ADMINISTRADOR PODE COLOCAR VOCÊ EM RISCO, GERENCIAR A SUPERFÍCIE DE ATAQUE DO ADMINISTRADOR** e **DEFINIR CONFIGURAÇÕES DO ADMINISTRADOR TEMPORÁRIO**. Cada seção apresenta uma visão geral dos conceitos e uma explicação de algumas ações a serem tomadas.

A princípio, todos os seus administradores globais serão permanentes. Ao clicar em **SEU ADMINISTRADOR PODE COLOCAR VOCÊ EM RISCO**, você verá uma lista de funções de administrador global e quantas delas você tem.

Clicar em **GERENCIAR A SUPERFÍCIE DE ATAQUE DO ADMINISTRADOR** apresentará a você uma oportunidade de alterar administradores para temporários, deixá-los como permanentes ou removê-los da função.

**DEFINIR CONFIGURAÇÕES DE ADMINISTRADOR TEMPORÁRIO** permite que você exija a autenticação multifator, habilite notificações e determine por quanto tempo um administrador temporário terá privilégios.

## Alterar funções de administrador global para temporário ou permanente

Você tem três opções para alterar a janela de tempo de um administrador global:

1.  Clique no botão **Tornar todos temporários** para transformar em temporários todos os administradores globais

2.  Clique no botão **Tornar todos permanentes** para transformar em permanentes todos os administradores globais

3.  Selecione **Manter Perm**, **Tornar Temp.** ou **Remover da Função** para cada administrador global.

## Altere o período de ativação da função de um administrador global.

Há duas maneiras de definir o período de ativação para um administrador global:

1.  Mova o controle deslizante do **período de ativação** para a esquerda ou para a direita para aumentar ou diminuir o período de ativação. O período de ativação pode ser de até 72 horas.

2.  Insira o número de horas no campo **horas** à direita do controle deslizante.

## Habilitar notificações

Para que os administradores possam receber email quando as funções se tornarem ativas, habilite as notificações clicando no botão **Habilitar**. Você também pode desabilitar esse recurso mais tarde.

## Exigir Multi-Factor Authentication

Se você quiser que os administradores sejam obrigados a usar a MFA para entrarem nas respectivas contas e a solicitar uma extensão da função, habilite a MFA clicando no botão **Habilitar**. Você também pode desabilitar esse recurso mais tarde.

<!--For more information about MFA and PIM, click here. PLACEHOLDER: NEED LINK TO MFA DOC.-->

Selecione as funções às quais essas configurações serão aplicadas. Clique em **OK**.

> [AZURE.WARNING] Neste momento, é importante que você tenha mais de um administrador de segurança. Se houver apenas um administrador de segurança que não esteja definido como permanente e que não tenha o MFA configurado, o usuário não será capaz de administrar o PIM após a expiração da atribuição da função.

Clique no botão **OK** quando tiver terminado.

Depois de ter feito alterações, o assistente não aparecerá mais. No entanto, você poderá acessá-lo novamente clicando no botão **Assistente**, em **Gerenciar identidades**.

## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->