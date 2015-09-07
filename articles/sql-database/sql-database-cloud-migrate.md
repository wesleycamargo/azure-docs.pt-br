<properties
   pageTitle="Migrando um banco de dados para o Banco de Dados SQL do Azure"
	description="Banco de Dados SQL do Microsoft Azure, implantação de banco de dados, migração de banco de dados, banco de dados de importação, exportação de banco de dados, assistente de migração"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="08/26/2015"
	ms.author="carlrab"/>

# Migrando um banco de dados para o Banco de Dados SQL do Azure

O Banco de Dados SQL V12 do Azure oferece compatibilidade de mecanismo quase completa com o SQL Server 2014 e posterior. Dessa forma, a tarefa de migrar a maioria dos bancos de dados de uma instância local do SQL Server 2005 ou superior para um banco de dados SQL do Azure está muito mais simples. A migração para vários bancos de dados é um esquema direto e uma operação de movimentação de dados que requer poucas (se houver) alterações no esquema e pouca ou nenhuma reengenharia de aplicativos. Onde os bancos de dados precisam ser alterados, o escopo dessas alterações é mais restrito.

Desde o início, os recursos do SQL Server no escopo do servidor não têm suporte do Banco de Dados SQL V12 do Azure. Os bancos de dados e aplicativos que dependem desses recursos precisarão de alguma reengenharia para que possam ser migrados. Embora o Banco de Dados SQL V12 do Azure melhore a compatibilidade com um banco de dados do SQL Server local, a migração ainda precisará ser planejada e executada cuidadosamente, especialmente para bancos de dados grandes e complexos.

## Determinando a compatibilidade
Para determinar se seu banco de dados do SQL Server local é compatível com o Banco de Dados SQL V12 do Azure, você pode iniciar a migração usando um dos dois métodos discutidos na opção 1 abaixo e verificar se as rotinas de validação do esquema detectam uma incompatibilidade, ou pode usar o SQL Server Data Tools no Visual Studio, conforme discutido na opção 2 abaixo, para validar a compatibilidade. Se o banco de dados do SQL Server local tiver problemas de compatibilidade, você poderá usar o SQL Server Data Tools no Visual Studio ou o SQL Server Management Studio para resolver os problemas de compatibilidade.

## Métodos de migração
Há vários métodos de migrar um banco de dados do SQL Server local compatível para o Banco de Dados SQL V12 do Azure.

- No caso de bancos de dados pequenos a médios, migrar bancos de dados do SQL Server 2005 ou posteriores compatíveis é tão simples quanto executar o assistente Implantar Banco de Dados no Banco de Dados do Microsoft Azure no SQL Server Management Studio, desde que você não tenha desafios de conectividade (nenhuma conectividade, baixa largura de banda ou problemas de tempo limite).
- No caso de bancos de dados médios a grandes, ou quando há desafios de conectividade, você pode usar o SQL Server Management Studio para exportar os dados e o esquema para um arquivo BACPAC (armazenado localmente ou em um blob do Azure) e, em seguida, importar o arquivo BACPAC para sua instância SQL do Azure. Se você armazenar o BACPAC em um blob do Azure, também será possível importar o arquivo BACPAC de dentro do portal do Azure.  
- Para bancos de dados maiores, você conseguirá o melhor desempenho migrando o esquema e os dados separadamente. Você pode extrair o esquema em um projeto de banco de dados usando o SQL Server Management Studio ou Visual Studio e, em seguida, implantar o esquema para criar o banco de dados SQL do Azure. Em seguida, você pode extrair os dados usando o BCP e depois usar o BCP para importar os dados usando fluxos paralelos no banco de dados SQL do Azure. Migrar um banco de dados grande e complexo levará muitas horas, independentemente do método escolhido.

### Opção 1:
******Migrando um banco de dados compatível usando o SQL Server Management Studio***

O SQL Server Management Studio fornece dois métodos para migrar seu banco de dados do SQL Server local compatível para um banco de dados SQL. É possível usar o assistente Implantar Banco de Dados no Banco de Dados SQL do Microsoft Azure ou exportar o banco de dados para um arquivo BACPAC, que pode então ser importado para criar um novo banco de dados SQL do Azure. O assistente valida a compatibilidade do Banco de Dados SQL V12 do Azure, extrai o esquema e os dados em um arquivo BACPAC e depois os importa na instância especificada do banco de dados SQL do Azure.

### Opção 2:
***Atualizar o esquema de banco de dados offline usando o Visual Studio e implantar com o SQL Server Management Studio***

