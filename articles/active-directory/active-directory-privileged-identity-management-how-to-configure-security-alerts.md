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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Privileged Identity Management do Azure AD: Como configurar alertas de segurança

## Visão geral de alertas de segurança
O PIM (Privileged Identity Management) oferece os seguintes alertas que podem ser configurados. Os alertas de segurança podem ser exibidos na seção Alertas do painel PIM.

| Alerta | Gatilho |
| ------------- | ------------- |
| **Suspeita de ataque de ativação permanente** | Um administrador ativou sua função temporária fora do PIM. |
| **Renovação da ativação suspeitas de funções privilegiadas** | Havia muitas reativações da mesma função durante o tempo permitido nas configurações. |
| **Uso suspeito de usuário de Administrador global de token de mel** | O uso de um usuário "pote de mel" foi detectado.|
| **Autenticação fraca é configurada para ativação de função** | Há funções sem MFA nas configurações. |
| **Administradores redundantes aumentam sua superfície de ataque** | Há administradores temporários que não ativaram suas funções dentro do número de dias determinado nas configurações. |
| **Administradores globais em excesso aumentam a superfície de ataque** | Existem administradores mais globais do que permitido nas configurações. |

## Configurar Alertas de Segurança

### Configurar o alerta "Ativação suspeita de renovação de funções privilegiadas"
1. Da seção **Atividade** do painel, selecione **Alertas de segurança**. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Defina o **Prazo de renovação da ativação** ajustando o controle deslizante ou digitando o número de minutos no campo de texto. O número máximo permitido é 100.
4. Defina o **Número de renovações da ativação** dentro do prazo de renovação da ativação ajustando o controle deslizante ou inserindo o número de renovações no campo de texto. O número máximo de renovações é 100.
5. Clique em **Salvar**.

### Configurar o alerta "Administradores redundantes aumentam sua superfície de ataque"
1. Da seção **Atividade** do painel, selecione **Alertas de segurança**. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Selecione o número de dias permitido sem a ativação da função ajustando o controle deslizante ou inserindo o número de dias no campo de texto.
4. Clique em **Salvar**.

### Configurar o alerta "Administradores globais em excesso aumentam sua superfície de ataque"

Esse alerta tem duas configurações que podem disparar o alerta. O número mínimo de Administradores Globais vai disparar o alerta se houver mais administradores do que o número permitido. Se a porcentagem de administradores globais na quantidade total de tipos de administradores é maior que o percentual nas configurações, o alerta também será acionado.

1. Da seção **Atividade** do painel, selecione **Alertas de segurança**. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Defina o **Número mínimo de Administradores Globais** ajustando o controle deslizante ou digitando o número no campo de texto.
4. Defina o **Percentual de Administradores Globais** ao ajustar o controle deslizante ou ao inserir o número no campo de texto.
5. Clique em **Salvar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->