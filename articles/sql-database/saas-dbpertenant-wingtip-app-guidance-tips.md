---
title: "Diretrizes para o exemplo de aplicativo de multilocatário do Banco de Dados SQL - Wingtip SaaS | Microsoft Docs"
description: "Fornece orientações e etapas para instalar e executar o aplicativo de multilocatário de exemplo que usa o Banco de Dados SQL do Azure, o exemplo Wingtip SaaS."
keywords: tutorial do banco de dados SQL
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: genemi
ms.openlocfilehash: fbfaea938676991cf6280e5dd8c1e1190aa268a8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Diretrizes e dicas para o exemplo de aplicativo SaaS de multilocatário do Banco de Dados SQL do Azure


## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Baixar e desbloquear os scripts SaaS do Wingtip

Conteúdos executáveis (scripts, dlls) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, ***siga as etapas abaixo para desbloquear o arquivo. zip antes da extração***. Isso garante que os scripts podem ser executados.

1. Navegue até [o repositório github SaaS do Wingtip](https://github.com/Microsoft/WingtipSaaS).
2. Clique em **Clonar ou baixar**.
3. Clique em **Baixar ZIP** e salve o arquivo.
4. Clique com botão direito no arquivo **WingtipSaaS master.zip** e selecione **Propriedades**.
5. Na guia **geral**, selecione **Desbloquear**.
6. Clique em **OK**.
7. Extraia os arquivos.

Os scripts estão localizados na pasta *... \\módulos de aprendizado\\WingtipSaaS mester*.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Trabalhando com os scripts do PowerShell do SaaS Wingtip

Para aproveitar ao máximo o exemplo, você precisa saber mais sobre os scripts fornecidos. Usar pontos de interrupção e percorrer os scripts, examinando os detalhes de como os diferentes padrões de SaaS são implementados. Para percorrer facilmente os módulos e scripts fornecidos para a melhor compreensão, é recomendável usar o [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualizar o arquivo de configuração para sua implantação

Editar o **UserConfig.psm1** arquivo com o valor de usuário e grupo de recursos que podem ser definidos durante a implantação:

1. Abra o *ISE do PowerShell* e carregue... \\UserConfig.psm1\\ do *Módulo de Aprendizado* 
2. Atualize *ResourceGroupName* e *Nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
3. Salve as alterações!

Definir esses valores aqui simplesmente impede que você precise atualizar esses valores específicos de implantação em cada script.

### <a name="execute-scripts-by-pressing-f5"></a>Executar Scripts pressionando F5

Vários scripts usam *$PSScriptRoot* para permitir a navegação em pastas e *$PSScriptRoot* é avaliada somente quando o script é executado, ao pressionar **F5**.  Realçar e executar uma seleção (**F8**) pode resultar em erros, então pressione **F5** ao executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Percorrer os scripts para examinar a implementação

É a melhor maneira de entender os scripts, percorrendo-los para ver o que fazem. Confira os scripts para **demonstração** incluídos que apresentam uma forma fácil de seguir o fluxo de trabalho de alto nível. Os script para **demonstração** mostram as etapas necessárias para executar cada tarefa, portanto, defina pontos de interrupção e analise mais detalhadamente em chamadas individuais para ver os detalhes de implementação para os diferentes padrões de SaaS.

Dicas para explorar e depurar scripts do PowerShell:

- Abra os scripts para **demonstração** no ISE do PowerShell.
- Execute ou continue com **F5** (o uso de **F8** não é recomendável porque o *$PSScriptRoot* não é avaliado durante a execução de seleções de um script).
- Coloque pontos de interrupção, clicando ou selecionando uma linha e pressionando **F9**.
- Passe por uma função ou chamada de script usando **F10**.
- Intervenha em uma função ou chamada de script usando **F11**.
- Saia da função atual ou da chamada de script usando **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de banco de dados e executar consultas SQL usando o SSMS

Use o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se conectar e procurar os servidores e bancos de dados do aplicativo.

A implantação inicialmente tem dois servidores de Banco de Dados SQL aos quais se conectar – o servidor *tenants1-&lt;User&gt;* e o servidor *catalog-&lt;User&gt;*. Para garantir uma conexão bem-sucedida de demonstração, ambos os servidores têm um [regra de firewall](sql-database-firewall-configure.md) permitindo todos os IPs.


1. Abra o *SSMS* e se conecte ao servidor *tenants1-&lt;User&gt;.database.windows.net*.
2. Clique em **Conectar** > **Mecanismo de Banco de Dados...**:

   ![servidor catalog](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. As credenciais de demonstração são: logon = *developer*, senha = *P@ssword1*

   ![connection](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. Repita as etapas 2 a 3 e conecte-se ao servidor *catalog-&lt;User&gt;.database.windows.net*.


Após se conectar com êxito, você deverá ver ambos os servidores. Sua lista de bancos de dados pode ser diferente, dependendo dos locatários provisionados.

![pesquisador de objetos](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Próximas etapas

[Implantar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)

