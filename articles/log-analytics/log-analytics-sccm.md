---
title: Conectar o Configuration Manager ao Log Analytics | Microsoft Docs
description: Este artigo mostra as etapas para conectar o Configuration Manager ao Log Analytics e começar a analisar dados.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 433914bc4501b13ba65015d15b0c513a38bf1273
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041655"
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Conectar o Configuration Manager ao Log Analytics
É possível conectar o ambiente do System Center Configuration Manager ao Azure Log Analytics para sincronizar dados de coleta de dispositivo e fazer referência a essas coleções no Log Analytics e na Automação do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

O Log Analytics dá suporte à ramificação atual do System Center Configuration Manager, versão 1606 e superior.  

## <a name="configuration-overview"></a>Visão geral de configuração
As etapas a seguir resumem as etapas para configurar a integração do Configuration Manager com o Log Analytics.  

1. No portal do Azure, registre o Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e certifique-se de ter a ID do cliente e a chave secreta do cliente do registro do Azure Active Directory. Consulte [Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md) para obter informações detalhadas sobre como executar essa etapa.
2. No Portal do Azure, [conceda permissão para o Configuration Manager (o aplicativo Web registrado) acessar o Log Analytics](#grant-configuration-manager-with-permissions-to-log-analytics).
3. No Configuration Manager, [adicione uma conexão usando o Assistente de Adição de Conexão do OMS](#add-an-oms-connection-to-configuration-manager).
4. No Configuration Manager, [atualize as propriedades de conexão](#update-oms-connection-properties) se a chave secreta do cliente ou a senha expirar ou for perdida.
5. [Baixe e instale o Microsoft Monitoring Agent](#download-and-install-the-agent) no computador que execute a função de sistema do site de ponto de conexão de serviço do Configuration Manager. O agente envia dados do Configuration Manager para o espaço de trabalho do Log Analytics.
6. No Log Analytics, [importe coleções do Configuration Manager](#import-collections) como grupos de computadores.
7. No Log Analytics, exiba dados do Configuration Manager como [grupos de computadores](log-analytics-computer-groups.md).

Você pode ler mais sobre a conexão do Configuration Manager ao OMS em [Sync data from Configuration Manager to the Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx) (Sincronizar dados do Configuration Manager com o Microsoft Operations Management Suite).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Conceder o Configuration Manager com permissões para o Log Analytics
No procedimento a seguir, você concede a função *Colaborador* no espaço de trabalho do Log Analytics ao aplicativo do AD e à entidade de serviço criada anteriormente para o Configuration Manager.  Se você ainda não tiver um espaço de trabalho, consulte [Criar um espaço de trabalho no Azure Log Analytics](log-analytics-quick-create-workspace.md) antes de continuar.  Isso permite que o Configuration Manager autentique e conecte-se ao espaço de trabalho do Log Analytics.  

> [!NOTE]
> É necessário especificar permissões no Log Analytics para o Configuration Manager. Caso contrário, você receberá uma mensagem de erro ao usar o assistente de configuração do Configuration Manager.
>

1. No Portal do Azure, clique em **Todos os serviços**, localizado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.<br><br> ![Portal do Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho para modificar.
3. No painel esquerdo, selecione**IAM (Controle de acesso)**.
4. Na página Controle de Acesso, clique em **Adicionar** e o painel **Adicionar permissões** é exibido.
5. No painel **Adicionar permissões** na lista suspensa **Função** selecione a função **Colaborador**.  
6. Na lista suspensa **Atribuir acesso a** selecione o aplicativo Configuration Manager criado no AD anteriormente e clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Baixe e instale o agente
Revise o artigo [Conectar computadores Windows ao serviço do Log Analytics no Azure](log-analytics-agent-windows.md) para reconhecer os métodos disponíveis para instalar o Microsoft Monitoring Agent no computador que hospeda a função de sistema do site de ponto de conexão de serviço do Configuration Manager.  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Adicionar uma conexão do OMS ao Configuration Manager
Para adicionar uma conexão do OMS, seu ambiente do Configuration Manager deve ter uma [ponto de conexão de serviço](https://technet.microsoft.com/library/mt627781.aspx) configurado para o modo online.

1. No espaço de trabalho **Administração** do Configuration Manager, selecione **Conector OMS**. Isso abre o **Assistente de Adição de Conexão do OMS**. Selecione **Avançar**.
2. Na tela **Geral**, confirme se você realizou as seguintes ações e se tem os detalhes de cada item, em seguida, selecione **Avançar**.

   1. No portal do Azure, você registrou o Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e tem a [ID do cliente do registro](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
   2. No portal do Azure, você criou uma chave secreta do aplicativo para o aplicativo registrado no Azure Active Directory.  
   3. No portal do Azure, você concedeu ao aplicativo Web registrado a permissão para acessar o OMS.  
      ![Conexão com a página geral do Assistente do OMS](./media/log-analytics-sccm/sccm-console-general01.png)
3. Na tela **Azure Active Directory**, configure suas definições de configuração para o Log Analytics fornecendo o **Locatário**, a **ID do Cliente** e a **Chave Secreta do Cliente** e selecione **Avançar**.  
   ![Conexão com a página do Azure Active Directory do Assistente do OMS](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Se você completar todos os outros procedimentos com êxito, as informações na tela **Configuração de Conexão do OMS** aparecerão automaticamente nessa página. As informações para as configurações de conexão devem aparecer para sua **Assinatura do Azure**, **Grupo de recursos do Azure** e **Espaço de Trabalho do Operations Management Suite**.  
   ![Conexão com a página de Conexão do OMS do Assistente do OMS](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. O assistente conecta ao serviço do Log Analytics usando as informações que você inseriu. Selecione as coleções de dispositivos que você deseja sincronizar com o serviço e clique em **Adicionar**.  
   ![Selecionar Coleções](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Verifique as configurações de conexão na tela **Resumo** e, em seguida, selecione **Avançar**. A tela **Progresso** mostra o status da conexão e deve **Concluir**.

> [!NOTE]
> Você deve conectar o site da camada superior de sua hierarquia ao Log Analytics. Se você conectar um site primário autônomo ao Log Analytics e, em seguida, adicionar um site de administração central ao seu ambiente, será necessário excluir e recriar a conexão na nova hierarquia.
>
>

Após ter vinculado o Configuration Manager ao Log Analytics, será possível adicionar ou remover coleções e exibir as propriedades da conexão.

## <a name="update-log-analytics-connection-properties"></a>Atualizar propriedades de conexão do Log Analytics
Se uma senha ou chave secreta do cliente expirar ou for perdida, será necessário atualizar manualmente as propriedades de conexão do Log Analytics.

1. No Configuration Manager, navegue até **Serviços de Nuvem**, selecione **Conector OMS** para abrir a página **OMS Connection Properties (Propriedades da Conexão do OMS)**.
2. Nessa página, clique na guia **Azure Active Directory** para exibir seu **Locatário**, **ID do Cliente**, **Client secret key expiration (Expiração da chave secreta do cliente)**. **Verifique se** sua **Chave secreta do cliente** caso ela tenha expirado.

## <a name="import-collections"></a>Importe coleções
Depois de adicionar uma conexão do OMS ao Configuration Manager e instalar o agente no computador executando a função de sistema do site de ponto de conexão de serviço do Configuration Manager, a próxima etapa é importar coleções do Configuration Manager no Log Analytics como grupos de computadores.

Após concluir a configuração inicial para importar coleções de dispositivos de sua hierarquia, as informações de associação de coleta serão recuperadas a cada 3 horas para manter a associação atual. É possível escolher desabilitar isso a qualquer momento.

1. No Portal do Azure, clique em **Todos os serviços**, localizado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. Na sua lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho no qual o Configuration Manager está registrado.  
3. Selecione **Configurações avançadas**.<br><br> ![Configurações avançadas do Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Grupos de Computadores** e, em seguida, selecione **SCCM**.  
5. Selecione **Importar associações de coleta do Configuration Manager** e clique em **Salvar**.  
   ![Grupos de computadores – guia SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Exibir dados do Configuration Manager
Depois de adicionar uma conexão do OMS ao Configuration Manager e instalar o agente no computador executando a função de sistema de site do ponto de conexão de serviço do Configuration Manager, os dados do agente são enviados para o Log Analytics. No Log Analytics, suas coleções do Configuration Manager aparecem como [grupos de computadores](log-analytics-computer-groups.md). Você pode exibir os grupos da página **Configuration Manager** em **Configurações\Grupos de Computadores**.

Depois que as coleções forem importadas, você poderá ver quantos computadores com associações de coleção foram detectados. Você também pode ver o número de coleções que foram importadas.

![Grupos de computadores – guia SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Quando você clica em qualquer um, Search abre, mostrando todos os grupos importados ou todos os computadores que pertencem a cada grupo. Usando a [Pesquisa de Log](log-analytics-log-searches.md), você pode iniciar uma análise detalhada de dados do Configuration Manager.

## <a name="next-steps"></a>Próximas etapas
* Use a [Pesquisa de Log](log-analytics-log-searches.md) para exibir informações detalhadas sobre os dados do Configuration Manager.
