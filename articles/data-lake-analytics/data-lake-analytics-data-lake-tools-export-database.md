---
title: Como exportar bancos de dados U-SQL usando as Ferramentas do Azure Data Lake para Visual Studio | Microsoft Docs
description: "Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para exportar o banco de dados U-SQL e importá-lo a uma conta local ao mesmo tempo."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>Como exportar o banco de dados U-SQL

Neste documento, você saberá como usar as [Ferramentas do Azure Data Lake para Visual Studio](http://aka.ms/adltoolsvs) para exportar o banco de dados U-SQL como um script U-SQL único e recursos baixados. Também há o suporte para importar o banco de dados exportado para a uma conta local no mesmo processo.

Geralmente os clientes mantêm vários ambientes para desenvolvimento, teste e produção. Esses ambientes são hospedados em uma conta local do computador local dos desenvolvedores e em uma conta do Azure Data Lake Analytics no Azure. Ao desenvolver e ajustar consultas U-SQL em ambientes de desenvolvimento e teste, é comum que os desenvolvedores precisem recriar tudo no banco de dados da produção. O **Assistente para Exportação de Banco de Dados** ajuda a acelerar esse processo. Ao usar o assistente, os desenvolvedores podem clonar o ambiente existente de banco de dados e os dados de exemplo para outras contas do Azure Data Lake Analytics.

## <a name="export-steps"></a>Etapas de exportação

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>Etapa 1: clique com botão direito do mouse no banco de dados no Gerenciador de Servidores clique em "Exportar..."

Todas as contas do Azure Data Lake Analytics às quais você tem permissão estão listadas no Gerenciador de Servidores. Expanda a que contém o banco de dados que deseja exportar e clique com o botão direito do mouse no banco de dados para selecionar **Exportar...**. Caso não encontre o menu de contexto, é preciso a [atualizar a ferramenta para a versão mais recente](http://aka.ms/adltoolsvs).

![Banco de dados de exportação das ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>Etapa 2: configure os objetos que deseja exportar

Algumas vezes, há um banco de dados grande, mas você precisa apenas de uma pequena parte dele, então você pode configurar o subconjunto de objetos que deseja exportar no assistente de exportação. Observe que a ação de exportação é concluída por meio da execução de um trabalho de U-SQL e, portanto, exportar a partir da conta do Azure incorre em alguns custos.

![Assistente do banco de dados de exportação das ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>Etapa 3: verifique a lista de objetos e mais configurações

Nessa etapa, você pode verificar duas vezes os objetos selecionados na parte superior da caixa de diálogo. Se houver alguns erros, é possível clicar em Anterior para voltar e configurar os objetos que deseja exportar novamente.

Você também pode fazer outras configurações do destino de exportação. As descrições dessas configurações estão listadas na tabela abaixo:

|Configuração|Descrição|
|-------------|-----------|
|Nome do destino|Esse nome indica onde você deseja salvar os recursos exportados do banco de dados, como assemblies, arquivos adicionais e dados de exemplo. Uma pasta com esse nome será criada na sua pasta raiz local de dados.|
|Diretório do projeto|Esse caminho define onde você deseja salvar o script U-SQL exportado, o que inclui todas as definições de objeto de banco de dados.|
|Somente esquema|Selecionar essa opção resulta na exportação de apenas definições e recursos de banco de dados (como assemblies e arquivos adicionais).|
|Esquema e dados|Selecionar essa opção resulta na exportação de definições, recursos e dados do banco de dados. As linhas N superiores das tabelas são exportadas.|
|Importar automaticamente para o banco de dados local|Marcar essa configuração significa que o banco de dados exportado será importado para o seu banco de dados local automaticamente depois que a exportação for concluída.|

![Configuração do assistente do banco de dados de exportação das ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Etapa 4: verifique os resultados da exportação

Após todas essas configurações e o progresso de exportação, é possível encontrar os resultados exportados da janela de log no assistente. Por meio do log marcado por um retângulo vermelho na captura de tela abaixo, é possível encontrar o local do script U-SQL exportado e dos recursos do banco de dados, incluindo assemblies, arquivos adicionais e dados de exemplo.

![Assistente do banco de dados de exportação das ferramentas do Data Lake Analytics concluído](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>Como importar o banco de dados exportado para a conta local

A maneira mais conveniente de fazer essa importação é marcando a opção **Importar automaticamente para o banco de dados local** durante o andamento da exportação na Etapa 3. Caso tenha esquecido de fazer isso, é possível encontrar o script U-SQL exportado por meio do log de exportação e executar o script U-SQL localmente para importar o banco de dados para a sua conta local.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Como importar o banco de dados exportado para a conta do Azure Data Lake Analytics

Para importar o banco de dados para outra conta do Azure Data Lake Analytics, é preciso de duas etapas:

1. Carregue os recursos exportados, incluindo assemblies, arquivos adicionais e dados de exemplo na conta padrão Azure Data Lake Store da conta do Azure Data Lake Analytics à qual você deseja importar. É possível encontrar a pasta de recursos exportada na pasta raiz local de dados e carregar a pasta inteira para a raiz da conta de armazenamento padrão.
2. Envie o script U-SQL exportado para a conta do Azure Data Lake Analytics à qual deseja importar o banco de dados após a conclusão do carregamento.

## <a name="known-limitation"></a>Limitações conhecidas

Atualmente, se você tiver selecionado **Esquema e dados** no assistente, a ferramenta executa um trabalho de U-SQL para exportar os dados armazenados em tabelas. É por isso que o processo de exportação de dados pode ser lento e incorrer em custos. 

## <a name="next-steps"></a>Próximas etapas

* [Entender o banco de dados U-SQL](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Como testar e depurar trabalhos do U-SQL usando a execução local e o SDK para U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


