---
title: Como usar o pacote de conteúdo do Power BI do Azure Active Directory | Microsoft Docs
description: Aprenda a usar o Pacote de Conteúdo do Power BI do Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c0326a6b611d5f3d5633db2d2b64b8cdc15e10a7
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816677"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Como usar o Pacote de Conteúdo do Power BI do Azure Active Directory

|  |
|--|
|Atualmente, o pacote de conteúdo do Azure AD Power BI usa as APIs do Graph do Azure AD para recuperar dados do locatário do Azure AD. Como resultado, você poderá ver alguma disparidade entre os dados disponíveis no pacote de conteúdo e os dados recuperados usando as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

Como administrador de TI, é necessário entender como seus usuários adotam e usam os recursos do Azure Active Directory. Isso permite que você planeje sua infraestrutura de TI e sua comunicação para aumentar o uso e obter o máximo proveito de recursos do Azure AD. Com o Pacote de Conteúdo do Power BI para Azure Active Directory, é possível analisar ainda mais seus dados para coletar insights mais avançados sobre o que acontece em seu diretório. Com a integração das APIs do Azure Active Directory ao Power BI, é possível baixar facilmente o pacote de conteúdo predefinido e obter insights sobre todas as atividades no Azure Active Directory usando a experiência de visualização avançada oferecida pelo Power BI. Você pode criar seu próprio painel e compartilhá-lo facilmente com qualquer pessoa em sua organização. 

## <a name="prerequisites"></a>Pré-requisitos

É necessária uma licença premium (P1/P2) do Azure AD para usar o pacote de conteúdo. 

## <a name="install-the-content-pack"></a>Instalar o pacote de conteúdo

Confira o [início rápido](quickstart-install-power-bi-content-pack.md) para instalar o pacote de conteúdo do Power BI do Azure AD.

## <a name="what-can-i-do-with-this-content-pack"></a>O que posso fazer com esse pacote de conteúdo?

Antes de falarmos sobre o que você pode fazer com o pacote de conteúdo, veja rapidamente os vários relatórios que existem no pacote de conteúdo. Os dados do relatório abrangem até os **últimos 30 dias**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Relatórios incluídos nesta versão do pacote de conteúdo de logs do Azure Active Directory

**Relatório de uso do aplicativo e tendência**: obtenha informações sobre os aplicativos usados em sua organização e quais estão sendo usados com maior frequência e quando. Você pode usar esse relatório para coletar informações sobre como um aplicativo distribuído recentemente em sua organização está sendo usado ou para descobrir quais aplicativos são populares. Ao fazer isso, você poderá melhorar o uso se vir que o aplicativo não está sendo usado.

**Entradas por local e os usuários**: obtenha informações sobre todas as entradas executadas usando o Azure Identity e veja informações sobre a identidade dos usuários. Com isso, você poderá se aprofundar em entradas individuais e responder a perguntas como:

- Onde é feita a entrada de determinado usuário?
- Qual usuário tem a maioria dos logons e de onde? 
- A entrada foi bem-sucedida?  
 
Você pode analisar detalhes clicando em uma data ou um local específico.

**Usuários exclusivos por aplicativo**: veja todos os usuários exclusivos que usam determinado aplicativo. Isso inclui apenas os usuários que entraram "*com êxito*" em um aplicativo.

**Entradas de dispositivo**: veja o tipo de sistema operacional e navegadores usados por usuários em sua organização com informações detalhadas sobre os usuários, incluindo:

- Nome do Usuário
- Endereço IP
- Local padrão 
- Status de entrada 

Com esse relatório, você pode entender os vários perfis de dispositivo usados em sua organização e determinar as políticas de dispositivo com base no que é usado

**Funil SSPR**: entenda como redefinições de senha estão sendo feitas em sua organização. Veja quantas redefinições foram tentadas por meio da ferramenta SSPR e quantas delas foram bem-sucedidas. Aprofunde-se nas falhas de redefinição de senha usando o SSPR e entenda por que determinadas falhas ocorreram. Esse relatório fornece uma compreensão mais profunda de como a ferramenta SSPR é usada dentro de sua organização para que você possa tomar as decisões corretas.

## <a name="customizing-azure-ad-activity-content-pack"></a>Personalizando o pacote de conteúdo da Atividade do Azure AD

**Alterar a visualização**: você pode alterar uma visualização de relatório clicando em **Editar Relatório** e selecionando a visualização desejada.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Incluir campos adicionais**: você pode adicionar um campo ao relatório ou removê-lo selecionando o visual no qual deseja adicionar ou remover o campo. No exemplo a seguir, estou adicionando o campo "status de entrada” para o modo de exibição de tabela. 
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Fixar visualizações ao seu painel**: você pode personalizar seu painel, incluir suas próprias visualizações no relatório e fixá-lo no painel. No exemplo a seguir, adicionei um novo filtro chamado "Status de entrada" e o incluí no relatório. Também alterei a visualização de gráfico de barras para gráfico de linha e posso fixar este novo visual no painel.

![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/12.png) 

![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 

 


**Compartilhar seu dashboard**: depois de criar o conteúdo desejado, você pode compartilhar o painel com os usuários em sua organização. Lembre-se de que, depois de compartilhar o relatório, eles poderão ver os campos que você selecionou no relatório.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Agendar uma atualização diária de seu relatório do Power BI

Para agendar uma atualização diária de seu relatório do Power BI, vá para **Conjuntos de Dados > Configurações > Agendar Atualização** e defina-o conforme mostrado abaixo.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Atualizar para a versão mais recente do pacote de conteúdo

Se você quiser atualizar seu pacote de conteúdo para obter uma versão mais recente:

- Baixe o novo pacote de conteúdo e configure-o de acordo com as instruções listadas neste artigo.

- Depois de configurá-lo, vá para **Fonte de Dados > Configurações > Credenciais da fonte de dados** e digite novamente suas credenciais, conforme mostrado abaixo

    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Assim que a nova versão do pacote de conteúdo estiver funcionando, você poderá remover a versão antiga, se necessário, excluindo os relatórios subjacentes e os conjuntos de dados associados a esse pacote de conteúdo.

## <a name="still-having-issues"></a>Ainda está com problemas? 

Confira nosso [guia de solução de problemas](troubleshoot-content-pack.md). Para obter ajuda geral com o Power BI, confira esses [artigos de ajuda](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Próximas etapas

* [Instalar pacote de conteúdo do Power BI](quickstart-install-power-bi-content-pack.md).
* [Solucionar problemas com erros do pacote de conteúdo](troubleshoot-content-pack.md).
* [O que são relatórios do Azure AD?](overview-reports.md).
