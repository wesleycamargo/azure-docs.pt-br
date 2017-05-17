---
title: Importar e exportar no Banco de Dados do Azure para MySQL | Microsoft Docs
description: "Fornece uma introdução à importação e exportação de bancos de dados no Banco de Dados do Azure para MySQL"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>Migrar seu banco de dados MySQL usando importação e exportação
Este tutorial mostra a maneira mais comum para importar e exportar os dados no banco de dados MySQL do Azure usando MySQL Workbench. 

## <a name="before-you-begin"></a>Antes de começar
Para seguir este guia de instruções, você precisa:
- Um Banco de Dados do Azure para o servidor MySQL [Criar seu primeiro banco de dados MySQL do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench [Baixar MySQL Workbench](https://dev.mysql.com/downloads/workbench/)

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use ferramentas comuns, como MySQL Workbench, Toad ou Navicat para se conectar remotamente e importar ou exportar dados para o Banco de Dados do Azure para MySQL. Use essas ferramentas no computador cliente com uma conexão de Internet para se conectar ao Banco de Dados do Azure para MySQL. Use uma conexão SSL criptografada para práticas recomendadas de segurança, consulte também [Configurar conectividade SSL no Banco de Dados do Azure para MySQL](concepts-ssl-connection-security.md). Você não precisa mover os seus arquivos de importação e exportação para nenhum local específico na nuvem ao migrar para o Banco de Dados do Azure para MySQL. 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>Criar um banco de dados no Banco de Dados do Azure de destino para o serviço MySQL
Você deve criar um banco de dados vazio no Banco de Dados do Azure de destino para o servidor MySQL para o qual você deseja migrar os dados usando o MySQL Workbench, Toad, Navicat ou qualquer ferramenta de terceiros para MySQL. O banco de dados pode ter o mesmo nome que o banco de dados que contém os dados de despejo ou você pode criar um banco de dados com um nome diferente.

![Banco de Dados do Azure para Cadeia de Conexão do MySQL](./media/concepts-migrate-import-export/p5.png)

![Cadeia de Conexão do MySQL Workbench](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>Importar e Exportar usando o MySQL Workbench
Existem duas maneiras de exportar e importar dados no MySQL Workbench, cada uma atendendo a uma finalidade diferente. 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>Assistente de Importação e Exportação de Dados de Tabela usando o menu de contexto do Pesquisador de Objetos
![Importação/Exportação do MySQL Workbench usando o menu de contexto do Pesquisador de Objetos](./media/concepts-migrate-import-export/p1.png)

Esse assistente oferece suporte a operações de importação e exportação usando arquivos CSV e JSON e inclui várias opções de configuração (separadores, seleção de coluna, seleção de codificação e muito mais). O assistente pode ser executado em servidores MySQL locais ou conectados remotamente e a ação de importação inclui o mapeamento da tabela, da coluna e do tipo. O assistente está acessível no menu de contexto do pesquisador de objetos clicando-se com o botão direito do mouse em uma tabela e escolhendo o **Assistente de Exportação de Dados de Tabela** ou o **Assistente de Importação de Dados de Tabela**. 

## <a name="table-data-export-wizard"></a>Assistente de Exportação de Dados de Tabela
O exemplo a seguir exporta a tabela para um arquivo CSV. 
- Clique com botão direito do mouse na Tabela do Banco de Dados a ser exportada. 
- Selecione **Assistente de Exportação de Dados de Tabela**. Selecione as Colunas a serem exportadas, o Deslocamento da linha (se houver) e a Contagem (se houver). 
- Clique em **Avançar** na janela “Selecionar dados para exportação”. Selecione o Caminho do arquivo, o tipo de arquivo CSV ou JSON, o Separador de linha, Incluir cadeias de caracteres em e o Separador de campo. 
- Selecione **Avançar** na janela “Selecionar Local do arquivo de saída” e selecione Avançar na janela “Exportar dados”.


## <a name="table-data-import-wizard"></a>Assistente de Importação de Dados de Tabela
O exemplo a seguir importa a tabela de um arquivo CSV.
- Clique com botão direito do mouse na Tabela do Banco de Dados a ser importada. 
- Navegue e selecione o arquivo CSV a ser importado e em seguida clique no botão Avançar. 
- Selecione a Tabela de destino (nova ou existente) e marque ou desmarque a caixa de seleção “Truncar tabela antes da importação” e clique no botão Avançar.
- Selecione codificação e as colunas a serem importadas e selecione o botão Avançar. 
- Selecione Avançar na janela de Importação de dados e os dados serão devidamente importados.

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>Assistente de Exportação e Importação de Dados SQL do Navegador de Gerenciamento
Use este assistente para exportar ou importar o SQL gerado no MySQL Workbench ou com o comando mysqldump. Acesse esses assistentes a partir do painel do Navegador ou selecionando o Servidor no menu principal e, em seguida, Importação de Dados ou Exportação de Dados. 

## <a name="data-export"></a>Exportação de Dados
![Exportação de Dados do MySQL Workbench usando o Navegador de Gerenciamento](./media/concepts-migrate-import-export/p2.png)

Esta guia permite que você exporte os dados do MySQL. 
- Selecione cada esquema que você deseja exportar, se desejar escolha objetos e tabelas do esquema específicos de cada esquema e gere a exportação. As opções de configuração incluem a exportação para uma pasta de projeto ou arquivo SQL independente, opcionalmente, despejo de eventos e rotinas armazenados ou ignorar os dados da tabela. 
- Como alternativa, use **Exportar um conjunto de resultados** para exportar um conjunto de resultados específico no editor de SQL para outro formato como CSV, JSON, HTML e XML. 
- Selecione os objetos do banco de dados para exportação e configure as opções relacionadas. 
- Clique em **Atualizar** para carregar os objetos atuais. 
- Opcionalmente, abra a guia Opções avançadas que permite que você refine a operação de exportação. Por exemplo, adicionar bloqueios de tabela, use instruções de substituir em vez de inserir, citar identificadores com caracteres de acento grave e muito mais. 
- Clique em **Iniciar exportação** para iniciar o processo de exportação. 


## <a name="data-import"></a>Importação de Dados
![Importação de Dados do MySQL Workbench usando o Navegador de Gerenciamento](./media/concepts-migrate-import-export/p3.png)

Esta guia permite importar ou restaurar os dados exportados da operação de Exportação de dados ou de outros dados exportados a partir do comando mysqldump. 
- Escolha a pasta de projeto ou arquivo SQL independente, escolha o esquema para o qual os dados serão importados, ou escolha Novo para definir um novo esquema. 
- Clique em **Iniciar importação** para iniciar o processo de importação conforme a configuração.

## <a name="next-steps"></a>Próximas etapas
Se você não estiver familiarizado com o guia de introdução deste serviço de banco de dados, consulte:
-  [Criar um Banco de Dados do Azure para servidor MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md) 
- [Criar um Banco de Dados do Azure para servidor MySQL usando a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

