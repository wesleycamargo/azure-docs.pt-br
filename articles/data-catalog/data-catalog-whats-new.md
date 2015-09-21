<properties
   pageTitle="O que há de novo no Catálogo de Dados do Azure"
   description="Visão geral dos novos recursos na visualização do Catálogo de Dados do Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/04/2015"
   ms.author="maroche"/>

# O que há de novo no Catálogo de Dados do Azure

As atualizações para o **Catálogo de Dados do Azure** são lançadas periodicamente. Nem todas as versões incluem novos recursos voltados para o usuário, já que algumas versões se concentram em recursos de serviço de back-end. Esta página destacará os novos recursos voltados para o usuário adicionados ao serviço do **Catálogo de Dados do Azure**.

## Novidades da versão da semana de 4 de setembro de 2015

A partir de 4 de setembro de 2015, os seguintes recursos foram adicionados ao **Catálogo de Dados do Azure**:

- Suporte ao registro manual de tipos desconhecidos de fonte de dados. Os usuários podem inserir manualmente informações de fonte de dados usando o portal do **Catálogo de Dados do Azure**, de modo que as fontes de dados explicitamente sem suporte da ferramenta de registro de fonte de dados não podem ser anotadas nem descobertas.
- Suporte ao registro e à descoberta de bancos de dados do SQL Server como contêineres. Ao registrar tabelas e exibições do SQL Server, o **Catálogo de Dados do Azure** cria uma entrada para o banco de dados, bem como para as tabelas e exibições. O banco de dados pode ser descoberto e anotado usando o portal do **Catálogo de Dados do Azure**. Os usuários também podem pesquisar e filtrar o conteúdo de um banco de dados, além de pesquisar e filtrar o conteúdo do catálogo.


> [AZURE.NOTE]As tabelas e exibições do SQL Server que foram registradas antes da versão de 04/09/2015 devem ser registradas novamente usando a ferramenta de registro de fonte de dados antes que a entrada do banco de dados seja adicionada ao catálogo. Registrar novamente uma fonte de dados não afetará nenhuma anotação que tenha sido adicionada pelos usuários no portal do **Catálogo de Dados do Azure**.

## Novidades da versão da semana de 28 de agosto de 2015

A partir de 28 de agosto de 2015, os seguintes recursos foram adicionados ao **Catálogo de Dados do Azure**:

- Suporte para criação de perfil de dados de fontes de dados do SQL Server e Oracle. Ao registrar tabelas e exibições do SQL Server e Oracle, os usuários podem optar por incluir informações de perfil de dados para os objetos que estão sendo registrados. O perfil de dados inclui estatísticas no nível de objeto e no nível de coluna.
- Suporte para fontes de dados HDFS do Hadoop. Agora, os usuários podem registrar e descobrir os diretórios e arquivos do HDFS.

## Novidades da versão da semana de 21 de agosto de 2015

A partir de 21 de agosto de 2015, os seguintes recursos foram adicionados ao **Catálogo de Dados do Azure**:

- Suporte para fornecer informações de solicitação de acesso de fontes de dados registradas. Para qualquer ativo de dados registrado, os usuários agora podem fornecer instruções para solicitação de acesso, incluindo links de email ou URLs, a fim de integrar facilmente com ferramentas e processos existentes.
- Dicas de ferramenta para marcas e especialistas, para facilitar a descoberta de quais usuários forneceram quais metadados para os ativos de dados registrados.
- Adicionamos um novo botão "Usuário" e o menu à nossa barra de navegação superior. Esse menu permite que o usuário veja a conta usada para fazer logon no **Catálogo de Dados do Azure** e para sair, se ele desejar. Esse menu também exibe o nome do catálogo, que é importante para os desenvolvedores que usam a API REST do **Catálogo de Dados do Azure**.
- Somente Standard Edition: ao adicionar proprietários aos ativos de dados, o **Catálogo de Dados do Azure** agora oferece suporte a contas de usuário e grupos de segurança como proprietários. Para adicionar um grupo de segurança como um proprietário de ativos de dados selecionados, você pode inserir o nome para exibição do grupo ou o endereço de email do UPN do grupo, se houver.
- Suporte para fontes de dados do Armazenamento do Blobs do Azure. Agora, os usuários podem registrar e descobrir os blobs e diretórios de armazenamento do Azure.

<!---HONumber=Sept15_HO2-->