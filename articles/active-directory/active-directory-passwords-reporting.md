---
title: "Relatórios de redefinição de senha por autoatendimento – Azure Active Directory"
description: "Relatórios de eventos de autoatendimento de redefinição de senha do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: ae9475218141f4fdf08ef6b39fb85f3b18711e61
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opções de relatórios para o gerenciamento de senhas do Azure AD

Após a implantação, muitas organizações desejam saber como ou se a SSPR (redefinição de senha de autoatendimento) realmente está sendo usada. O recurso de relatório do Azure Active Directory (Azure AD) o ajuda a responder perguntas usando relatórios predefinidos. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas.

![Relatórios][Reporting]

As perguntas a seguir podem ser respondidas pelos relatórios existentes no [portal do Azure] (https://portal.azure.com/):

> [!NOTE]
> Você deve ser [administrador global](active-directory-assign-admin-roles-azure-portal.md) e aceitar a coleta desses dados em nome de sua organização. Para aceitar, você deve visitar a guia **Relatórios** ou os logs de auditoria pelo menos uma vez. Até lá, os dados não serão coletados para a sua organização.
>

* Quantas pessoas foram registradas para a redefinição de senhas?
* Quem se registrou para a redefinição de senhas?
* Que dados as pessoas estão registrando?
* Quantas pessoas redefiniram suas senhas nos últimos sete dias?
* Quais são os métodos mais comuns que os usuários ou administradores usam para redefinir suas senhas?
* Quais são os problemas comuns que os usuários ou administradores enfrentam ao tentar usar a redefinição de senhas?
* Quais administradores estão redefinindo suas próprias senhas com frequência?
* Há qualquer atividade suspeita acontecendo na redefinição de senhas?

## <a name="power-bi-content-pack"></a>Pacotes de conteúdo do Power BI

Se você é um usuário do Power BI, há um pacote de conteúdo do Azure AD que inclui relatórios fáceis de usar para SSPR. Encontre mais informações sobre como usar e implantar o pacote de conteúdo no artigo [Como usar o pacote de conteúdo do Power BI do Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md). Com o pacote de conteúdo, você pode criar seus próprios painéis e compartilhá-los com outras pessoas em sua organização.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Como exibir relatórios de gerenciamento de senhas no portal do Azure

Na experiência do portal do Azure, melhoramos a maneira de exibir a redefinição de senha e a atividade de registro de redefinição de senha. Use as etapas abaixo para encontrar os eventos de redefinição de senha e registro de redefinição de senha:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no painel esquerdo.
3. Procure **Azure Active Directory** na lista de serviços e selecione-o.
4. Selecione **Usuários e grupos**.
5. Selecione **Logs de auditoria** no menu **Usuários e grupos**. Isso mostra todos os eventos de auditoria que ocorreram em relação a todos os usuários no diretório. Você pode filtrar essa exibição para ver todos os eventos relacionados a senhas.
6. Para filtrar essa exibição somente para os eventos relacionados à redefinição de senha, selecione o botão **Filtrar** na parte superior do painel.
7. No menu **Filtro**, selecione a lista suspensa **Categoria** e altere-a para o tipo de categoria **Gerenciamento de Senha de Autoatendimento**.
8. Opcionalmente, filtre mais a lista escolhendo a **Atividade** específica em que você está interessado.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Como recuperar eventos de gerenciamento de senhas da API de Relatórios e Eventos do Azure AD

A API de Relatórios e Eventos do Azure AD é compatível com a recuperação de todas as informações incluídas nos relatórios de redefinição de senha e registro de redefinição de senha. Com essa API, você pode baixar eventos individuais de redefinição de senha e registro de redefinição de senha e integrá-los à tecnologia de relatórios de sua escolha.

> [!IMPORTANT]
> Atualmente, os Relatórios do Azure AD e a API de Eventos recuperam até *75.000 eventos individuais* dos tipos [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent). A API abrange os últimos 30 dias.
> 
> Se precisa recuperar ou armazenar dados além desta janela, sugerimos mantê-los em um banco de dados externo usando a API para consultar os deltas resultantes. É recomendável que você comece a recuperar esses dados quando começar a usar SSPR em sua organização. Mantenha-os externamente e, em seguida, continue a rastrear os deltas desse ponto em diante.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>Como começar a usar a API de relatórios

Para acessar esses dados, você precisa gravar um aplicativo ou script pequeno para recuperá-los de nossos servidores. Para obter mais informações, confira [Introdução à API de relatórios do Azure AD](active-directory-reporting-api-getting-started.md).

Quando você tiver um script de trabalho, desejará examinar os eventos de registro e redefinição de senhas que podem ser recuperados para atender suas situações:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): lista as colunas disponíveis para os eventos de redefinição de senhas.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): lista as colunas disponíveis para os eventos do registro de redefinição de senhas.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrição das colunas do relatório no portal do Azure

