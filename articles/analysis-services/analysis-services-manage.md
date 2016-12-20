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
ms.date: 10/31/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 55a016a0943885a3aaa636316808939777afb0f8


---
# <a name="manage-analysis-services"></a>Gerenciar o Analysis Services
Depois de criar um servidor do Analysis Services no Azure, talvez seja necessário executar algumas tarefas de administração e gerenciamento imediatamente ou em algum momento no futuro. Por exemplo, executar o processamento nos dados atualizados, controlar quem pode acessar os modelos em seu servidor ou monitorar a integridade do servidor. Algumas tarefas de gerenciamento só podem ser executadas no Portal do Azure, outras no SQL Server Management Studio (SSMS) e algumas tarefas podem ser executadas em ambos.

## <a name="azure-portal"></a>Portal do Azure
O [Portal do Azure](http://portal.azure.com/) é onde você pode criar e excluir servidores, monitorar os recursos do servidor, alterar o tamanho e gerenciar quem tem acesso aos seus servidores.  Se você estiver enfrentando problemas, também poderá enviar uma solicitação de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Conectar-se ao seu servidor no Azure é como se conectar a uma instância de servidor em sua própria organização. No SSMS, você pode executar algumas tarefas como processar dados ou criar um script de processamento, gerenciar funções e usar o PowerShell.

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
   
    Observação: se você não vir a Autenticação do Active Directory, talvez seja necessário [habilitar a autenticação do Azure Active Directory](#enable-azure-active-directory-authentication) no SSMS.
   
    ![Conectar-se no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Uma vez que o gerenciamento de seu servidor no Azure usando o SSMS é praticamente igual ao gerenciamento de um servidor local, não vamos entrar em detalhes aqui. Toda a Ajuda de que você precisa pode ser encontrada em [Gerenciamento de Instância do Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) no MSDN.

## <a name="server-administrators"></a>Administradores de servidor
Você pode usar os **Administradores do Analysis Services** na folha de controle de seu servidor no Portal do Azure ou no SSMS para gerenciar os administradores de servidor. Os Administradores do Analysis Services são os administradores de servidor de banco de dados com direitos para execução de tarefas comuns de administração de banco de dados, como adicionar e remover bancos de dados e gerenciar usuários. Por padrão, o usuário que cria o servidor no Portal do Azure é adicionado automaticamente como Administrador do Analysis Services.

Você também deve saber:

* O Windows Live ID não é um tipo de identidade com suporte para os Azure Analysis Services.  
* Os Administradores do Analysis Services deve ser usuários válidos do Azure Active Directory.
* Se você criar um servidor do Azure Analysis Services por meio de modelos do Azure Resource Manager, os Administradores do Analysis Services usarão uma matriz JSON de usuários que devem ser adicionados como administradores.

Os Administradores do Analysis Services podem ser diferentes dos administradores de recursos do Azure, que podem gerenciar os recursos de assinaturas do Azure. Isso mantém a compatibilidade com o comportamentos de gerenciamento existente de XMLA e TSML no Analysis Services e serve para que você possa separar as tarefas entre o gerenciamento de recursos do Azure e o gerenciamento de banco de dados do Analysis Services.

Para exibir todas as funções e tipos de acesso para o recurso do Azure Analysis Services, use o IAM (Controle de acesso) na folha de controle.

## <a name="database-users"></a>Usuários de banco de dados
Os usuários de banco de dados de modelo do Azure Analysis Services devem estar em seu Azure Active Directory. Os nomes de usuário especificados para o banco de dados de modelo devem organizados pelo endereço de email organizacional ou UPN. Isso é diferente dos bancos de dados de modelo locais que oferecem suporte a usuários de acordo com os nomes de usuário de domínio do Windows.

Você pode adicionar usuários usando [atribuições de função no Azure Active Directory](../active-directory/role-based-access-control-configure.md) ou usando TMSL ([Linguagem de Script de Modelo de Tabela](https://msdn.microsoft.com/library/mt614797.aspx)) no SQL Server Management Studio.

**Exemplo de script TMSL**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Habilitar a autenticação do Azure Active Directory
Para habilitar o recurso de autenticação do Azure Active Directory para SSMS no Registro, crie um arquivo de texto chamado EnableAAD.reg e, em seguida, copie e cole o seguinte:

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Salve e execute o arquivo.

## <a name="troubleshooting-connection-problems"></a>Solucionar problemas de conexão
Ao se conectar ao servidor usando o SSMS, se (na etapa 3) você tentar entrar usando uma conta não federada ou uma conta não no Azure Active Directory e não puder se conectar, talvez seja necessário limpar o cache de logon. Feche o SSMS antes de seguir estas etapas.

1. No Explorador de arquivos, navegue até `C:\Users\<user_name>\AppData\Local\`.
2. Excluir a pasta **AADCacheOM**.
3. Pesquise a pasta **Local** por arquivos .dat começando com o nome **omlibs-tokens-cache.** Se você encontrar algum, exclua-o.
4. Abra o SSMS e repita as etapas em [Conectar-se com SSMS](#to-connect-with-ssms) acima.

## <a name="next-steps"></a>Próximas etapas
Se você ainda não tiver implantado um modelo de tabela em seu novo servidor, agora é um bom momento. Para saber mais, confira [Implantar no Azure Analysis Services](analysis-services-deploy.md).

Se você tiver implantado um modelo de tabela para seu servidor, você estará pronto para se conectar usando um cliente ou navegador. Para saber mais, confira [Obter dados do servidor do Azure Analysis Services](analysis-services-connect.md).




<!--HONumber=Nov16_HO3-->


