<properties
   pageTitle="O Assistente de Segurança do Privileged Identity Management do Azure"
   description="Na primeira vez que usar a extensão do Privileged Identity Management, você verá um assistente de segurança. Este artigo descreve as etapas para usar o assistente."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2015"
   ms.author="inhenk"/>

# O Assistente de Segurança do Privileged Identity Management do Azure

Na primeira vez que executar o Privileged Identity Management, você verá um assistente. O assistente ajuda você a entender os riscos à segurança de identidades com privilégios e a usar o Privileged Identity Management para reduzir esses riscos.

Há três seções a serem analisadas, **SEU ADMINISTRADOR PODE COLOCAR VOCÊ EM RISCO, GERENCIAR A SUPERFÍCIE DE ATAQUE DO ADMINISTRADOR** e **DEFINIR CONFIGURAÇÕES DO ADMINISTRADOR TEMPORÁRIO**. Cada seção apresenta uma visão geral dos conceitos e uma explicação de algumas ações a serem tomadas.

A princípio, todos os seus administradores globais serão permanentes. Ao clicar em **SEU ADMINISTRADOR PODE COLOCAR VOCÊ EM RISCO**, você verá uma lista de funções de administrador global e quantas delas você tem.

Clicar em **GERENCIAR A SUPERFÍCIE DE ATAQUE DO ADMINISTRADOR** apresentará a você uma oportunidade de alterar administradores para temporários, deixá-los como permanentes ou removê-los da função.

**DEFINIR CONFIGURAÇÕES DE ADMINISTRADOR TEMPORÁRIO** permite determinar por quanto tempo um administrador temporário terá privilégios, habilitar notificações e exigir autenticação multifator.

## Alterar funções de administrador global para temporário ou permanente

Você tem três opções para alterar a janela de tempo de um administrador global.

1.  Clique no botão **Tornar todos temporários** para transformar em temporários todos os administradores globais

2.  Clique no botão **Tornar todos permanentes** para transformar em permanentes todos os administradores globais

3.  Selecione **Manter Perm**, **Tornar Temp.** ou **Remover da Função** para cada administrador global.

4.  Clique em **OK**.

5.  Clique em **Enviar**.

## Altere o período de ativação da função de um administrador global.

1.  Mova o controle deslizante do **período de ativação** para a esquerda ou para a direita para aumentar ou diminuir o período de ativação. O período de ativação pode ser de até 72 horas.

2.  Insira o número de horas no campo **horas** à direita do controle deslizante.

## Habilitar notificações

Para que os administradores possam receber email quando as funções se tornarem ativas, habilite as notificações clicando no botão **Habilitar**. Você também pode desabilitar esse recurso.

## Exigir Multi-Factor Authentication

Se deseja que os administradores sejam obrigados a usar MFA para fazer logon nas respectivas contas e a solicitar uma extensão de sua função, habilite a MFA clicando no botão **Habilitar**. Você também pode desabilitar esse recurso.

Para saber mais sobre MFA e PIM, clique aqui. ESPAÇO RESERVADO: PRECISA DE LINK PARA DOC MFA.

Selecione as funções às quais essas configurações serão aplicadas. Clique em **OK**.

> [AZURE.WARNING]É importante, neste momento, que você tenha mais de um administrador de segurança, pois se um deles não estiver definido como permanente e a atribuição de função expirar, e você não tiver uma MFA configurada para esse usuário, este não poderá administrar o PIM.

Clique no botão **OK**. Quando tiver terminado.

Depois de ter feito alterações, o assistente não aparecerá mais. No entanto, você pode acessá-lo novamente clicando no botão Assistente em Gerenciar identidades.

## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->