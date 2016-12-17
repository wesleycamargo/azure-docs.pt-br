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
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 6b65689c75247af81e916730f125ff39bb49489f


---
# <a name="get-data-from-azure-analysis-services"></a>Obter dados do Azure Analysis Services
Depois de criar um servidor no Azure e implantar um modelo tabular nele, os usuários em sua organização estarão prontos para se conectar e começar a explorar os dados.

O Azure Analysis Services oferece suporte a conexões de cliente usando [modelos de objeto atualizados](#client-libraries); TOM, AMO, Adomd.Net ou MSOLAP, para se conectar por meio de xmla com o servidor. Por exemplo, Power BI, Power BI Desktop, Excel ou qualquer aplicativo cliente de terceiros que ofereça suporte aos modelos de objeto.

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
A conexão com o Azure Analysis Services no Excel tem suporte pelo uso de Obter Dados no Excel 2016 ou no Power Query em versões anteriores. O [provedor MSOLAP.7](https://aka.ms/msolap) é necessário. Não há suporte para a conexão usando o Assistente para Importar tabela no Power Pivot.

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

## <a name="client-libraries"></a>Bibliotecas de cliente
Ao conectar-se ao Azure Analysis Services do Excel ou de outras interfaces, como TOM, AsCmd, ADOMD.NET, talvez seja necessário instalar as bibliotecas de cliente mais recentes do provedor. Obtenha a versão mais recente:  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Próximas etapas
[Gerenciar seu serviço](analysis-services-manage.md)




<!--HONumber=Nov16_HO3-->


