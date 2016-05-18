<properties
   pageTitle="Como configurar alertas de segurança | Microsoft Azure"
   description="Saiba como configurar alertas de segurança para a extensão Privileged Identity Management do Azure."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Como configurar alertas de segurança no Azure AD Privileged Identity Management

## Alertas de segurança
O Azure PIM (Privileged Identity Management) gera os seguintes alertas, que podem ser exibidos na seção Alertas do painel PIM.

| Alerta | Gatilho | Recomendações |
| ----- | ------- | -------------- |
| **Ativação permanente** | Um administrador foi atribuído permanentemente a uma função, fora do PIM. | Revise a nova atribuição de função e altere-a para temporária, se necessário. |
| **Renovação da ativação suspeitas de funções privilegiadas** | Havia muitas reativações da mesma função durante o tempo permitido nas configurações. | Entre em contato com o usuário para assegurar que ele possa ativar a função com êxito. |
| **Autenticação fraca é configurada para ativação de função** | Há funções sem MFA nas configurações. | Considere exigir o MFA para a ativação de todas as funções. |
| **Administradores redundantes** | Existem administradores temporários que não ativaram suas funções recentemente. | Remova as atribuições de função que não são mais necessárias. |
| **Muitos administradores globais** | Existem mais administradores globais do que o recomendado. | Remova as atribuições de função que não são mais necessárias ou torne algumas delas temporárias. |

## Definir configurações de alerta de segurança

### Alerta “Renovação da ativação suspeita de funções com privilégios”

Defina as configurações **Período de tempo de renovação de ativação** e **Número de renovações de ativação** para controlar quando esse alerta for disparado.

1. Selecione **Alertas de segurança** na seção **Atividade** do painel. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Defina o **Prazo de renovação da ativação** ajustando o controle deslizante ou digitando o número de minutos no campo de texto. O máximo é 100.
4. Defina o **Número de renovações da ativação** dentro do prazo de renovação da ativação ajustando o controle deslizante ou inserindo o número de renovações no campo de texto. O máximo é 100.
5. Clique em **Salvar**.

### Alerta “Administradores redundantes”
1. Selecione **Alertas de segurança** na seção **Atividade** do painel. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Selecione o número de dias permitido sem a ativação da função ajustando o controle deslizante ou inserindo o número de dias no campo de texto.
4. Clique em **Salvar**.

### Alerta “Muitos administradores globais”

Há duas configurações que podem disparar esse alerta:
- O **Número mínimo de Administradores Globais** vai disparar o alerta se houver mais administradores do que o número permitido.
- O **Percentual de administradores globais** vai disparar o alerta se o percentual de administradores que são administradores globais for maior do que as configurações permitem.

1. Selecione **Alertas de segurança** na seção **Atividade** do painel. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Defina o **Número mínimo de Administradores Globais** ajustando o controle deslizante ou digitando o número no campo de texto.
4. Defina o **Percentual de Administradores Globais** ajustando o controle deslizante ou inserindo o percentual no campo de texto.
5. Clique em **Salvar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->