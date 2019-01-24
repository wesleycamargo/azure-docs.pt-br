---
title: Como usar o pacote de conteúdo do Power BI do Azure Active Directory | Microsoft Docs
description: Aprenda a usar o Pacote de Conteúdo do Power BI do Azure Active Directory
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: eeabbf70650b4304788d1cd6a40d5924a22f3650
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812736"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Como usar o pacote de conteúdo do Power BI do Azure Active Directory

|  |
|--|
|Atualmente, o pacote de conteúdo do Azure AD Power BI usa as APIs do Graph do Azure AD para recuperar dados do locatário do Azure AD. Como resultado, você poderá ver alguma disparidade entre os dados disponíveis no pacote de conteúdo e os dados recuperados usando as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

O pacote de conteúdo do Power BI para Azure AD (Azure Active Directory) contém relatórios pré-criados para ajudá-lo a reconhecer como os usuários adotam e usam os recursos do Azure AD. Isso permite que você obtenha informações sobre todas as atividades no diretório, usando a experiência de exibição avançada no Power BI. Além disso, é possível criar seu próprio painel e compartilhá-lo com qualquer pessoa na organização. 

## <a name="prerequisites"></a>Pré-requisitos

É necessária uma licença premium (P1/P2) do Azure AD para usar o pacote de conteúdo. Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory.

## <a name="install-the-content-pack"></a>Instalar o pacote de conteúdo

Confira o [início rápido](quickstart-install-power-bi-content-pack.md) para instalar o pacote de conteúdo do Power BI do Azure AD.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Os relatórios incluídos nesta versão do Azure AD registram o Pacote de Conteúdo

Os relatórios a seguir estão incluídos no pacote de conteúdo do Power BI do Azure AD. Os relatórios contêm dados dos **últimos 30 dias**.

**Relatório de tendências e uso do aplicativo**:  Esse relatório fornece informações sobre os aplicativos usados na organização. É possível obter uma lista dos aplicativos mais populares ou reconhecer como um aplicativo implantado recentemente na organização está sendo usado. Isso permite acompanhar e melhorar o uso ao longo do tempo.

**Entradas por localização e usuários**: Esse relatório fornece dados sobre todas as entradas realizadas usando o Azure Identity. Com esse relatório, é possível fazer busca detalhada para entradas individuais e responder a perguntas como:

- A partir de onde esse usuário entrou?
- Qual usuário tem a maioria dos logons e de onde? 
- A entrada foi bem-sucedida?  
 
Também é possível filtrar os resultados, selecionando uma data ou local específico.

**Usuários exclusivos por aplicativo**:  Esse relatório fornece uma exibição de todos os usuários exclusivos que usam um determinado aplicativo. Inclui somente os usuários que tenham entrado "*com êxito*" em um aplicativo.

**Entradas de dispositivo**: Esse relatório ajuda a reconhecer os vários perfis de dispositivos usados na organização e a determinar políticas de dispositivos com base no uso. Ele fornece dados sobre o tipo de sistema operacional e navegadores usados para entrar nos aplicativos, além de informações detalhadas sobre os usuários, incluindo:

- Nome do Usuário
- Endereço IP
- Local padrão 
- Status de entrada 

**Funil SSPR**: Esse relatório ajuda a reconhecer como a ferramenta SSPR é usada na organização. É possível exibir quantas redefinições de senha foram tentadas através da ferramenta SSPR e quantas delas foram realizadas com êxito. Também é possível obter detalhes nas falhas de redefinição de senha e reconhecer por que algumas falhas ocorreram. 

## <a name="customize-azure-ad-activity-content-pack"></a>Personalizar o pacote de conteúdo da Atividade do Azure AD

