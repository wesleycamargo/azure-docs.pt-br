---
title: Relatórios de atividade de entrada no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividades de entrada no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0d651f9d4fa48cec3a61f1f307f4447fe2cba63b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248944"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de entrada no portal do Azure Active Directory

Com os relatórios do Azure Active Directory (Azure AD) no [portal do Azure](https://portal.azure.com) você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

A arquitetura de relatório no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
    - **Logs de auditoria** – informações de atividade do sistema sobre o gerenciamento de usuários e de grupos, sobre os aplicativos gerenciados e sobre as atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, veja Entradas de risco.
    - **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, consulte Usuários sinalizados para risco.

Este tópico fornece uma visão geral das atividades de entrada.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Usuários na função Admin de segurança, Leitor de segurança, Leitor de relatório
* Administradores globais
* Qualquer usuário (não administradores) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?
* Seu locatário deve ter uma licença do Azure AD Premium associada a ele para ver o relatório de atividade de entrada


## <a name="sign-in-activities"></a>Atividades de entrada

Com as informações fornecidas pelo relatório de entrada de usuário, você encontra respostas para perguntas como:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

Seu primeiro ponto de entrada para todos os dados de atividades de login é **Login** na seção Atividade do **Active Directory do Azure**.


![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/61.png "Atividade de entrada")


Um log de entradas tem um modo de exibição de lista padrão que mostra:

- A hora de entrada
- O usuário relacionado
- O aplicativo no qual o usuário entrou
- O status de entrada
- O status da detecção de riscos
- O status do requisito de MFA (autenticação multifator)

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/01.png "Atividade de entrada")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/19.png "Atividade de entrada")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/02.png "Atividade de entrada")

Ao clicar em um item na exibição de lista, você obterá mais detalhes sobre ele em uma exibição horizontal.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/03.png "Atividade de entrada")

> [!NOTE]
> Os clientes agora podem solucionar problemas de políticas de acesso condicional por meio de todos os relatórios de login. Ao clicar na guia **Acesso condicional** para um registro de entrada, os clientes podem rever o status de acesso condicional e analisar os detalhes das políticas aplicadas ao login e o resultado de cada política.
> Para obter mais informações, consulte as [Perguntas frequentes sobre as informações de CA em todos os logins](active-directory-reporting-faq.md#conditional-access).

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/ConditionalAccess.png "Atividade de entrada")


## <a name="filter-sign-in-activities"></a>Filtrar atividades de entrada

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de entradas usando os seguintes campos padrão:

- Usuário
- Aplicativo
- Status de entrada
- Status da detecção de riscos
- Data

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/04.png "Atividade de entrada")

O filtro **Usuário** permite que você especifique o nome ou o UPN (nome UPN) do usuário desejado.

O filtro **Aplicativo** permite que você especifique o nome do aplicativo desejado.

O filtro **status de entrada** permite que você selecione:

- Todos
- Sucesso
- Failure

O filtro **Riscos Detectados** filtro permite que você selecione:

- Todos
- SIM
- Não 

O filtro **Data** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Intervalo de tempo personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

Se você adicionar outros campos ao modo de exibição de entradas, esses campos serão adicionados automaticamente à lista de filtros. Por exemplo, ao adicionar o campo **Aplicativo Cliente** à sua lista, você também obtém outra opção de filtro que permite definir os seguintes filtros:

- Navegador      
- Exchange ActiveSync (com suporte)               
- Exchange ActiveSync (sem suporte)
- Outros clientes               
    - IMAP
    - MAPI
    - Clientes mais antigos do Office
    - POP
    - SMTP


![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/12.png "Atividade de entrada")


## <a name="download-sign-in-activities"></a>Baixar atividades de entrada

Você pode baixar os dados de atividade de entrada se desejar trabalhar com eles fora do portal do Azure. Clicar em **Baixar** cria um arquivo CSV contendo os 5 mil registros mais recentes.  Além de um botão de download, o portal do Azure também fornece uma opção para gerar um script para baixar seus dados.  

![Baixar](./media/active-directory-reporting-activity-sign-ins/71.png "Baixar")

Se você precisar de mais flexibilidade, você pode usar a solução de script. Clicar em **Script** cria um script do PowerShell que inclui todos os filtros que você definiu. Baixe e execute esse script no **modo de administrador** para gerar o arquivo CSV. 

### <a name="running-the-script-on-a-windows-10-machine"></a>Executando o script em uma máquina com Windows 10

Se você deseja executar o script em uma máquina do **Windows 10**, é necessário executar algumas etapas adicionais primeiro. 

1. Instalar o [módulo AzureRM](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. Importe o módulo abrindo um prompt do PowerShell e executando o comando **Import-Module AzureRM**.
3. Execute **Set-ExecutionPolicy irrestrito** e escolha **Sim para Todos**. 
4. Agora você pode executar o script do PowerShell baixado no modo de administrador para gerar o arquivo CSV.

Além da implementação técnica, o número de registros que você pode baixar também é restringido pelas [políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md).  


## <a name="sign-in-activities-shortcuts"></a>Atalhos de atividades de entrada

Além do Azure Active Directory, o portal do Azure fornece pontos de entrada adicionais para os dados de atividades de entrada:

- Visão geral da proteção de segurança de identidade
- Usuários
- Grupos
- Aplicativos empresariais


### <a name="users-sign-ins-activities"></a>Atividades de logon de usuários

Com as informações fornecidas pelo relatório de entrada de usuário, você encontra respostas para perguntas como:

- O que é o padrão de entrada de um usuário?
- Quantos usuários entraram em uma semana?
- Qual é o status dessas entradas?

O ponto de entrada para esses dados é o gráfico de entradas do usuário na página de visão geral da **proteção de segurança de identidade**. O grafo de entrada do usuário mostra agregações semanais de entradas para todos os usuários em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/06.png "Atividade de entrada")

Quando você clica em um dia no gráfico de entradas, obtém uma lista detalhada das atividades de entrada do dia.

Cada linha na lista de atividades de entrada mostra:

* Quem entrou?
* Qual aplicativo era o destino da entrada?
* Qual é o status da entrada?
* Qual é o status de MFA da entrada?

Ao clicar em um item, você verá mais detalhes sobre a operação de entrada:

- Id de Usuário
- Usuário
- Nome de Usuário
- ID do aplicativo
- Aplicativo
- Cliente
- Localização
- Endereço IP
- Data
- MFA obrigatório
- Status de entrada

 
Na página **Usuários**, você obtém uma visão geral completa de todas as entradas do usuário clicando em **Entradas** na seção **Atividade**.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/08.png "Atividade de entrada")

## <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são os três principais aplicativos em sua organização?
* Recentemente, eu implantei um aplicativo. Como ele está se saindo?

Seu ponto de entrada para esses dados é composto pelos três principais aplicativos em sua organização no relatório dos 30 últimos dias, presente na seção **Visão geral**, em **Aplicativos empresariais**.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/10.png "Atividade de entrada")

As agregações semanais ao grafo de uso do aplicativo de entradas para seus três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/47.png "Atividade de entrada")

Se desejar, você pode definir o foco em um aplicativo específico.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Relatórios")

Quando você clica em um dia no grafo de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.

A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/11.png "Atividade de entrada")



## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre os códigos de erro da atividade de entrada, confira o [Códigos de erro do relatório de atividade de entrada no portal do Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

