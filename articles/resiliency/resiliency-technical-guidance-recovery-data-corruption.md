<properties
   pageTitle="Orientações técnicas de resiliência para a recuperação de dados corrompidos ou exclusão acidental | Microsoft Azure"
   description="Artigo com noções básicas sobre como recuperar dados corrompidos ou de exclusão acidental e como criar aplicativos resilientes, altamente disponíveis, com tolerância a falhas, bem como planejamento de recuperação de desastres"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Orientações técnicas de resiliência do Azure - Recuperação de dados corrompidos ou exclusão acidental

Parte de um plano robusto de continuidade dos negócios é ter um plano, caso os dados sejam corrompidos ou excluídos acidentalmente. Abaixo estão informações sobre recuperação depois que os dados são corrompidos ou excluídos acidentalmente devido a erros do aplicativo ou do operador.

##Máquinas Virtuais
Para proteger as máquinas virtuais do Azure (às vezes chamadas de VM IaaS [Infraestrutura como Serviço]) de erros de aplicativo ou exclusão acidental use o [Backup do Azure](https://azure.microsoft.com/services/backup/). O Backup do Azure permite a criação de backups que são consistentes em vários discos de VM. Além disso, o Cofre de Backup pode ser replicado entre regiões para fornecer recuperação de perda de região.

##Armazenamento
Observe que, enquanto o armazenamento do Azure fornece resiliência de dados por meio de réplicas automáticas, isso não impede que o código do aplicativo (ou desenvolvedores/usuários) corrompa dados por meio de exclusão acidental ou involuntária, atualização e assim por diante. Manter a fidelidade dos dados no caso de erro do usuário ou aplicativo requer técnicas mais avançadas, como copiar os dados de um local de armazenamento secundário com um log de auditoria. Os desenvolvedores podem tirar proveito da [capacidade de instantâneo](https://msdn.microsoft.com/library/azure/ee691971.aspx), que pode criar um ponto somente leitura em instantâneos de conteúdo do blob. Isso pode ser usado como a base de uma solução de fidelidade de dados para blobs de armazenamento do Azure.

###Backup de armazenamento de tabela e de blob
Embora blobs e tabelas sejam altamente duráveis, sempre representam o estado atual dos dados. A recuperação de modificação indesejada ou exclusão de dados pode exigir a restauração dos dados para um estado anterior. Isso pode ser obtido aproveitando as capacidades fornecidas pelo Azure para armazenar e manter cópias pontuais.

Para Blobs do Azure, você pode executar backups pontuais usando o [recurso de instantâneo de blob](https://msdn.microsoft.com/library/ee691971.aspx). Para cada instantâneo, você é cobrado apenas pelo armazenamento necessário para armazenar as diferenças no blob desde o último estado do instantâneo. Os instantâneos são dependentes da existência do blob original nos quais se baseiam, portanto uma operação de cópia para outro blob ou até mesmo outra conta de armazenamento é aconselhável para garantir que os dados de backup estejam adequadamente protegidos contra exclusão acidental. Para tabelas do Azure, você pode fazer cópias pontuais em uma tabela diferente ou nos Blobs do Azure. Diretrizes e exemplos mais detalhados de como efetuar backups de aplicativo de tabelas e blobs podem ser encontrados aqui:

  * [Protegendo suas tabelas contra erros de aplicativo](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Protegendo seus blobs contra erros de aplicativo](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##Banco de dados
Há várias opções de [continuidade de negócios](../sql-database/sql-database-business-continuity.md) (backup, restauração) disponíveis para o banco de dados SQL do Azure. Bancos de dados podem ser copiados por meio da funcionalidade [Cópia do banco de dados](../sql-database/sql-database-copy.md) funcionalidade, ou [exportar](../sql-database/sql-database-export.md) e [importar](https://msdn.microsoft.com/library/hh710052.aspx) um arquivo bacpac do SQL Server. Cópia de banco de dados fornece resultados transacionais consistentes, enquanto um bacpac não (por meio do serviço de importação/exportação). Essas duas opções são executadas como serviços baseados em fila no data center e atualmente não fornecem um tempo de conclusão de SLA.

>[AZURE.NOTE]As opções de importação/exportação e cópia de banco de dados impõem um grau significativo de carga no banco de dados de origem e pode disparar a contenção de recursos ou eventos de limitação.

###Backup do banco de dados SQL
Backups pontuais para o Banco de Dados SQL do Microsoft Azure são obtidos ao [Copiar seu banco de dados SQL do Azure](../sql-database/sql-database-copy.md). Você pode usar esse comando para criar uma cópia transacionalmente consistente do banco de dados no mesmo servidor de banco de dados lógico ou em um servidor diferente. Em ambos os casos, a cópia do banco de dados é totalmente funcional e completamente independente do banco de dados de origem. Cada cópia que você cria representa uma opção de recuperação pontual. Você pode recuperar o estado do banco de dados completamente renomeando o novo banco de dados com o nome do banco de dados de origem. Como alternativa, você pode recuperar um subconjunto específico de dados do novo banco de dados usando consultas Transact-SQL. Para detalhes adicionais sobre o banco de dados SQL, consulte: [Continuidade de negócios na nuvem e recuperação de desastre do banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md).

###SQL Server em backup de máquinas virtuais
Para o SQL Server usado com as máquinas virtuais de infraestrutura como serviço do Azure (geralmente chamadas de IaaS ou VMs de IaaS), há duas opções: backups tradicionais e envio de logs. O uso de backups tradicionais permite restaurar para um ponto específico no tempo, mas o processo de recuperação é lento. A restauração de backups tradicionais exige começar com um backup inicial completo e aplicar quaisquer backups feitos depois disso. A segunda opção é configurar uma sessão de envio de logs para atrasar a restauração de backups de log (por exemplo, por duas horas). Isso fornece uma janela para recuperação de erros feitos no primário.

##Outros serviços da plataforma Azure
Alguns serviços da plataforma Azure armazenam informações em uma conta de armazenamento controlada pelo usuário ou banco de dados SQL do Azure. Se o recurso de conta ou armazenamento for excluído ou corrompido, isso poderá causar erros graves no serviço. Nesses casos, é importante manter os backups que permitiriam recriar esses recursos caso eles sejam excluídos ou corrompidos.

Para sites do Azure e Serviços Móveis do Azure, você deve fazer o backup e a manutenção dos bancos de dados associados. Para o serviço de mídia do Azure e máquinas virtuais, você deve manter a conta de armazenamento do Azure associada e todos os recursos dessa conta. Por exemplo, para máquinas virtuais, você deve fazer backup e gerenciar os discos de VM no armazenamento de blobs do Azure.

##Listas de verificação de dados corrompidos ou exclusão acidental

##Lista de verificação de máquinas virtuais
  1. Examinar a seção [Máquinas Virtuais](#virtual-machines) deste documento
  2. Fazer backup e manutenção dos discos de VM com o Backup do Azure (ou seu próprio sistema usando o armazenamento de blobs do Azure e instantâneos de VHD)

##Lista de verificação de armazenamento
  1. Examinar a seção [Armazenamento](#storage) deste documento
  2. Fazer backups regulares dos recursos de armazenamento críticos
  3. Considerar o uso do recurso de instantâneo para blobs

##Lista de verificação de banco de dados
  1. Examinar a seção [Banco de dados](#database) deste documento
  2. Criar backups pontuais usando o comando Cópia de banco de dados

##Lista de verificação do SQL Server em backup de máquinas virtuais
  1. Examinar a seção [SQL Server em backup de máquinas virtuais](#sql-server-on-virtual-machines-backup) deste documento
  2. Usar backup tradicional e técnicas de restauração
  3. Criar um sessão de envio de logs em atraso

##Lista de verificação de aplicativos Web
  1. Fazer backup e manutenção do banco de dados associado, se houver

##Lista de verificação de Serviços de Mídia
  1. Fazer backup e manutenção dos recursos de armazenamento associados

##Mais informações
Consulte [Armazenamento e cenários de backup e recuperação](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/) para obter mais informações sobre recursos de restauração e backup do Azure.

##Próximas etapas
Este artigo faz parte de uma série que tem como foco [Orientações técnicas de resiliência do Azure](./resiliency-technical-guidance.md). Se você estiver procurando mais resiliência, recuperação de desastres e recursos de alta disponibilidade, consulte as orientações técnicas de resiliência do Azure, [recursos adicionais](./resiliency-technical-guidance.md#additional-resources).

<!---HONumber=AcomDC_0525_2016-->