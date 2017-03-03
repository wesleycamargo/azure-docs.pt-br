---
title: Obter dados do Azure Analysis Services | Microsoft Docs
description: Saiba como se conectar e obter dados de um servidor do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/13/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: 118ea84abb0de095ac515ee98a643718ca54c043
ms.lasthandoff: 02/16/2017


---
# <a name="get-data-from-azure-analysis-services"></a>Obter dados do Azure Analysis Services

Depois de criar um servidor no Azure e implantar um modelo tabular nele, os usuários em sua organização estarão prontos para se conectar e começar a explorar os dados.

## <a name="data-providers-aka-client-libraries"></a>Provedores de dados (também conhecido como bibliotecas de cliente)

Aplicativos cliente, como o Power BI Desktop e o Microsoft Excel usam provedores AMO, ADOMD.NET e OLEDB para se conectar e criar a interface com o Analysis Services. Com algumas versões mais antigas do Excel ou aplicativos personalizados, a instalação dos provedores de dados mais recentes pode ser necessária para se conectar ao Azure Analysis Services. Para obter mais informações, consulte [Provedores de dados](analysis-services-data-providers.md).

## <a name="server-name"></a>Nome do servidor

Quando você cria um servidor do Azure Analysis Services, é necessário especificar um nome exclusivo e a região na qual o servidor será criado. Ao especificar o nome do servidor em uma conexão, o esquema de nomenclatura do servidor será:

```
<protocol>://<region>/<servername>
```
 Quando o protocolo for a cadeia de caracteres **asazure**, a região será o Uri da região na qual o servidor foi criado (por exemplo, para Oeste dos EUA, westus.asazure.windows.net), e servername será o nome do seu servidor exclusivo dentro da região.

## <a name="get-the-server-name"></a>Obter o nome do servidor

Antes de se conectar, você precisa obter o nome do servidor. No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie todo o nome do servidor. Se outros usuários em sua organização também estiverem se conectando a este servidor, convém compartilhar este nome de servidor com eles. Ao especificar um nome de servidor, todo o caminho deve ser usado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Conexão no Power BI Desktop

> [!NOTE]
> Esse recurso está em fase de Visualização.
> 
> 

1. No [Power BI Desktop](https://powerbi.microsoft.com/desktop/), clique em **Obter Dados** > **Bancos de Dados** > **Azure Analysis Services**.
2. Em **Servidor**, cole o nome do servidor a partir da área de transferência.
3. Em **Banco de Dados**, se você souber o nome do banco de dados de modelo de tabela ou da perspectiva a qual você deseja se conectar, cole-o aqui. Caso contrário, deixe esse campo em branco. Você pode selecionar um banco de dados ou uma perspectiva na próxima tela.
4. Deixe a opção padrão **Conexão ativa** selecionada e pressione **Conectar**. Se você receber uma solicitação para inserir uma conta, digite sua conta organizacional.
5. Em **Navegador**, expanda o servidor e selecione o modelo ou a perspectiva que você deseja se conectar e clique em **Conectar**. Um único clique em um modelo ou perspectiva mostra todos os objetos dessa exibição.

## <a name="connect-in-power-bi"></a>Conectar-se no Power BI

1. Crie um arquivo do Power BI Desktop que tenha uma conexão ativa com seu modelo no servidor.
2. No [Power BI](https://powerbi.microsoft.com), clique em **Obter Dados** > **Arquivos**. Localize e selecione o arquivo.

## <a name="connect-in-excel"></a>Conectar-se no Excel

A conexão com o Azure Analysis Services no Excel tem suporte pelo uso de Obter Dados no Excel 2016 ou no Power Query em versões anteriores. O [provedor MSOLAP.7](analysis-services-data-providers.md) é necessário. Não há suporte para a conexão usando o Assistente para Importar tabela no Power Pivot.

> [!NOTE]
> Algumas organizações implantam atualizações do Office 365 no Canal Adiadas; isso significa que as atualizações de versão são adiadas em até quatro meses em relação à versão atual. Para a versão do Excel 2016 build 1609.7369.2115 e anteriores ou Excel 2013, você pode criar um arquivo de conexão de dados do Office (.odc) e atualizar manualmente o provedor MSOLAP.7 para se conectar a um servidor do Azure Analysis Services. Para saber mais, consulte [criar um arquivo .odc](analysis-services-odc.md).
> 
> 

**Para conectar-se do Excel 2016**

1. No Excel 2016, na faixa de opções **Dados**, clique em **Obter Dados Externos** > **De Outras Fontes** > **Do Analysis Services**.
2. No Assistente de Conexão de Dados, em **Nome do Servidor**, cole o nome do servidor a partir da área de transferência. Em seguida, em **Credenciais de logon**, selecione **Usar o seguinte Nome de Usuário e Senha** e digite o nome de usuário da organização, por exemplo nancy@adventureworks.com,, e a senha.

    ![Conectar-se no logon do Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. Em **Selecionar Banco de Dados e Tabela**, selecione o banco de dados e um modelo ou perspectiva e, em seguida, clique em **Concluir**.
   
    ![Conectar-se no modelo de seleção do Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Cadeia de conexão

Ao conectar-se ao Azure Analysis Services usando o Modelo de objeto de tabela, use os seguintes formatos de cadeia de conexão:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação integrada do Azure Active Directory

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
A autenticação integrada selecionará o cache de credencial do Azure Active Directory, se estiver disponível. Caso contrário, a janela de logon do Azure será exibida.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticação do Azure Active Directory com nome de usuário e senha

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="next-steps"></a>Próximas etapas

[Gerenciar seu serviço](analysis-services-manage.md)