**Alterar visualização**:  Você pode alterar uma visualização de relatório clicando em **Editar Relatório** e selecionando a visualização desejada.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Incluir campos adicionais**:  Você pode adicionar um campo ao relatório ou removê-lo selecionando o visual no qual deseja adicionar ou remover o campo. Por exemplo, é possível adicionar o campo "status de entrada" à exibição de tabela, conforme mostrado abaixo. 
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Fixar visualizações interessantes no painel**:  É possível personalizar o painel, incluindo suas próprias visualizações no relatório e fixando-a no painel. 

![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 
**Excluindo o painel**: Você também pode compartilhar o painel com os usuários da organização. Após compartilhar o relatório, os usuários poderão ver os campos que você selecionou no relatório.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Agendar uma atualização diária do relatório do Power BI

Para agendar uma atualização diária do relatório do Power BI, vá para **Conjuntos de dados** > **Configurações** > **Programar atualização** e defina, conforme mostrado abaixo.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Atualizar para a versão mais recente do pacote de conteúdo

Se você quiser atualizar o pacote de conteúdo para uma versão mais recente:

- Baixe o novo pacote de conteúdo e configure-o usando as instruções deste artigo.

- Após configurá-lo, acesse **Fonte de Dados** > **Configurações** > **Credenciais da fonte de dados** e insira novamente as credenciais.

    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Após verificar se a nova versão do pacote de conteúdo funciona conforme o esperado, você poderá remover a versão antiga, se necessário, excluindo os relatórios e os conjuntos de dados subjacentes associados a esse pacote de conteúdo.

## <a name="troubleshoot-content-pack-errors"></a>Solucionar problemas com erros do pacote de conteúdo

Ao trabalhar com o pacote de conteúdo, é possível encontrar os seguintes erros: 

- [Falha na atualização](#refresh-failed) 
- [Falha ao atualizar as credenciais de fonte de dados](#failed-to-update-data-source-credentials) 
- [Importação de dados muito demorada](#data-import-is-too-slow) 

Para obter ajuda geral com o Power BI, confira esses [artigos de ajuda](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>Falha na atualização 
 
**Como esse erro ocorre**: Email do Power BI ou status com falha no histórico de atualizações. 


| Causa | Como corrigir |
| ---   | ---        |
| Erros de falha na atualização podem ser causados quando as credenciais dos usuários que se conectam ao pacote de conteúdo foram redefinidas, mas não atualizadas nas configurações de conexão do pacote de conteúdo. | No Power BI, localize o conjunto de dados correspondente ao painel de logs de atividades do Azure AD (**Logs de atividades do Azure Active Directory**), escolha programar atualização e insira as credenciais do Azure AD. |
| Uma atualização poderá falhar devido a problemas de dados no pacote de conteúdo subjacente. | [Registre um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais de fonte de dados 
 
**Como esse erro ocorre**: No Power BI ao conectar o pacote de conteúdo dos logs de atividades do Azure AD. 

| Causa | Como corrigir |
| ---   | ---        |
| O usuário de conexão não é um administrador global, um leitor de segurança ou um administrador de segurança. | Use uma conta que seja um administrador global, um leitor de segurança ou um administrador de segurança para acessar os pacotes de conteúdo. |
| Seu locatário não é um locatário Premium ou não tem, pelo menos, um usuário com um Arquivo de licença Premium. | [Registre um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>A importação de dados é muito lenta 
 
**Como esse erro ocorre**: No Power BI, após conectar o pacote de conteúdo, o processo de importação de dados começará a preparar o painel para os logs de atividades do Azure AD. Você verá a mensagem: **Importando dados...** sem qualquer outro andamento.  

| Causa | Como corrigir |
| ---   | ---        |
| Dependendo do tamanho do locatário, essa etapa poderá demorar de alguns minutos a 30 minutos. | Se a mensagem não for alterada para mostrar o painel dentro de uma hora, [registre um ticket de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Próximas etapas

* [Instalar pacote de conteúdo do Power BI](quickstart-install-power-bi-content-pack.md).
* [O que são relatórios do Azure AD?](overview-reports.md).
