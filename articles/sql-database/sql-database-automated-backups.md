<properties
   pageTitle="Continuidade comercial em nuvem - Backup interno - Banco de Dados SQL | Microsoft Azure"
   description="Saiba mais sobre backups internos do Banco de Dados SQL que permitem a você reverter um Banco de Dados SQL do Azure para um ponto anterior ou copiar um banco de dados para um novo banco de dados em uma região geográfica (por até 35 dias)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-features"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Visão geral: backups automatizados do Banco de Dados SQL

O serviço Banco de Dados SQL do Azure protege todos os bancos de dados com um backup automatizado, que é mantido por sete dias para Basic, 14 dias para Standard e 35 dias para Premium. Você pode usar esses backups automatizado para executar recuperações pontuais e restaurar um banco de dados excluído após uma corrupção ou exclusão de dados acidental.

Os backups de banco de dados são feitos automaticamente sem nenhuma necessidade de aceitação e sem encargos adicionais. Esses backups automáticos e a restauração pontual fornecem uma maneira sem custo e sem administração para proteger bancos de dados de corrupção ou de exclusão acidental, seja qual for a causa.

## Custos do Backup Automatizado

O Banco de Dados SQL do Microsoft Azure fornece até 200% de seu armazenamento máximo de banco de dados provisionado em armazenamento de backup, sem custo adicional. Por exemplo, se você tiver uma instância de banco de dados Standard com tamanho provisionado de 250 GB, serão oferecidos a você 500 GB de espaço de armazenamento para backup sem custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, é possível optar por reduzir o período de retenção ao entrar em contato com o Suporte do Azure ou pagar pelo armazenamento de backup adicional com a taxa padrão do Armazenamento com Redundância Geográfica com Acesso de Leitura (RA-GRS).

## Detalhes de backup automático

Todos os bancos de dados Basic, Standard e Premium são protegidos por backups automáticos. Os backups completos são feitos a cada semana, os backups diferenciais a cada dia e os backups de log a cada cinco minutos. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente, isso é concluído em 30 minutos, mas pode demorar mais. Se um banco de dados já for grande, por exemplo, se for criado como o resultado de uma cópia ou de uma restauração de banco de dados de um banco de dados grande, então o primeiro backup completo poderá demorar mais para ser concluído. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato dos backups completos e diferenciais é determinado pelo sistema para balancear a carga total. Os arquivos de backup são armazenados em uma conta de armazenamento com redundância geográfica com acesso de leitura (RA-GRS) para garantir a disponibilidade para fins de recuperação de desastre.

## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->