A lista a seguir explica cada uma das colunas do relatório no portal do Azure em detalhes:

* **Usuário**: o usuário que tentou uma operação de registro de redefinição de senha.
* **Função** – a função do usuário no diretório.
* **Data e hora** – a data e a hora da tentativa.
* **Dados Registrados**: os dados de autenticação fornecidos pelo usuário durante o registro de redefinição de senha.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrição dos valores do relatório no portal do Azure

A tabela a seguir descreve os diferentes valores que podem ser definidos para cada coluna no portal do Azure:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Dados Registrados |**Email Alternativo**: email alternativo ou email de autenticação usado pelo usuário para se autenticar.<p><p>**Telefone Comercial**: telefone comercial usado pelo usuário para se autenticar.<p>**Celular**: celular ou telefone de autenticação usado pelo usuário para se autenticar.<p>**Perguntas de Segurança**: perguntas de segurança usadas pelo usuário para se autenticar.<p>**Qualquer combinação dos métodos acima, por exemplo, email alternativo + celular**: ocorre quando uma política de duas portas é especificada e mostra quais dos dois métodos o usuário utilizou para autenticar sua solicitação de redefinição de senha. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividades de Gerenciamento de Senha de autoatendimento

Os tipos de atividade a seguir aparecem na categoria de evento de auditoria **Gerenciamento de Senhas de Autoatendimento**:

