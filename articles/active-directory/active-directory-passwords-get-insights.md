---
title: "Obter percepções: relatórios de gerenciamento de senhas do Azure AD | Microsoft Docs"
description: "Este artigo descreve como usar os relatórios para obter informações sobre operações de gerenciamento de senhas em sua organização."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 1472b51d-53f4-4b0f-b1be-57f6fa88fa65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: ff26d568624a712cf86db962193c8f631065c004
ms.lasthandoff: 04/13/2017


---
# <a name="how-to-get-operational-insights-with-password-management-reports"></a>Como obter percepções operacionais com relatórios de gerenciamento de senhas
> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-my-password).
>
>

Esta seção descreve como você pode usar relatórios de gerenciamento de senhas do Azure Active Directory para ver como os usuários estão usando a redefinição e alteração de senhas em sua organização.

* [**Visão geral de relatórios de gerenciamento de senhas**](#overview-of-password-management-reports)
* [**Como exibir relatórios de gerenciamento de senhas no novo portal do Azure**](#how-to-view-password-management-reports)
 * [Funções de diretório que podem ler relatórios](#directory-roles-allowed-to-read-reports)
* [**Tipos de atividades de Gerenciamento de senhas de autoatendimento no novo Portal do Azure**](#self-service-password-management-activity-types)
 * [Impedido de executar a redefinição de senha de autoatendimento](#activity-type-blocked-from-self-service-password-reset)
 * [Alterar senha (autoatendimento)](#activity-type-change-password-self-service)
 * [Redefinir senha (pelo administrador)](#activity-type-reset-password-by-admin)
 * [Redefinir a senha (autoatendimento)](#activity-type-reset-password-self-service)
 * [Progresso da atividade de fluxo de redefinição de senha de autoatendimento](#activity-type-self-serve-password-reset-flow-activity-progress)
 * [Desbloquear a conta de usuário (autoatendimento)](#activity-type-unlock-user-account-self-service)
 * [Usuário registrado para redefinição de senha de autoatendimento](#activity-type-user-registered-for-self-service-password-reset)
* [**Como recuperar eventos de gerenciamento de senhas da API de Relatórios e Eventos do Azure AD**](#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
 * [Limitações de recuperação de dados da API de emissão de relatórios](#reporting-api-data-retrieval-limitations)
* [**Como baixar eventos de registro de redefinição de senhas rapidamente com o PowerShell**](#how-to-download-password-reset-registration-events-quickly-with-powershell)
* [**Como exibir relatórios de gerenciamento de senhas no portal clássico**](#how-to-view-password-management-reports-in-the-classic-portal)
* [**Exibir atividade de registro de redefinição de senha em sua organização no portal clássico**](#view-password-reset-registration-activity-in-the-classic-portal)
* [**Exibir atividade de redefinição de senha em sua organização no portal clássico**](#view-password-reset-activity-in-the-classic-portal)


## <a name="overview-of-password-management-reports"></a>Visão geral dos relatórios de gerenciamento de senhas
Após a implantação da redefinição de senhas, uma das próximas etapas mais comuns é ver como ela está sendo usada na organização.  Por exemplo, talvez você queira obter uma visão de como os usuários estão s registrando para a redefinição de senha ou quantas redefinições de senha foram feitas nos últimos dias.  Aqui estão algumas perguntas comuns que você poderá responder com os relatórios de gerenciamento de senhas que existem nos [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) hoje:

* Quantas pessoas foram registradas para a redefinição de senhas?
* Quem se registrou para a redefinição de senhas?
* Que dados as pessoas estão registrando?
* Quantas pessoas redefiniram suas senhas nos últimos sete dias?
* Quais são os métodos mais comuns que os usuários ou administradores usam para redefinir suas senhas?
* Quais são os problemas comuns que os usuários ou administradores enfrentam ao tentar usar a redefinição de senhas?
* Quais administradores estão redefinindo suas próprias senhas com frequência?
* Há qualquer atividade suspeita acontecendo na redefinição de senhas?

## <a name="how-to-view-password-management-reports"></a>Como exibir Relatórios de Gerenciamento de Senhas
Na nova experiência do [Portal do Azure](https://portal.azure.com), temos uma maneira melhor de exibir a redefinição de senha e a atividade de registro de redefinição de senha.  Siga as etapas abaixo para encontrar os eventos de redefinição de senha e registro de redefinição de senha no novo [Portal do Azure](https://portal.azure.com):

1. Navegue até [**portal.azure.com**](https://portal.azure.com)
2. Clique no menu **Mais serviços** na navegação à esquerda principal do Portal do Azure
3. Procure **Azure Active Directory** na lista de serviços e selecione-o
4. Clique em **Usuários e Grupos** no menu de navegação do Azure Active Directory
5. Clique no item de navegação **Logs de Auditoria** no menu de navegação Usuários e Grupos. Isso mostrará todos os eventos de auditoria que ocorrem em relação a todos os usuários no diretório. Você pode filtrar essa exibição para ver também todos os eventos relacionados a senhas.
6. Para filtrar esse modo de exibição somente para eventos relacionados ao gerenciamento de senhas, clique no botão **Filtrar** na parte superior da folha.
7. No menu **Filtro**, selecione a lista suspensa **Categoria** e altere-a para o tipo de categoria **Gerenciamento de Senha de Autoatendimento**.
8. Opcionalmente, filtre mais a lista escolhendo a **Atividade** específica em que você está interessado
### <a name="direct-link-to-user-audit-blade"></a>Link direto para a folha de Auditoria de Usuário
Se você entrou no portal, aqui está um link direto para a folha de auditoria do usuário em que você pode ver esses eventos:

* [Ir para o modo de exibição de auditoria de gerenciamento de usuários diretamente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit)

### <a name="directory-roles-allowed-to-read-reports"></a>Funções de Diretório podem ler relatórios
Atualmente, as seguintes funções de diretório podem ler relatórios de Gerenciamento de Senhas do Azure AD no portal do Azure clássico:

* Administrador global

Para poder ler esses relatórios, um administrador global na empresa deve ter aceitado que esses dados sejam recuperados em nome da organização, acessando a guia de relatórios ou os logs de auditoria pelo menos uma vez. Até fazer isso, os dados não serão coletados para sua organização.

Para ler mais sobre as funções de diretório e o que elas podem fazer, confira [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles).

## <a name="self-service-password-management-activity-types"></a>Tipos de atividades de Gerenciamento de Senha de autoatendimento
Os tipos de atividade a seguir aparecem na categoria de evento de auditoria **Gerenciamento de Senhas de Autoatendimento**.  A seguir há uma descrição de cada um deles.

* [**Impedido de executar redefinição de senha de autoatendimento**](#activity-type-blocked-from-self-service-password-reset) – indica que um usuário tentou redefinir uma senha, usar uma porta específica ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* [**Alterar senha (autoatendimento)**](#activity-type-change-password-self-service) – indica que um usuário executou uma alteração de senha voluntária ou forçada (devido à expiração).
* [**Redefinição de senha (pelo administrador)**](#activity-type-reset-password-by-admin) - indica que um administrador executou uma redefinição em nome do usuário no Portal do Azure.
* [**Redefinição de senha (autoatendimento)**](#activity-type-reset-password-self-service) - indica que um usuário redefiniu com êxito a senha no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com).
* [**Progresso de atividade do fluxo de redefinição de senha de autoatendimento**](#activity-type-self-serve-password-reset-flow-activity-progress) – indica cada etapa específica que um usuário executa (como uma porta de autenticação de redefinição de senha específica) como parte do processo de redefinição de senha.
* [**Desbloquear a conta de usuário (autoatendimento)**](#activity-type-unlock-user-account-self-service) – indica que um usuário desbloqueou com êxito sua conta do Active Directory sem redefinir a senha no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso de [desbloqueio de conta do AD sem redefinir](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password).
* [**Usuário registrado para redefinição de senha de autoatendimento**](#activity-type-user-registered-for-self-service-password-reset) – indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento.

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
* **Ator de Atividade** - o usuário que alterou sua senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que alterou sua senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
 * _Sucesso_ - indica que um usuário alterou a senha com êxito
 * _Falha_ - indica que um usuário não pôde alterar sua senha. Clicar na linha permitirá que você veja a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.
* **Razão da Falha do Status de Atividade** -
 * _FuzzyPolicyViolationInvalidPassword_ - o usuário selecionou uma senha que foi excluída automaticamente porque os recursos de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: redefinir senha (pelo administrador)
A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um administrador executou uma redefinição em nome de um usuário no Portal do Azure.
* **Ator de Atividade** - o administrador que realizou a redefinição em nome de outro usuário final ou administrador de senha. Deve ser o administrador global, o administrador de senha, o administrador de usuário ou o administrador de assistência técnica.
* **Destino de Atividade** - o usuário cuja senha foi redefinida. Pode ser um usuário final ou um administrador diferente.
* **Status de Atividade Permitida**
 * _Sucesso_ - indica que um administrador redefine a senha de um usuário com êxito
 * _Falha_ - indica que um administrador falhou ao alterar a senha do usuário. Clicar na linha permitirá que você veja a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: redefinir senha (autoatendimento)
A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário redefiniu a senha com êxito no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com).
* **Ator de Atividade** - o usuário que redefiniu sua senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que redefiniu sua senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
 * _Sucesso_ - indica que um usuário redefiniu sua própria senha com êxito
 * _Falha_ - indica que um usuário falhou ao redefinir sua senha. Clicar na linha permitirá que você veja a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.
* **Razão da Falha do Status de Atividade** -
 * _FuzzyPolicyViolationInvalidPassword_ - o administrador selecionou uma senha que foi excluída automaticamente porque os recursos de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: progresso de atividade de fluxo de redefinição de senha de autoatendimento
A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica cada etapa específica que um usuário executa (como uma porta de autenticação de redefinição de senha específica) como parte do processo de redefinição de senha.
* **Ator de Atividade** - o usuário que realizou a parte do fluxo de redefinição de senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que realizou a parte do fluxo de redefinição de senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
 * _Sucesso_ - indica que um usuário concluiu com êxito uma etapa específica do fluxo de redefinição de senha.
 * _Falha_ - indica que uma etapa específica do fluxo de redefinição de senha falhou. Clicar na linha permitirá que você veja a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.
* **Razões de Status de Atividade Permitida**
 * Confira a tabela abaixo para ver [todas as razões de status de atividade de redefinição permitida](#allowed-values-for-details-column)

### <a name="activity-type-unlock-user-account-self-service"></a>Tipo de atividade: desbloquear conta de usuário (autoatendimento)
A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário desbloqueou com êxito sua conta do Active Directory sem redefinir a senha no [Portal de Redefinição de Senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso de [desbloqueio de conta do AD sem redefinir](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password).
* **Ator de Atividade** - o usuário que desbloqueou sua conta sem redefinir a senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que desbloqueou sua conta sem redefinir a senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
 * _Sucesso_ - indica que um usuário desbloqueou sua própria conta com êxito
 * _Falha_ - indica que um usuário falhou ao desbloquear sua conta. Clicar na linha permitirá que você veja a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: usuário registrado para redefinição de senha de autoatendimento
A seguinte lista explica essa atividade em detalhes:

* **Descrição da Atividade** – indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento.
* **Ator de Atividade** - o usuário que se registrou para a redefinição de senha. Pode ser um usuário final ou um administrador.
* **Destino de Atividade** - o usuário que se registrou para a redefinição de senha. Pode ser um usuário final ou um administrador.
* **Status de Atividade Permitida**
 * _Sucesso_ - indica que um usuário se registrou com êxito para a redefinição de senha de acordo com a política atual.
 * _Falha_ - indica que um usuário falhou ao se registrar para a redefinição de senha. Clicar na linha permitirá que você veja a categoria **Motivo do Status de Atividade** para saber mais sobre o motivo da falha. Observação - isso não significa que um usuário não possa redefinir sua própria senha, apenas que ele não concluiu o processo de registro. Se houver dados não verificados na conta que estejam corretos (como um número de telefone que não foi validado), mesmo que eles não tenham verificado esse número de telefone, ainda poderão usá-lo para redefinir a senha. Para obter mais informações, confira [O que acontece quando um usuário é registrado?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Como recuperar eventos de gerenciamento de senhas da API de Relatórios e Eventos do Azure AD
Desde agosto de 2015, a API de Relatórios e Eventos do Azure AD dá suporte à recuperação de todas as informações incluídas nos relatórios de redefinição de senha e registro de redefinição de senha. Usando essa API, você pode baixar eventos de redefinição de senha individuais e de registro de redefinição de senha para a integração à tecnologia de relatórios de sua escolha.

### <a name="how-to-get-started-with-the-reporting-api"></a>Como começar a usar a API de relatórios
Para acessar esses dados, você precisará gravar um pequeno aplicativo ou script para recuperá-los de nossos servidores. [Saiba como começar com a API de Relatório do AD do Azure](active-directory-reporting-api-getting-started.md).

Quando você tiver um script de trabalho, em seguida desejará examinar os eventos de registro e redefinição de senhas que podem ser recuperados para atender suas situações.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): lista as colunas disponíveis para os eventos de redefinição de senhas
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): lista as colunas disponíveis para os eventos do registro de redefinição de senhas

### <a name="reporting-api-data-retrieval-limitations"></a>Relatando limitações de recuperação de dados de API
Atualmente, os Relatórios do Azure AD e a API de Eventos recuperam até **75.000 eventos individuais** dos tipos [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent), abrangendo os **últimos 30 dias**.

Se precisar recuperar ou armazenar dados além desta janela, sugerimos persisti-los em um banco de dados externo e usar a API para consultar os deltas resultantes. Uma prática recomendada é começar a recuperar esses dados ao iniciar o processo de redefinição de senha na organização, persisti-lo externamente e continuar a acompanhar os deltas desse ponto em diante.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Como baixar eventos de registro de redefinição de senha rapidamente com o PowerShell
Além de usar os Relatórios do Azure AD e a API de Eventos diretamente, você também pode usar o script do PowerShell abaixo para eventos de registro recentes no diretório. Isso é útil caso você deseje ver quem foi registrado recentemente ou garantir que a distribuição de redefinição de senha ocorra conforme o esperado.

* [Script do PowerShell de Atividade de Registro do Azure AD SSPR](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

## <a name="how-to-view-password-management-reports-in-the-classic-portal"></a>Como exibir relatórios de gerenciamento de senhas no portal clássico
Para localizar os relatórios de gerenciamento de senhas, siga as etapas abaixo:

1. Clique na **Extensão do Active Directory** no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Selecione o diretório na lista que aparece no portal.
3. Clique na guia **Relatórios** .
4. Procure na seção **Logs de Atividade** .
5. Selecione o relatório **Atividade de redefinição de senhas** ou **Atividade de registro de redefinição de senhas**.

   ![][001]

## <a name="view-password-reset-registration-activity-in-the-classic-portal"></a>Exibir atividade de registro de redefinição de senha no portal clássico
O relatório de atividade de registro de redefinição de senhas mostra todos os registros de redefinição de senhas que ocorreram na sua organização.  Um registro de redefinição de senhas é exibido no relatório para qualquer usuário que registrou com êxito as informações de autenticação no portal de registro de senha de redefinição de senhas ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

* **Intervalo de tempo máximo**: 30 dias
* **Número máximo de linhas**: 75.000
* **Baixável**: sim, por meio de arquivo CSV

    ![][002]

### <a name="description-of-report-columns"></a>Descrição das colunas do relatório
A lista a seguir explica cada uma das colunas do relatório em detalhes:

* **Usuário** – o usuário que tentou uma operação de registro de redefinição de senha.
* **Função** – a função do usuário no diretório.
* **Data e hora** – a data e a hora da tentativa.
* **Dados Registrados** – os dados de autenticação fornecidos pelo usuário durante o registro de redefinição de senha.

### <a name="description-of-report-values"></a>Descrição dos valores de relatório
A tabela a seguir descreve os diferentes valores permitidos para cada coluna:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Dados Registrados |**Email Alternativo** – email alternativo ou email de autenticação usado pelo usuário para se autenticar<p><p>**Telefone Comercial**– telefone comercial usado pelo usuário para se autenticar<p>**Celular** - celular ou telefone de autenticação usado pelo usuário para se autenticar<p>**Perguntas de Segurança** – perguntas de segurança usadas pelo usuário para se autenticar<p>**Qualquer combinação dos itens acima (por exemplo, Email Alternativo + Celular)** – ocorre quando uma política de 2 acessos é especificada e mostra quais dos dois métodos o usuário utilizou para autenticar a solicitação de redefinição de sua senha. |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>Exibir atividade de redefinição de senha no portal clássico
Esse relatório mostra todas as tentativas de redefinição de senha que ocorreram na sua organização.

* **Intervalo de tempo máximo**: 30 dias
* **Número máximo de linhas**: 75.000
* **Baixável**: sim, por meio de arquivo CSV

    ![][003]

### <a name="description-of-report-columns"></a>Descrição das colunas do relatório
A lista a seguir explica cada uma das colunas do relatório em detalhes:

1. **Usuário** – o usuário que tentou uma operação de redefinição de senha (com base no campo ID de Usuário fornecido quando o usuário redefine uma senha).
2. **Função** – a função do usuário no diretório.
3. **Data e hora** – a data e a hora da tentativa.
4. **Método(s) usado(s)** – os métodos de autenticação de usuário usados para essa operação de redefinição.
5. **Resultado** – resultado final da operação de redefinição de senha.
6. **Detalhes** – os detalhes do motivo pelo qual a redefinição de senha resultou no valor obtido.  Também inclui as etapas de atenuação que você pode tomar para resolver um erro inesperado.

### <a name="description-of-report-values"></a>Descrição dos valores de relatório
A tabela a seguir descreve os diferentes valores permitidos para cada coluna:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Métodos usados |**Email Alternativo** – email alternativo ou email de autenticação usado pelo usuário para se autenticar<p>**Telefone Comercial** – telefone comercial usado pelo usuário para se autenticar<p>**Celular** – celular ou telefone de autenticação usado pelo usuário para se autenticar<p>**Perguntas de Segurança** – perguntas de segurança usadas pelo usuário para se autenticar<p>**Qualquer combinação dos itens acima (por exemplo, Email Alternativo + Celular)** – ocorre quando uma política de 2 acessos é especificada e mostra quais dos dois métodos o usuário utilizou para autenticar a solicitação de redefinição de sua senha. |
| Result |**Abandonado** – redefinição de senha iniciada pelo usuário mas, em seguida, interrompida pela metade sem ser concluída<p>**Bloqueado** – a conta do usuário foi impedida de usar a redefinição de senha devido à tentativa de usar a página de redefinição de senha ou uma porta única de redefinição de senha muitas vezes em um período de 24 horas<p>**Cancelado** – redefinição de senha iniciada pelo usuário, mas o botão Cancelar foi clicado para cancelar a sessão pela metade <p>**Administrador Contatado** – o usuário teve um problema durante a sessão que ele não pôde resolver, assim, o usuário clicou no link "Entre em contato com seu administrador" em vez de concluir o fluxo de redefinição de senha<p>**Falha** – o usuário não pôde redefinir uma senha, provavelmente porque o usuário não foi configurado para usar o recurso (por exemplo, nenhuma licença, informações de autenticação ausentes, senha gerenciada no local, mas o write-back está desativado).<p>**Bem-sucedida** – a redefinição de senha foi bem-sucedida. |
| Detalhes |Consulte a tabela abaixo. |

### <a name="allowed-values-for-details-column"></a>Valores permitidos para a coluna de detalhes
Abaixo está a lista de tipos de resultado que você pode esperar ao usar o relatório de atividade de redefinição de senha:

| Detalhes | Tipo de resultado |
| --- | --- |
| Abandonado pelo usuário depois de concluir a opção de verificação de email |Abandonado |
| Abandonado pelo usuário depois de concluir a opção de verificação de SMS móvel |Abandonado |
| Abandonado pelo usuário depois de concluir a opção de verificação de chamada de voz móvel |Abandonado |
| Abandonado pelo usuário depois de concluir a opção de verificação de chamada de voz comercial |Abandonado |
| Abandonado pelo usuário depois de concluir a opção de perguntas de segurança |Abandonado |
| Abandonado pelo usuário depois de inserir sua ID de usuário |Abandonado |
| Abandonado pelo usuário depois de iniciar a opção de verificação de email |Abandonado |
| Abandonado pelo usuário depois de iniciar a opção de verificação de SMS móvel |Abandonado |
| Abandonado pelo usuário depois de iniciar a opção de verificação de chamada de voz móvel |Abandonado |
| Abandonado pelo usuário depois de iniciar a opção de verificação de chamada de voz comercial |Abandonado |
| Abandonado pelo usuário depois de iniciar a opção de perguntas de segurança |Abandonado |
| Abandonado pelo usuário antes de selecionar uma nova senha |Abandonado |
| Abandonado pelo usuário ao selecionar uma nova senha |Abandonado |
| O usuário inseriu muitos códigos de verificação de SMS inválidos e está bloqueado para 24 horas |Bloqueado |
| O usuário tentou muitas vezes a verificação de voz por telefone celular e está bloqueado por 24 horas |Bloqueado |
| O usuário tentou muitas vezes a verificação de voz comercial e está bloqueado por 24 horas |Bloqueado |
| O usuário tentou responder às perguntas de segurança muitas vezes e está bloqueado por 24 horas |Bloqueado |
| O usuário tentou verificar um número de telefone muitas vezes e está bloqueado por 24 horas |Bloqueado |
| O usuário fez o cancelamento antes de passar pelos métodos de autenticação obrigatórios |Cancelado |
| O usuário fez o cancelamento antes de enviar uma nova senha |Cancelado |
| O usuário contatou um administrador depois de tentar a opção de verificação de email |Administrador contatado |
| O usuário contatou um administrador depois de tentar a opção de verificação de SMS móvel |Administrador contatado |
| O usuário contatou um administrador depois de tentar a opção de verificação de chamada de voz móvel |Administrador contatado |
| O usuário contatou um administrador depois de tentar a opção de verificação de chamada de voz comercial |Administrador contatado |
| O usuário contatou um administrador depois de tentar a opção de verificação de pergunta de segurança |Administrador contatado |
| A redefinição de senha não está habilitada para este usuário. Habilite a redefinição de senha na guia Configurar para resolver o problema |Falha |
| O usuário não tem uma licença. Você pode adicionar uma licença para o usuário para resolver o problema |Falha |
| O usuário tentou redefinir em um dispositivo sem cookies habilitados |Falha |
| A conta do usuário tem métodos de autenticação insuficiente definidos. Adicione informações de autenticação para resolver esse problema |Falha |
| A senha do usuário é gerenciada localmente. Você pode habilitar o Write-back de Senha para resolver o problema |Falha |
| Não foi possível acessar o serviço de redefinição de senha no local. Verifique o log de eventos de seu computador de sincronização |Falha |
| Encontramos um problema durante a redefinição de senha local do usuário. Verifique o log de eventos de seu computador de sincronização |Falha |
| Este usuário não é membro do grupo de usuários de redefinição de senha. Adicione esse usuário ao grupo para resolver o problema. |Falha |
| A redefinição de senha foi desabilitada inteiramente para este locatário. Confira [aqui](http://aka.ms/ssprtroubleshoot) para resolver isto. |Falha |
| O usuário redefiniu a senha com êxito |Bem-sucedido |

## <a name="next-steps"></a>Próximas etapas
Veja abaixo links para todas as páginas de documentação sobre Redefinição de Senha do AD do Azure:

* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-my-password).
* [**Como funciona**](active-directory-passwords-how-it-works.md) – saiba mais sobre os seis diferentes componentes do serviço e o que cada um deles faz
* [**Introdução**](active-directory-passwords-getting-started.md) – saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md)- aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) – aprenda a solucionar rapidamente os problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) – aprofunde-se nos detalhes técnicos do funcionamento do serviço

[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"

