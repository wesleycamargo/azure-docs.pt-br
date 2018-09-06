---
title: Configurar alertas de segurança para funções de diretório do Azure AD no PIM | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções do diretório do Azure AD no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4e1cb47989011f179c54061bd29ae55b4ff86d80
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669404"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Configurar alertas de segurança para funções do diretório do Azure AD no PIM
## <a name="security-alerts"></a>Alertas de segurança
O Azure PIM (Privileged Identity Management) gera alertas quando há atividade suspeita ou não segura em seu ambiente. Quando um alerta é disparado, ele aparece no painel PIM. Selecione o alerta para ver um relatório que lista os usuários ou as funções que dispararam o alerta.

![Alertas de segurança do painel PIM – captura de tela](./media/pim-how-to-configure-security-alerts/PIM_security_dash.png)

| Alerta | Severity | Gatilho | Recomendações |
| --- | --- | --- | --- |
| **As funções estão sendo atribuídas fora do PIM** |Alto |Um usuário recebeu permanentemente uma função com privilégios, fora da interface do PIM. |Examine os usuários na lista e cancele a atribuição das funções com privilégios atribuídas fora do PIM. |
| **As funções estão sendo ativadas com muita frequência** |Média |Havia muitas reativações da mesma função durante o tempo permitido nas configurações. |Entre em contato com o usuário para ver por que ele ativou a função tantas vezes. Talvez o limite de tempo seja muito curto para que ele conclua suas tarefas ou talvez ele esteja utilizando scripts para ativar uma função automaticamente. Verifique se a duração da ativação da função é suficiente para a execução das tarefas. |
| **Roles don't require multi-factor authentication for activation (As funções não exigem o multi-factor authentication para ativação)** |Média |Há funções sem o MFA habilitado nas configurações. |Exigimos o MFA para as funções mais altamente privilegiadas, mas recomendamos que você habilite o MFA para a ativação de todas as funções. |
| **Usuários não estão utilizando suas funções privilegiadas** |Baixo |Existem administradores qualificados que não ativaram suas funções recentemente. |Inicie uma análise de acesso para determinar os usuários que não precisam mais de acesso. |
| **Há muitos administradores globais** |Baixo |Existem mais administradores globais do que o recomendado. |Caso você tenha um grande número de administradores globais, é provável que os usuários estejam obtendo mais permissões do que eles precisam. Mova os usuários para funções menos privilegiadas ou torne alguns deles qualificados para a função em vez de atribuídos permanentemente. |

### <a name="severity"></a>Severity
* **Alta**: exige ação imediata devido a uma violação da política. 
* **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
* **Baixa**: não exige ação imediata, mas sugere uma alteração preferencial da política.

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança
Você pode personalizar os alertas de segurança no PIM para trabalhar com seu ambiente e objetivos de segurança. Siga estas etapas para acessar a folha de configurações:

1. Entre no [Portal do Azure](https://portal.azure.com/) e selecione bloco **Azure AD Privileged Identity Management** no painel.
2. Selecione **Managed privileged roles (Funções com privilégios gerenciadas)** > **Configurações** > **Configurações de alertas**.
   
    ![Navegar até as configurações de alertas de segurança](./media/pim-how-to-configure-security-alerts/PIM_security_settings.png)

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerta “As funções estão sendo ativadas com muita frequência”
Esse alerta será disparado se um usuário ativar a mesma função com privilégios várias vezes dentro de um período especificado. Você pode configurar o período e o número de ativações.

* **Período de tempo de renovação de ativação**: especifique em dias, horas, minutos e segundos o período que você deseja usar para rastrear renovações suspeitas.
* **Number of activation renewals (Número de renovações de ativação)**: especifique o número de ativações, de 2 a 100, que você considera digno de alerta, dentro do período escolhido. Você pode mudar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto.

### <a name="there-are-too-many-global-administrators-alert"></a>Alerta “Há muitos administradores globais”
O PIM disparará esse alerta se dois critérios diferentes forem atendidos, e você poderá configurar ambos. Primeiro, você precisa atingir certo limite de administradores globais. Em segundo lugar, um determinado percentual do seu total de atribuições de função deve ser de administradores globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido.  

* **Número mínimo de Administradores Globais**: especifique o número de administradores globais, de 2 a 100, que você considera uma quantidade não segura.
* **Percentual de administradores globais**: especifique o percentual de administradores que são administradores globais, de 0 a 100%, que não é seguro no seu ambiente.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Alerta “Administrators aren't using their privileged roles” (Os administradores não estão utilizando suas funções privilegiadas)
Esse alerta será disparado se um usuário passar um determinado período sem ativar uma função.

* **Número de dias**: especifique o número de dias, de 0 a 100, que um usuário pode passar sem ativar uma função.

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função do diretório do Azure AD no PIM](pim-how-to-change-default-settings.md)