* [Impedido de executar redefinição de senha de autoatendimento](#activity-type-blocked-from-self-service-password-reset): indica que um usuário tentou redefinir uma senha, usar uma porta específica ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* [Alterar senha (autoatendimento)](#activity-type-change-password-self-service): indica que um usuário executou uma alteração de senha voluntária ou forçada (devido à expiração).
* [Redefinição de senha (pelo administrador)](#activity-type-reset-password-by-admin): indica que um administrador executou uma redefinição em nome do usuário no Portal do Azure.
* [Redefinição de senha (autoatendimento)](#activity-type-reset-password-self-service): indica que um usuário redefiniu sua senha com êxito no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com).
* [Progresso da atividade do fluxo de autoatendimento de redefinição de senha](#activity-type-self-serve-password-reset-flow-activity-progress): indica cada etapa específica que um usuário realiza (como a passagem em uma porta específica de autenticação de redefinição de senha) como parte do processo de redefinição de senha.
* [Desbloquear conta de usuário (autoatendimento)](#activity-type-unlock-user-account-self-service): indica que um usuário desbloqueou sua conta do Active Directory com êxito sem redefinir a senha no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso de desbloqueio de conta do AD sem redefinição.
* [Usuário registrado para autoatendimento de redefinição de senha](#activity-type-user-registered-for-self-service-password-reset): indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: redefinição de senha de autoatendimento bloqueada

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário tentou redefinir uma senha, usar uma porta específica ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* **Ator de Atividade**: o usuário que foi limitado ao executar operações de redefinição adicionais. O usuário pode ser um usuário final ou um administrador.
* **Destino de Atividade**: o usuário que foi limitado de executar operações de redefinição adicionais. O usuário pode ser um usuário final ou um administrador.
* **Status da atividade**:
  * _Sucesso_: indica que um usuário foi restringido ao executar qualquer redefinição adicional, tentar qualquer método de autenticação adicional ou validar quaisquer números de telefone adicionais nas próximas 24 horas.
* **Razão da falha do status de atividade**: não aplicável.

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: alterar senha (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário executou uma alteração de senha voluntária ou forçada (devido à expiração).
* **Ator de atividade**: o usuário que alterou sua senha. O usuário pode ser um usuário final ou um administrador.
* **Destino de atividade**: o usuário que alterou sua senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade**:
  * _Sucesso_: indica que um usuário alterou sua senha com êxito.
  * _Falha_: indica que um usuário não pôde alterar sua senha. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.
* **Motivo da falha do status de atividade**: 
  * _FuzzyPolicyViolationInvalidPassword_: o usuário selecionou uma senha que foi excluída automaticamente porque os recursos de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: redefinir senha (pelo administrador)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um administrador executou uma redefinição em nome de um usuário no Portal do Azure.
* **Ator de atividade**: o administrador que realizou a redefinição da senha em nome de outro usuário final ou administrador. Deve ser o administrador global, o administrador de senha, o administrador de usuário ou o administrador de assistência técnica.
* **Destino de atividade**: o usuário cuja senha foi redefinida. O usuário pode ser um usuário final ou um administrador diferente.
* **Status de atividade**:
  * _Sucesso_: indica que um administrador redefiniu a senha de um usuário com êxito.
  * _Falha_: indica que um administrador falhou ao alterar a senha do usuário. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: redefinir senha (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário redefiniu sua senha com êxito no [portal de redefinição de senha do Azure AD](https://passwordreset.microsoftonline.com).
* **Ator de atividade**: o usuário que redefiniu sua senha. O usuário pode ser um usuário final ou um administrador.
* **Destino de atividade**: o usuário que redefiniu sua senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade**:
  * _Sucesso_: indica que um usuário redefiniu sua própria senha com êxito.
  * _Falha_: indica que um usuário não conseguiu redefinir sua própria senha. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.
* **Motivo da falha do status de atividade**: 
  * _FuzzyPolicyViolationInvalidPassword_: o administrador selecionou uma senha que foi excluída automaticamente porque os recursos de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: progresso de atividade de fluxo de redefinição de senha de autoatendimento

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: indica cada etapa específica que um usuário executa (como uma porta de autenticação de redefinição de senha específica) como parte do processo de redefinição de senha.
* **Ator de atividade**: o usuário que realizou a parte do fluxo de redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Destino de atividade**: o usuário que realizou a parte do fluxo de redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade**:
  * _Sucesso_: indica que um usuário concluiu com êxito uma etapa específica do fluxo de redefinição de senha.
  * _Falha_: indica que uma etapa específica do fluxo de redefinição de senha falhou. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.
* **Motivos de status de atividade**: consulte a tabela a seguir para [todos os motivos de status de atividade redefinição permitidos](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de atividade: desbloquear uma conta de usuário (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário desbloqueou sua conta do Active Directory com êxito sem redefinir a senha no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso de desbloqueio de conta do AD sem redefinição.
* **Ator de atividade**: o usuário que desbloqueou sua conta sem redefinir a senha. O usuário pode ser um usuário final ou um administrador.
* **Destino de atividade**: o usuário que desbloqueou sua conta sem redefinir a senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade permitidos**:
  * _Sucesso_: indica que um usuário desbloqueou sua própria conta com êxito.
  * _Falha_: indica que um usuário não conseguiu desbloquear sua conta. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: usuário registrado para redefinição de senha de autoatendimento

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento. 
* **Ator de atividade**: o usuário que se registrou para a redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Destino de atividade**: o usuário que se registrou para a redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade permitidos**:
  * _Sucesso_: indica que um usuário se registrou com êxito para a redefinição de senha de acordo com a política atual. 
  * _Falha_: indica que um usuário falhou ao se registrar para a redefinição de senha. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha. 

     >[!NOTE]
     >Falha não significa que um usuário não pode redefinir sua senha. Isso significa que ele não concluiu o processo de registro. Se houver dados não verificados na conta que estejam corretos (como um número de telefone que não foi validado), mesmo que eles não tenham verificado esse número de telefone, ainda poderão usá-lo para redefinir a senha. Para obter mais informações, confira [O que acontece quando um usuário é registrado?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Exemplo de logs de auditoria da atividade de SSPR no Azure AD"
