<properties urlDisplayName="Storage for SQL Server backups" pageTitle="Como usar o armazenamento do Azure para o backup e a restauração do SQL Server | Azure" metaKeywords="" description="" metaCanonical="" services="storage" documentationCenter="" title="How to Use Azure Storage for SQL Server Backup and Restore" authors="jeffreyg" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/30/2014" ms.author="jeffreyg; jeffreyg" />



<h1 id="SQLServerBackupandRestoretostorage"> Como usar o armazenamento do Azure para o backup e restauração SQL Server</h1>

O recurso que fornece a capacidade de gravar backups do SQL Server no serviço de armazenamento de blobs do Azure foi lançado no SQL Server 2012 SP1 CU2. Você pode usar essa funcionalidade para fazer backup e restauração do serviço Blob do Azure por meio de um banco de dados local do SQL Server ou um banco de dados do SQL Server em uma máquina virtual do Azure. Backup em nuvem oferece benefícios de disponibilidade, armazenamento externo replicados geo ilimitado e facilidade de migração de dados para e da nuvem.   Nesta versão, você pode emitir declarações de BACKUP ou restauração usando T-SQL ou o SMO. Voltar para backup ou restauração do serviço de armazenamento de BLOBs do Azure usando SQL Server Management Studio Assistente de Backup ou restauração não está disponível.

<h2> Vantagens de usar o serviço Blob Azure para Backups do SQL Server</h2>

Gerenciamento de armazenamento, o risco de falha de armazenamento, acesso a armazenamento externo e configurar dispositivos são alguns dos desafios de backup gerais.  Para o SQL Server em execução em uma máquina Virtual do Azure, há desafios adicionais de configuração e de backup de um VHD ou configurando anexado unidades. A seguinte lista alguns dos principais benefícios de usar o armazenamento de serviço de armazenamento de BLOBs do Azure para backups do SQL Server:

* Armazenamento externo ilimitado, flexível e confiável: Armazenar seus backups no serviço Blob do Azure pode ser uma opção conveniente, flexível e de fácil acesso externo. Criar armazenamento externo para os backups do SQL Server pode ser tão fácil quanto modificando seus scripts/trabalhos existentes. Armazenamento externo deve ser tipicamente longe o suficiente da localização do banco de dados de produção para evitar um desastre único que pode afetar os dois locais de banco de dados externo e produção. Escolhendo a localização geográfica replicar o armazenamento de Blob têm uma camada extra de proteção em caso de desastre que poderia afetar a região inteira. Além disso, os backups estão disponíveis de qualquer lugar e a qualquer momento e podem ser facilmente acessados para restaurações.
* Arquivo de backup: O serviço de armazenamento de blobs do Azure oferece uma alternativa melhor que a opção de fita usada com frequência para arquivar backups. Armazenamento em fita pode exigir transporte físico para um recurso externo e medidas para proteger a mídia. Armazenar os backups em armazenamento de BLOBs do Azure fornece um instante e altamente disponível, e a opção de arquivamento de um duráveis.
* Sem sobrecarga de gerenciamento de hardware: Não há nenhuma sobrecarga de gerenciamento de hardware com os serviços do Azure. Serviços Azure gerenciar o hardware e fornecem replicação geográfica para redundância e proteção contra falhas de hardware.
* No momento, para instâncias do SQL Server em execução em uma máquina virtual do Azure, o backup dos serviços de armazenamento de blobs do Azure pode ser feito criando discos anexados. No entanto, há um limite para o número de discos que você pode anexar a uma máquina Virtual do Azure. Esse limite é de 16 discos para uma instância extra grande e menos para instâncias menores. Ao habilitar um backup direto para o armazenamento de Blob do Azure, você pode ignorar o limite de 16 discos.
* Além disso, o arquivo de backup que agora é armazenado no serviço de armazenamento de blobs do Azure está diretamente disponível para um SQL Server local ou outro SQL Server em execução em uma máquina virtual do Azure, sem a necessidade de o banco de dados anexar/desanexar ou baixar e anexar o VHD.
* Benefícios de custo: Pague somente pelo serviço usado. Pode ser econômico como uma opção de arquivamento externo e backup. Consulte a [calculadora de preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Pricing Calculator") e o [artigo sobre Preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Pricing article") para obter mais informações.

Para obter mais detalhes, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

As seções a seguir apresentam o serviço de armazenamento de BLOBs do Azure e os componentes do SQL Server usados quando fazer backup ou a restauração do serviço de armazenamento de BLOBs do Azure. É importante compreender os componentes e a interação entre eles para fazer um backup ou restauração do serviço de armazenamento de BLOBs do Azure. 