Se seu banco de dados do SQL Server local não for compatível ou para determinar se ele é compatível, você pode importar o esquema de banco de dados em um projeto de banco de dados do Visual Studio para análise. Para analisar, especifique a plataforma de destino para o projeto como Banco de Dados SQL V12 e crie o projeto. Se a criação for bem-sucedida, o banco de dados é compatível. Se falhar, você poderá corrigir os erros no SQL Server Data Tools para Visual Studio ("SSDT"). Depois que o projeto for criado com êxito, você poderá publicá-lo como uma cópia do banco de dados de origem e usar o recurso de comparação de dados no SSDT para copiar os dados do banco de dados de origem no banco de dados compatível com o SQL V12 do Azure. Esse banco de dados atualizado é implantado para banco de dados SQL do Azure usando a opção 1. Se a migração somente de esquema for necessária, o esquema poderá ser publicado diretamente do Visual Studio no Banco de Dados SQL do Azure. Use esse método quando o esquema de banco de dados requer mais alterações do que o Assistente de migração pode administrar sozinho.

## Decidir quais opções usar
- Se você prevê que um banco de dados pode ser migrado sem alteração, deve ser usada a opção 1, que é rápida e fácil. Se você não tiver certeza, inicie pela exportação de um BACPAC somente de esquema do banco de dados, conforme descrito na opção 1. Se a exportação tiver êxito sem erros, você pode usar a opção 1 para migrar o banco de dados com seus dados.  
- Caso encontre erros durante a exportação da opção 1, use a opção 2 e corrija o esquema de banco de dados offline no Visual Studio usando uma combinação do assistente de migração e aplique manualmente as alterações do esquema. Uma cópia do banco de dados de origem é atualizada in situ e, em seguida, é migrada para o Azure usando a opção 1.

## Ferramentas de migração
As ferramentas usadas incluem o SSMS (SQL Server Management Studio) e as ferramentas do SQL Server no Visual Studio (VS, SSDT), bem como o portal do Azure.

> Certifique-se de instalar as versões mais recentes das ferramentas de cliente, pois as versões anteriores não são compatíveis com o Banco de Dados SQL V12.

### SQL Server Management Studio (SSMS)
O SSMS pode ser usado para implantar um banco de dados compatível diretamente ao banco de dados SQL do Azure ou para exportar um backup lógico do banco de dados como um BACPAC, que pode ser importado, ainda usando o SSMS, para criar um novo banco de dados SQL do Azure.

[Baixar a última versão do SSMS](https://msdn.microsoft.com/library/mt238290.aspx)

### Ferramenta do SQL Server no Visual Studio (VS, SSDT)
As ferramentas do SQL Server no Visual Studio podem ser usadas para criar e gerenciar um projeto de banco de dados que consiste em um conjunto de arquivos Transact-SQL para cada objeto no esquema. O projeto pode ser importado de um banco de dados ou de um arquivo de script. Depois de criado, o projeto pode ser transferido para o banco de dados SQL do Azure v12 compilar o projeto e, em seguida, valida a compatibilidade do esquema. Clicar em um erro abre o arquivo de Transact-SQL correspondente, permitindo que ele seja editado e o erro corrigido. Depois que todos os erros são corrigidos, o projeto pode ser publicado diretamente para o banco de dados SQL a fim de criar um banco de dados vazio ou de volta para (cópia de) o banco de dados original do SQL Server para atualizar seu esquema, que permite que o banco de dados seja implantado com seus dados usando o SSMS acima.

Use [o SQL Server Data Tools para Visual Studio mais recente](https://msdn.microsoft.com/library/mt204009.aspx) com o Visual Studio 2013 Update 4 ou posterior.

## Comparações
| Opção 1: | Opção 2: |
| ------------ | ------------ | ------------ |
| Implantar um banco de dados compatível com banco de dados do SQL do Azure | Atualizar o banco de dados no local, em seguida, implantar o banco de dados SQL do Azure |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![Edição offline](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| Usa o SSMS | Usa o VS e o SSMS |
|Processo simples, requer que esse esquema seja compatível. O esquema é migrado inalterado. | O esquema é importado em um projeto de banco de dados no Visual Studio. Atualizações adicionais são feitas usando o SSDT para Visual Studio e o esquema final é usado para atualizar o banco de dados no local original. |
| Sempre implanta ou exporta o banco de dados inteiro. | Controle total dos objetos que estão incluídos na migração. |
| Não há provisão para alterar a saída se houver erros, o esquema de origem deve ser compatível. | Recursos completos do SSDT para Visual Studio disponíveis. O esquema é alterado offline. | A validação do aplicativo ocorre no Azure. Deve ser mínima, já que o esquema é migrado sem alterações. | A validação de aplicativos pode ser feita no SQL Server antes que o banco de dados seja implantado no Azure. |
| É um processo de uma ou duas etapas de configuração simples e fácil. | Processo mais complexo com várias etapas (mais simples se for apenas a implantação do esquema). |

<!---HONumber=August15_HO9-->