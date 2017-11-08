---
title: "Relatórios: SSPR do Azure AD | Microsoft Docs"
description: "Relatórios de eventos de autoatendimento de redefinição de senha do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5b28e15d643497dbdf827b3976ad7dcdc73507b1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opções de relatórios para o gerenciamento de senhas do Azure AD

Após a implantação, muitas organizações desejam saber como ou se o SSPR realmente está sendo usado. O Azure AD fornece recursos de relatórios que ajudam você a responder perguntas usando relatórios definidos e, caso você esteja licenciado de forma adequada, permitem que você crie consultas personalizadas.

![Relatórios][Reporting]

As perguntas a seguir podem ser respondidas pelos relatórios existentes no [portal do Azure] (https://portal.azure.com/).

> [!NOTE]
> Você deve ser [administrador global](active-directory-assign-admin-roles.md#assign-or-remove-administrator-roles) e aceitar a coleta desses dados em nome de sua organização acessando a guia de relatórios ou os logs de auditoria pelo menos uma vez. Os dados não serão coletados para sua organização antes de você fazer isso

* Quantas pessoas foram registradas para a redefinição de senhas?
* Quem se registrou para a redefinição de senhas?
* Que dados as pessoas estão registrando?
* Quantas pessoas redefiniram suas senhas nos últimos sete dias?
* Quais são os métodos mais comuns que os usuários ou administradores usam para redefinir suas senhas?
* Quais são os problemas comuns que os usuários ou administradores enfrentam ao tentar usar a redefinição de senhas?
* Quais administradores estão redefinindo suas próprias senhas com frequência?
* Há qualquer atividade suspeita acontecendo na redefinição de senhas?

## <a name="power-bi-content-pack"></a>Pacotes de conteúdo do Power BI

Se você for um usuário do Power BI há um pacote de conteúdo do Azure AD que inclui relatórios fáceis de usar para SSPR. Encontre mais informações sobre como usar e implantar o pacote de conteúdo no artigo [Como usar o pacote de conteúdo do Power BI do Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md). Em seguida, você pode criar seus próprios painéis e compartilhá-los com outras pessoas em sua organização.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Como exibir relatórios de gerenciamento de senhas no portal do Azure

Na experiência do portal do Azure, temos uma maneira melhor de exibir a redefinição de senha e a atividade de registro de redefinição de senha.  Siga as etapas abaixo para encontrar os eventos de redefinição de senha e registro de redefinição de senha:

1. Navegue até [**portal.azure.com**](https://portal.azure.com)
2. Clique no menu **Mais serviços** na barra de navegação à esquerda principal do portal do Azure
3. Procure **Azure Active Directory** na lista de serviços e selecione-o
4. Clique em **Usuários e Grupos** no menu de navegação do Azure Active Directory
5. Clique no item de navegação **Logs de Auditoria** no menu de navegação Usuários e Grupos. Isso mostra todos os eventos de auditoria que ocorrem em relação a todos os usuários no diretório. Você pode filtrar essa exibição para ver também todos os eventos relacionados a senhas.
6. Para filtrar essa exibição somente para os eventos relacionados à redefinição de senha, clique no botão **Filtrar** na parte superior da folha.
7. No menu **Filtro**, selecione a lista suspensa **Categoria** e altere-a para o tipo de categoria **Gerenciamento de Senha de Autoatendimento**.
8. Opcionalmente, filtre mais a lista escolhendo a **Atividade** específica em que você está interessado

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Como recuperar eventos de gerenciamento de senhas da API de Relatórios e Eventos do Azure AD

A API de Relatórios e Eventos do Azure AD dá suporte à recuperação de todas as informações incluídas nos relatórios de redefinição de senha e registro de redefinição de senha. Com essa API, você pode baixar eventos individuais de redefinição de senha e registro de redefinição de senha para a integração à tecnologia de relatórios de sua escolha.

### <a name="how-to-get-started-with-the-reporting-api"></a>Como começar a usar a API de relatórios

Para acessar esses dados, você precisa escrever um aplicativo ou script pequeno para recuperá-los de nossos servidores. [Saiba como começar com a API de Relatório do AD do Azure](active-directory-reporting-api-getting-started.md).

Quando você tiver um script de trabalho, em seguida desejará examinar os eventos de registro e redefinição de senhas que podem ser recuperados para atender suas situações.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): lista as colunas disponíveis para os eventos de redefinição de senhas
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): lista as colunas disponíveis para os eventos do registro de redefinição de senhas

### <a name="reporting-api-data-retrieval-limitations"></a>Relatando limitações de recuperação de dados de API

Atualmente, os Relatórios do Azure AD e a API de Eventos recuperam até **75.000 eventos individuais** dos tipos [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent), abrangendo os **últimos 30 dias**.

Se precisar recuperar ou armazenar dados além desta janela, sugerimos persisti-los em um banco de dados externo e usar a API para consultar os deltas resultantes. Nossa recomendação é começar a recuperar esses dados quando iniciar o uso do SSPR em sua organização, persisti-los externamente e continuar acompanhando os deltas desse ponto em diante.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Como baixar eventos de registro de redefinição de senha rapidamente com o PowerShell

Além de usar os Relatórios do Azure AD e a API de Eventos diretamente, você também pode usar o script do PowerShell abaixo para eventos de registro recentes no diretório. Isso é útil caso você deseje ver quem foi registrado recentemente ou garantir que a distribuição de redefinição de senha ocorra conforme o esperado.

* [Script do PowerShell de Atividade de Registro do Azure AD SSPR](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

### <a name="description-of-report-columns-in-azure-portal"></a>Descrição das colunas do relatório no portal do Azure

A lista a seguir explica cada uma das colunas do relatório em detalhes:

* **Usuário** – o usuário que tentou uma operação de registro de redefinição de senha.
* **Função** – a função do usuário no diretório.
* **Data e hora** – a data e a hora da tentativa.
* **Dados Registrados** – os dados de autenticação fornecidos pelo usuário durante o registro de redefinição de senha.

### <a name="description-of-report-values-in-azure-portal"></a>Descrição dos valores do relatório no portal do Azure

A tabela a seguir descreve os diferentes valores permitidos para cada coluna:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Dados Registrados |**Email Alternativo** – email alternativo ou email de autenticação usado pelo usuário para se autenticar<p><p>**Telefone Comercial**– telefone comercial usado pelo usuário para se autenticar<p>**Celular** - celular ou telefone de autenticação usado pelo usuário para se autenticar<p>**Perguntas de Segurança** – perguntas de segurança usadas pelo usuário para se autenticar<p>**Qualquer combinação dos itens acima (por exemplo, Email Alternativo + Telefone Celular)** – ocorre quando uma política de 2 portas é especificada e mostra quais dos dois métodos o usuário utilizou para autenticar sua solicitação de redefinição de senha. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividades de Gerenciamento de Senha de autoatendimento

Os tipos de atividade a seguir aparecem na categoria de evento de auditoria **Gerenciamento de Senhas de Autoatendimento**.  A seguir há uma descrição de cada um deles.

* [**Impedido de executar redefinição de senha de autoatendimento**](#activity-type-blocked-from-self-service-password-reset) – indica que um usuário tentou redefinir uma senha, usar uma porta específica ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* [**Alterar senha (autoatendimento)**](#activity-type-change-password-self-service) – indica que um usuário executou uma alteração de senha voluntária ou forçada (devido à expiração).
* [**Redefinição de senha (pelo administrador)**](#activity-type-reset-password-by-admin) – indica que um administrador executou uma redefinição de senha em nome de um usuário no portal do Azure.
* [**Redefinição de senha (autoatendimento)**](#activity-type-reset-password-self-service) – indica que um usuário redefiniu sua senha com êxito no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com).
* [**Progresso da atividade do fluxo de autoatendimento de redefinição de senha**](#activity-type-self-serve-password-reset-flow-activity-progress) – indica cada etapa específica que um usuário realiza (como a passagem em uma porta específica de autenticação de redefinição de senha) como parte do processo de redefinição de senha.
* [**Desbloquear conta de usuário (autoatendimento)**](#activity-type-unlock-user-account-self-service) – indica que um usuário desbloqueou sua conta do Active Directory com êxito sem redefinir a senha no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso de desbloqueio de conta do AD sem redefinição.
* [**Usuário registrado para autoatendimento de redefinição de senha**](#activity-type-user-registered-for-self-service-password-reset) – indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: redefinição de senha de autoatendimento bloqueada

A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário tentou redefinir uma senha, usar uma porta específica ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* **Ator de Atividade** - o usuário que foi limitado ao executar operações de redefinição adicionais. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que foi limitado ao executar operações de redefinição adicionais. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
  * _Sucesso_ - indica que um usuário foi restringido ao executar qualquer redefinição adicional, tentar qualquer método de autenticação adicional ou validar quaisquer números de telefone adicionais nas próximas 24 horas.
* **Razão da Falha do Status de Atividade** - não aplicável

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: alterar senha (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário executou uma alteração de senha voluntária ou forçada (devido à expiração).
* **Ator de Atividade** – o usuário que alterou sua senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** – o usuário que alterou sua senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
  * _Sucesso_ – indica que um usuário alterou sua senha com êxito
  * _Falha_ – indica que um usuário não pôde alterar sua senha. Clicar na linha permite ver a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.
* **Razão da Falha do Status de Atividade** - 
  * _FuzzyPolicyViolationInvalidPassword_ – o usuário selecionou uma senha que foi banida automaticamente porque as funcionalidades de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: redefinir senha (pelo administrador)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um administrador executou uma redefinição de senha em nome de um usuário no portal do Azure.
* **Ator de Atividade** – o administrador que realizou a redefinição de senha em nome de outro usuário final ou administrador. Deve ser o administrador global, o administrador de senha, o administrador de usuário ou o administrador de assistência técnica.
* **Destino de Atividade** - o usuário cuja senha foi redefinida. Pode ser um usuário final ou um administrador diferente.
* **Status de Atividade Permitida**
  * _Sucesso_ - indica que um administrador redefine a senha de um usuário com êxito
  * _Falha_ - indica que um administrador falhou ao alterar a senha do usuário. Clicar na linha permite ver a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: redefinir senha (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário redefiniu sua senha com êxito no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com).
* **Ator de Atividade** – o usuário que redefiniu sua senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** – o usuário que redefiniu sua senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
  * _Sucesso_ – indica que um usuário redefiniu sua própria senha com êxito
  * _Falha_ – indica que um usuário não conseguiu redefinir sua própria senha. Clicar na linha permite ver a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.
* **Razão da Falha do Status de Atividade** -
  * _FuzzyPolicyViolationInvalidPassword_ – o administrador selecionou uma senha que foi banida automaticamente porque as funcionalidades de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: progresso de atividade de fluxo de redefinição de senha de autoatendimento

A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica cada etapa específica que um usuário executa (como uma porta de autenticação de redefinição de senha específica) como parte do processo de redefinição de senha.
* **Ator de Atividade** - o usuário que realizou a parte do fluxo de redefinição de senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que realizou a parte do fluxo de redefinição de senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
  * _Sucesso_ - indica que um usuário concluiu com êxito uma etapa específica do fluxo de redefinição de senha.
  * _Falha_ - indica que uma etapa específica do fluxo de redefinição de senha falhou. Clicar na linha permite ver a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.
* **Razões de Status de Atividade Permitida**
  * Confira a tabela abaixo para ver [todas as razões de status de atividade de redefinição permitida](#allowed-values-for-details-column)

### <a name="activity-type-unlock-user-account-self-service"></a>Tipo de atividade: desbloquear conta de usuário (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário desbloqueou sua conta do Active Directory com êxito sem redefinir a senha no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso de desbloqueio de conta do AD sem redefinição.
* **Ator de Atividade** – o usuário que desbloqueou sua conta sem redefinir a senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** – o usuário que desbloqueou sua conta sem redefinir a senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
  * _Sucesso_ – indica que um usuário desbloqueou sua própria conta com êxito
  * _Falha_ – indica que um usuário não conseguiu desbloquear sua conta. Clicar na linha permite ver a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: usuário registrado para redefinição de senha de autoatendimento

A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento. 
* **Ator de Atividade** - o usuário que se registrou para a redefinição de senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que se registrou para a redefinição de senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
  * _Sucesso_ – indica que um usuário se registrou com êxito para a redefinição de senha de acordo com a política atual. 
  * _Falha_ - indica que um usuário falhou ao se registrar para a redefinição de senha. Clicar na linha permite ver a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha. Observação – isso não significa que um usuário não possa redefinir sua própria senha, apenas que ele não concluiu o processo de registro. Se houver dados não verificados na conta que estejam corretos (como um número de telefone que não foi validado), mesmo que eles não tenham verificado esse número de telefone, ainda poderão usá-lo para redefinir a senha. Para obter mais informações, confira [O que acontece quando um usuário é registrado?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

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
