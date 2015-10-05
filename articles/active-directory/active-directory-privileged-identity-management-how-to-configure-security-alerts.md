<properties
   pageTitle="Privileged Identity Management do Azure: Como Configurar Alertas de Segurança"
   description="Saiba como configurar alertas de segurança para a extensão Privileged Identity Management do Azure."
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
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Privileged Identity Management do Azure: Como Configurar Alertas de Segurança
## Visão Geral de Alertas de Segurança
O Privileged Identity Management do Azure oferece os alertas a seguir, que podem ser configurados. Os Alertas de Segurança podem ser exibidos na seção de Alertas do painel PIM.

| Alerta | Gatilho |
| ------------- | ------------- |
| **Suspeita de ataque de ativação permanente** | Um administrador ativou sua função temporária fora do PIM. |
| **Renovação da ativação suspeitas de funções privilegiadas** | Houve muitas reativações da mesma função com o tempo permitido nas configurações. |
| **Uso suspeito de usuário de Administrador global de token de mel** | O uso de um usuário "pote de mel" foi detectado.|
| **Autenticação fraca é configurada para ativação de função** | Há funções sem MFA nas configurações. |
| **Administradores redundantes aumentam sua superfície de ataque** | Há administradores temporários que não ativaram suas funções dentro do número de dias determinado nas configurações. |
| **Administradores globais em excesso aumentam a superfície de ataque** | Existem administradores mais globais do que permitido nas configurações. |

## Configurar Alertas de Segurança

### Configurar a "ativação suspeita de renovação de funções privilegiadas" alerta
1. Da seção **Atividade** do painel, selecione **Alertas de segurança**. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Defina o **Prazo de renovação da ativação** ajustando o controle deslizante ou digitando o número de minutos no campo de texto. O número máximo permitido é 100.
4. Defina o **Número de renovações da ativação** dentro do prazo de renovação da ativação ajustando o controle deslizante ou inserindo o número de renovações no campo de texto. O número máximo de renovações é 100.
5. Clique em **Salvar**.

### Configurar o Alerta "Administradores redundantes aumentam a superfície de ataque"
1. Da seção **Atividade** do painel, selecione **Alertas de segurança**. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Selecione o número de dias permitido sem a ativação da função ajustando o controle deslizante ou inserindo o número de dias no campo de texto.
4. Clique em **Salvar**

### Configure o Alerta "Administradores globais em excesso aumentam a superfície de ataque"

Esse alerta tem duas configurações que podem disparar o alerta. O número mínimo de Administradores Globais vai disparar o alerta se houver mais administradores do que o número permitido. Se a porcentagem de administradores globais na quantidade total de tipos de administradores é maior que o percentual nas configurações, o alerta também será acionado.

1. Da seção **Atividade** do painel, selecione **Alertas de segurança**. A folha **Alertas de segurança ativos** será exibida.
2. Clique em **Configurações**.
3. Defina o **Número mínimo de Administradores Globais** ajustando o controle deslizante ou digitando o número no campo de texto.
4. Defina o **Percentual, se Administradores Globais** ajustando o controle deslizante ou digitando o número no campo de texto.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->