Criar uma conta do Azure é a primeira etapa para esse processo. SQL Server usa o nome da conta de armazenamento do Azure e seus valores de chave de acesso para autenticar e gravar e ler blobs para o serviço de armazenamento. A credencial do SQL Server armazena essas informações de autenticação e é usada durante as operações de backup ou restauração. 

Para uma explicação completa da criação de uma conta de armazenamento e realizar uma restauração simples, consulte [Introdução ao Serviço de Armazenamento do Azure para Backup e restauração do SQL Server](http://go.microsoft.com/fwlink/?LinkId=271615) 

## Componentes do serviço de armazenamento de blobs do Azure 

* Conta de Armazenamento: A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para acessar um serviço de armazenamento de BLOBs do Azure, primeiro crie uma conta de armazenamento do Azure. SQL Server usa o nome da conta de armazenamento do Azure e seus valores de chave de acesso para autenticar e gravar e ler blobs para o serviço de armazenamento. 
Para obter mais informações sobre os serviços de armazenamento de blobs do Azure, consulte [Como usar serviços de armazenamento de blobs do Azure](http://www.windowsazure.com/pt-br/develop/net/how-to-guides/blob-storage/)

* Contêiner: Um contêiner fornece um agrupamento de um conjunto de blobs e pode armazenar um número ilimitado de blobs. Para escrever um SQL Server backup para um serviço Blob do Azure, você deve ter pelo menos o contêiner raiz criado. 

* Blob: Um arquivo de qualquer tipo e tamanho. Há dois tipos de blobs que podem ser armazenados no Serviço de Armazenamento de blob do Azure: blobs de página e de bloco.  O backup do SQL Server usa Blobs de página como o tipo do Blob. Os blobs são endereçáveis usando o seguinte formato de URL: `https://<storage account>.blob.core.windows.net/<container>/<blob>`
Para obter mais informações sobre blobs, consulte [Noções básicas sobre blobs de bloco e de página](http://msdn.microsoft.com/pt-br/library/windowsazure/ee691964.aspx)

## Componentes do SQL Server

* URL: Uma URL especifica um URI (Uniform Resource Identifier) para um único arquivo de backup. A URL é usada para fornecer o local e o nome do arquivo de backup do SQL Server. Nessa implementação, a URL válida somente é aquele que aponta para um Blob de páginas em uma conta de armazenamento do Azure. A URL deve apontar para um Blob real, não apenas um contêiner. Se o Blob não existir, ele será criado. Se um Blob existente for especificado, BACKUP falha, a menos que o > opção com o formato especificada. 
A seguir está um exemplo do URL desejar especificar o comando de BACKUP: 
**`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Observação:</b> HTTPS não é obrigatória, mas é recomendável.
<b>Importante</b>
Se você optar por copiar e carregar um arquivo de backup para o serviço de armazenamento de Blob do Azure, você deve usar um tipo de blob de páginas como sua opção de armazenamento se você planeja usar este arquivo para operações de restauração. RESTAURAÇÃO por meio de um tipo de blob de blocos falhará com um erro. 

* Credencial: As informações necessárias para se conectar e autenticar ao serviço de armazenamento de blobs do Azure são armazenadas como uma credencial.  Para o SQL Server gravar backups para uma restauração ou BLOBs do Azure dela, uma credencial do SQL Server deve ser criada. A credencial armazena o nome da conta de armazenamento e a chave de acesso da conta de armazenamento.  Depois que a credencial é criada, ele deve ser especificado na opção com CREDENCIAIS ao emitir as instruções de BACKUP/restauração. Para obter mais informações sobre como visualizar, copiar ou gerar novamente chaves de acesso da conta de armazenamento, consulte [Chaves de acesso da conta de armazenamento](http://msdn.microsoft.com/pt-br/library/windowsazure/hh531566.aspx).
Para obter instruções passo a passo sobre como criar uma credencial do SQL Server, consulte [Introdução ao serviço de armazenamento do Azure para SQL Server Backup e restauração](http://go.microsoft.com/fwlink/?LinkId=271615).

## Backups e restauração do banco de dados SQL Server com blobs do Azure - Conceitos e tarefas:

**Conceitos e considerações sobre código exemplos:**

[Backup e restauração do SQL Server com o Serviço de armazenamento de blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Tutorial de Introdução:**

[Introdução ao backup e à restauração do SQL Server no Serviço de armazenamento de blob do Azure](http://go.microsoft.com/fwlink/?LinkID=271615 "Tutorial")

**Práticas recomendadas, Solução de problemas:**
	
[Práticas recomendadas de backup e restauração (Serviço de armazenamento de blob do Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)




	





<!--HONumber=35.1-->
