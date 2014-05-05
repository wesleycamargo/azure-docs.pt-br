<properties linkid="manage-services-storage-SQL-Server-backup" urlDisplayName="Armazenamento para backups de servidor SQL" pageTitle="Como usar o armazenamento Azure para backup e restauração do servidor SQL | Azure" metaKeywords="" description="" metaCanonical="" services="storage" documentationCenter="" title="Como usar o armazenamento Azure para o backup e restauração do servidor SQL" authors="karaman" solutions="" manager="clairt" editor="tysonn" />



<h1 id="SQLServerBackupandRestoretostorage">  Como usar o armazenamento Azure para o backup e restauração do servidor SQL</h1>

O recurso que fornece a capacidade de gravar backups do SQL Server para o serviço de armazenamento de BLOBs do Azure foi lançado no SQL Server 2012 SP1 CU2. você pode usar essa funcionalidade para fazer o backup e a restauração do serviço de um banco de dados local do SQL Server ou um banco de dados do SQL Server em uma máquina Virtual do Azure BLOBs do Azure. Backup em nuvem oferece benefícios de disponibilidade, armazenamento externo replicados geo ilimitado e facilidade de migração de dados para e da nuvem.   Nesta versão, você pode emitir declarações de BACKUP ou restauração usando T-SQL ou o SMO. Voltar para backup ou restauração do serviço de armazenamento de BLOBs do Azure usando SQL Server Management Studio Assistente de Backup ou restauração não está disponível.

<h2> Vantagens de usar o serviço Blob Azure para Backups do SQL Server</h2>

Gerenciamento de armazenamento, o risco de falha de armazenamento, acesso a armazenamento externo e configurar dispositivos são alguns dos desafios de backup gerais.  Para o SQL Server em execução em uma máquina Virtual do Azure, há desafios adicionais de configuração e de backup de um VHD ou configurando anexado unidades. A seguinte lista alguns dos principais benefícios de usar o armazenamento de serviço de armazenamento de BLOBs do Azure para backups do SQL Server:

* Ilimitado, confiável e flexível de armazenamento externo: pode ser um conveniente armazenar os backups no serviço de BLOBs do Azure, flexíveis e fáceis de acessar esta opção de fora da empresa. Criar armazenamento externo para os backups do SQL Server pode ser tão fácil quanto modificando seus scripts/trabalhos existentes. Armazenamento externo deve ser tipicamente longe o suficiente da localização do banco de dados de produção para evitar um desastre único que pode afetar os dois locais de banco de dados externo e produção. Escolhendo a localização geográfica replicar o armazenamento de Blob têm uma camada extra de proteção em caso de desastre que poderia afetar a região inteira. Além disso, os backups estão disponíveis de qualquer lugar e a qualquer momento e podem ser facilmente acessados para restaurações.
* Arquivo de backup: O serviço de armazenamento de BLOBs do Azure oferece uma alternativa melhor que a opção de fita usadas com frequência para arquivar backups. Armazenamento em fita pode exigir transporte físico para um recurso externo e medidas para proteger a mídia. Armazenar os backups em armazenamento de BLOBs do Azure fornece um instante e altamente disponível, e a opção de arquivamento de um duráveis.
* Sem sobrecarga de gerenciamento de hardware: não há nenhuma sobrecarga de gerenciamento de hardware com o Azure services. Serviços Azure gerenciar o hardware e fornecem replicação geográfica para redundância e proteção contra falhas de hardware.
* No momento de instâncias do SQL Server em execução em uma máquina de Virtual do Azure, backup para serviços de armazenamento de BLOBs do Azure podem ser feitas criando discos anexados. No entanto, há um limite para o número de discos que você pode anexar a uma máquina Virtual do Azure. Esse limite é de 16 discos para uma instância extra grande e menos para instâncias menores. Ao habilitar um backup direto para o armazenamento de Blob do Azure, você pode ignorar o limite de 16 discos.
* Além disso, o arquivo de backup que agora é armazenado no serviço de armazenamento de BLOBs do Azure está diretamente disponível para um de SQL Server local ou outro SQL Server em execução em uma Azure Máquina Virtual, sem a necessidade de banco de dados anexar/retirar ou fazer o download e anexar o VHD.
* Benefícios de custo: Paga somente pelo serviço que é usado. Pode ser econômico como uma opção de arquivamento externo e backup. Consulte o [Azure Calculadora de preços](http://go.microsoft.com/fwlink/?LinkId=277060 "Pricing Calculator")e o [artigo preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Pricing article") para obter mais informações.

Para obter mais detalhes, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

As seções a seguir apresentam o serviço de armazenamento de BLOBs do Azure e os componentes do SQL Server usados quando fazer backup ou a restauração do serviço de armazenamento de BLOBs do Azure. É importante compreender os componentes e a interação entre eles para fazer um backup ou restauração do serviço de armazenamento de BLOBs do Azure. 

Criar uma conta do Azure é a primeira etapa para esse processo. SQL Server usa o nome da conta de armazenamento do Azure e seus valores de chave de acesso para autenticar e gravar e ler blobs para o serviço de armazenamento. A credencial do SQL Server armazena essas informações de autenticação e é usada durante as operações de backup ou restauração. 

Para uma explicação completa da criação de uma conta de armazenamento e realizar uma restauração simple, consulte [Introdução withAzure serviço de armazenamento de Backup do SQL Server e restauração](http://go.microsoft.com/fwlink/?LinkId=271615) 

## Componentes de serviço de armazenamento de Blob Azure 

* Conta de armazenamento: A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para acessar um serviço de armazenamento de BLOBs do Azure, primeiro crie uma conta de armazenamento do Azure. SQL Server usa o nome da conta de armazenamento do Azure e seus valores de chave de acesso para autenticar e gravar e ler blobs para o serviço de armazenamento. 
Para obter mais informações sobre os serviços de armazenamento de blobs do Azure, consulte [Como usar serviços de armazenamento de blobs do Azure](http://www.windowsazure.com/pt-br/develop/net/how-to-guides/blob-storage/).

* Contêiner: Um contêiner fornece um agrupamento de um conjunto de Blobs e pode armazenar um número ilimitado de Blobs. Para escrever um SQL Server backup para um serviço de BLOBs do Azure, você deve ter pelo menos o contêiner raiz criado. 

* Blob: um arquivo de qualquer tipo e tamanho. Há dois tipos de blobs que podem ser armazenados no Serviço de Armazenamento de blob do Azure: blobs de página e de bloco.  O backup do SQL Server usa Blobs de página como o tipo do Blob. Os blobs são endereçáveis usando o seguinte formato de URL: `https://<storage account>.blob.core.windows.net/<container>/<blob>` Para obter mais informações sobre blobs de páginas, consulte [Compreendendo blobs de bloco e blobs de página](http://msdn.microsoft.com/pt-br/library/windowsazure/ee691964.aspx)

## Componentes do SQL Server

* URL: URL especifica um URI (Uniform Resource Identifier) para um único arquivo de backup. A URL é usada para fornecer o local e o nome do arquivo de backup do SQL Server. Nessa implementação, a URL válida somente é aquele que aponta para uma página Blob em uma conta de armazenamento do Azure. A URL deve apontar para um Blob real, não apenas um contêiner. Se o Blob não existir, ele será criado. Se um Blob existente for especificado, BACKUP falha, a menos que o > opção com o formato especificada. 
A seguir está um exemplo do URL desejar especificar o comando de BACKUP: 
**`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Observação:</b> HTTPS não é obrigatória, mas é recomendável.
<b>Importante</b>
Se você optar por copiar e carregar um arquivo de backup para o serviço de armazenamento de BLOBs do Azure, você deve usar um tipo de blob de página como sua opção de armazenamento se você planeja usar este arquivo para operações de restauração. RESTAURAÇÃO a partir de um tipo de blob de bloco falhará com um erro. 

* Credencial: As informações necessárias para se conectar e autenticar ao serviço de armazenamento de BLOBs do Azure são armazenadas como uma credencial.  Para o SQL Server gravar backups para uma restauração ou BLOBs do Azure dela, uma credencial do SQL Server deve ser criada. A credencial armazena o nome da conta de armazenamento e a chave de acesso da conta de armazenamento.  Depois que a credencial é criada, ele deve ser especificado na opção com CREDENCIAIS ao emitir as instruções de BACKUP/restauração. para obter mais informações sobre como visualizar, copiar ou gerar novamente chaves de acesso da conta de armazenamento, consulte [Chaves de acesso da conta de armazenamento](http://msdn.microsoft.com/pt-br/library/windowsazure/hh531566.aspx).
Para obter instruções passo a passo sobre como criar uma credencial do SQL Server, consulte [Introdução ao serviço de armazenamento do Azure para SQL Server Backup e restauração](http://go.microsoft.com/fwlink/?LinkId=271615)

## Backups de banco de dados do SQL Server e restauração com o Azure Blobs- tarefas e conceitos:

**Conceitos e considerações sobre código exemplos:**

[Backup e restauração do SQL Server com o Serviço de armazenamento de blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Tutorial de Introdução:**

[Introdução ao backup e à restauração do SQL Server no Serviço de armazenamento de blob do Azure](http://go.microsoft.com/fwlink/?LinkID=271615 "Tutorial")

**Práticas recomendadas da solução de problemas**
	
[Práticas recomendadas de backup e restauração (Serviço de armazenamento de blob do Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)

