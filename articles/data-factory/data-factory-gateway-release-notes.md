<properties 
	pageTitle="Notas de versão para o Gateway de Gerenciamento de Dados | Azure Data Factory" 
	description="Notas de versão do Gateway de Gerenciamento de Dados" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/17/2016" 
	ms.author="spelluru"/>

# Notas de versão para o Gateway de Gerenciamento de Dados

Um dos desafios da integração de dados moderna é mover dados continuamente para e do local para a nuvem. O data factory torna perfeita essa integração com o Gateway de Gerenciamento de Dados, que é um agente que você pode instalar localmente para habilitar a movimentação de dados híbridos.

Confira o artigo [Como mover dados entre o local e a nuvem usando o Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md) para obter mais informações.

## VERSÃO ATUAL (1.12.5953.1)
- Correções de bug


## Versões anteriores

## 1\.11.5918.1

- O tamanho máximo do log de eventos do gateway foi aumentado de 1 MB para 40 MB.
- Uma caixa de diálogo de aviso é exibida caso uma reinicialização seja necessária durante a atualização automática do gateway. Você pode optar por reiniciar logo em seguida ou mais tarde. 
- Em caso de falha da atualização automática, instalador do gateway tentará novamente executar a atualização automática 3 vezes no máximo.
- Aprimoramentos de desempenho
	- Melhora no desempenho do carregamento de grandes tabelas de servidor local no cenário de cópia sem código.
- Correções de bug

## 1\.10.5892.1

- Aprimoramentos de desempenho
- Correções de bug

## 1\.9.5865.2

- Capacidade de atualização automática zero touch
- Novo ícone de bandeja com indicadores de status do gateway
- Capacidade de "Atualizar agora" do cliente
- Capacidade de definir o horário da agenda de atualização
- Script do PowerShell para ativar/desativar a atualização automática
- Suporte para o formato JSON  
- Aprimoramentos de desempenho
- Correções de bug

## 1\.8.5822.1

- Melhorar a experiência de solução de problemas
- Aprimoramentos de desempenho
- Correções de bug

### 1\.7.5795.1

- Aprimoramentos de desempenho
- Correções de bug

### 1\.7.5764.1

- Aprimoramentos de desempenho
- Correções de bug

### 1\.6.5735.1

- Suporte local a coletor/fonte HDFS
- Aprimoramentos de desempenho
- Correções de bug

### 1\.6.5696.1

- Aprimoramentos de desempenho
- Correções de bug

### 1\.6.5676.1

- Suporte a ferramentas de diagnóstico no Gerenciador de Configurações
- Suporte a colunas de tabela para fontes de dados tabulares do Azure Data Factory
- Suporte a SQL DW para Azure Data Factory
- Suporte Reclusivo em BlobSource e FileSource para o Azure Data Factory
- Suporte a CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy em BlobSink e FileSink com Cópia Binária para o Azure Data Factory
- Suporte a relatórios de andamento de Atividade de Cópia do Azure Data Factory
- Suporte a Validação de Conectividade de Fonte de Dados para Azure Data Factory
- Correções de bug


### 1\.6.5672.1

- Suporte a nome de tabela para fonte de dados ODBC para o Azure Data Factory
- Aprimoramentos de desempenho
- Correções de bug

### 1\.6.5658.1

- Suporte a Coletor de Arquivo para o Azure Data Factory
- Suporte à preservação de hierarquia na cópia binária para o Azure Data Factory
- Suporte a Idempotência de Atividade de Cópia para o Azure Data Factory
- Correções de bug

### 1\.6.5640.1

- Suporte a três ou mais fontes de dados para o Azure Data Factory (ODBC, OData, HDFS)
- Suporte ao caractere de aspas no analisador de csv para o Azure Data Factory
- Suporte à compactação (BZip2)
- Correções de bug

### 1\.5.5612.1

- Suporte a cinco bancos de dados relacionais do Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
- Suporte à compactação (Gzip e Deflate)
- Aprimoramentos de desempenho
- Correções de bug


### 1\.4.5549.1

- Adicionar suporte a fonte de dados Oracle para o Azure Data Factory
- Aprimoramentos de desempenho
- Correções de bug

### 1\.4.5492.1

- Binário unificado que dá suporte aos serviços Microsoft Azure Data Factory e Office 365 Power BI
- Refinar a interface do usuário de Configuração e o processo de registro
- Azure Data Factory – suporte a Ingresso e Egresso do Azure para fonte de dados do SQL Server

### 1\.2.5303.1

- 	Correção do problema de tempo limite para dar suporte a conexões de fontes de dados mais demoradas. 
 	
### 1\.1.5526.8

- Requer o .NET Framework 4.5.1 como pré-requisito durante a instalação.

### 1\.0.5144.2

- Nenhuma alteração que afeta os cenários do Azure Data Factory. 

## Perguntas/respostas

### Por que o Gerenciador de Fonte de Dados está tentando se conectar a um gateway?
Esse é um design de segurança em que você só pode configurar fontes de dados locais para acesso à nuvem na rede corporativa, e suas credenciais não fluirão para fora do firewall corporativo. Verifique se o seu computador pode acessar o computador em que o gateway está instalado.

<!---HONumber=AcomDC_0518_2016-->