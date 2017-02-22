---
title: Gerenciar o Azure Analysis Services | Microsoft Docs
description: Saiba como gerenciar um servidor do Analysis Services no Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 13eb8ab1bf3c218f14b4c23ca1a46e9552d55b25
ms.openlocfilehash: a5db6cccf6c3dc55ee2cda59cb9e2ecd2292fcb5


---
# <a name="manage-analysis-services"></a>Gerenciar o Analysis Services
Depois de criar um servidor do Analysis Services no Azure, talvez seja necessário executar algumas tarefas de administração e gerenciamento imediatamente ou em algum momento no futuro. Por exemplo, executar o processamento nos dados atualizados, controlar quem pode acessar os modelos em seu servidor ou monitorar a integridade do servidor. Algumas tarefas de gerenciamento só podem ser executadas no Portal do Azure, outras no SQL Server Management Studio (SSMS) e algumas tarefas podem ser executadas em ambos.

## <a name="azure-portal"></a>Portal do Azure
O [Portal do Azure](http://portal.azure.com/) é onde você pode criar e excluir servidores, monitorar os recursos do servidor, alterar o tamanho e gerenciar quem tem acesso aos seus servidores.  Se você estiver enfrentando problemas, também poderá enviar uma solicitação de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Conectar-se ao seu servidor no Azure é como se conectar a uma instância de servidor em sua própria organização. No SSMS, você pode executar algumas tarefas como processar dados ou criar um script de processamento, gerenciar funções e usar o PowerShell. [Baixe e instale a versão mais recente do SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Uma das maiores diferenças é a autenticação usada para se conectar ao seu servidor. Para se conectar ao servidor do Azure Analysis Services, você precisa selecionar **Autenticação de Senha do Active Directory**.

 Ao usar o SSMS, antes de se conectar ao servidor na primeira vez, verifique se que seu nome de usuário está incluído no grupo de administradores do Analysis Services. Para obter mais informações, consulte [Administradores de servidor](#server-administrators) posteriormente neste artigo.

### <a name="to-connect-with-ssms"></a>Para conectar-se com o SSMS
1. Antes de se conectar, você precisa obter o nome do servidor. No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSMS > **Pesquisador de Objetos**, clique em **Conectar** > **Analysis Services**.
3. Na caixa de diálogo **Conectar ao Servidor**, cole o nome do servidor e, em **Autenticação**, escolha uma das seguintes opções:
   
    **Autenticação Integrada do Active Directory** para usar o logon único com o Active Directory para federação do Azure Active Directory.
   
    **Autenticação de Senha do Active Directory** para usar uma conta organizacional. Por exemplo, ao conectar-se de um computador que não ingressou em um domínio.
   
    Observação: se você não vir a autenticação do Active Directory, será necessário atualizar para a [versão mais recente do SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).
   
    ![Conectar-se no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Uma vez que o gerenciamento de seu servidor no Azure usando o SSMS é praticamente igual ao gerenciamento de um servidor local, não vamos entrar em detalhes aqui. Toda a Ajuda de que você precisa pode ser encontrada em [Gerenciamento de Instância do Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) no MSDN.

## <a name="server-administrators-and-database-users"></a>Administradores de servidor e usuários de banco de dados
Nos Serviços de Análise do Azure, há dois tipos de usuários, administradores de servidor e usuários de banco de dados. Os dois tipos de usuários devem estar no Azure Active Directory e devem ser especificados por endereços de email organizacionais ou UPN. Isso é diferente dos bancos de dados de modelo tabular locais que oferecem suporte a usuários de banco de dados e administradores de servidor pelos nomes de usuário de domínio do Windows. Para saber mais, confira [Gerenciar usuários no Azure Analysis Services](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Solucionar problemas de conexão
Ao se conectar ao servidor usando o SSMS, se (na etapa 3) você tentar entrar usando uma conta não federada ou uma conta não no Azure Active Directory e não puder se conectar, talvez seja necessário limpar o cache de logon. Feche o SSMS antes de seguir estas etapas.

1. No Explorador de arquivos, navegue até `C:\Users\<user_name>\AppData\Local\`.
2. Excluir a pasta **AADCacheOM**.
3. Pesquise a pasta **Local** por arquivos .dat começando com o nome **omlibs-tokens-cache.** Se você encontrar algum, exclua-o.
4. Abra o SSMS e repita as etapas em [Conectar-se com SSMS](#to-connect-with-ssms) acima.

## <a name="next-steps"></a>Próximas etapas
Se você ainda não tiver implantado um modelo de tabela em seu novo servidor, agora é um bom momento. Para saber mais, confira [Implantar no Azure Analysis Services](analysis-services-deploy.md).

Se você tiver implantado um modelo de tabela para seu servidor, você estará pronto para se conectar usando um cliente ou navegador. Para saber mais, confira [Obter dados do servidor do Azure Analysis Services](analysis-services-connect.md).




<!--HONumber=Feb17_HO2-->


