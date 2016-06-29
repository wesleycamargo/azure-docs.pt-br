<properties
   pageTitle="Migrar seu banco de dados existente do Azure SQL Data Warehouse para o armazenamento premium | Microsoft Azure"
   description="Instruções para migrar um SQL Data Warehouse existente para o armazenamento premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Detalhes de migração para o Armazenamento Premium
O SQL Data Warehouse introduziu recentemente o [Armazenamento Premium para uma maior previsibilidade de desempenho][]. Agora estamos prontos para migrar os Data Warehouses existentes atualmente no Armazenamento Standard para o Armazenamento Premium. Continue lendo para obter mais detalhes sobre como e quando as migrações automáticas ocorrerão e como migrar automaticamente se você preferir controlar quando ocorre o tempo de inatividade.

Se você tiver mais de um Data Warehouse, use o [cronograma de migração automática][] abaixo para determinar quando ele será migrado também.

## Detalhes da migração automática
Por padrão, migraremos seu banco de dados para você entre 18h e 6h na hora do local da sua região em algum dia do [cronograma de migração automática][] abaixo. Durante a migração, o Data Warehouse existente ficará inutilizável. Estimamos que a migração levará cerca de uma hora por TB de armazenamento por Data Warehouse. Também vamos garantir que não haja cobrança durante a migração.

> [AZURE.NOTE] Você não poderá usar o Data Warehouse existente durante a migração. Quando a migração for concluída, o Data Warehouse ficará online novamente.

Os detalhes abaixo são as etapas que a Microsoft está seguindo por você para concluir a migração e não requer nenhuma atitude da sua parte. Para este exemplo, imagine que o DW existente no armazenamento padrão atualmente é denominado "MyDW".

1.	A Microsoft renomeará "MyDW" para "MyDW\_ DO\_NOT\_USE\_ [carimbo de data/hora]"
2.	A Microsoft pausará "MyDW\_ DO\_NOT\_USE\_ [carimbo de data/hora]". Durante esse tempo, a Microsoft fará um backup. Você poderá ver várias pausas/retomadas se encontrarmos problemas durante o processo.
3.	A Microsoft criará um novo DW chamado "MyDW" no Armazenamento Premium do backup feito na etapa 2 acima. "MyDW" não será exibido até a restauração ser concluída.
4.	Quando a restauração for concluída, "MyDW" será retornado para o mesmo nível de DWUs e estado pausado ou ativo em que estava antes da migração.
5.	Quando a migração for concluída, a Microsoft excluirá "MyDW\_DO\_NOT\_USE\_ [carimbo de data/hora]"
	
> [AZURE.NOTE] Estas configurações não serão transferidas como parte da migração:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

### Planejamento de migração automática
A migração automática ocorrerá entre 18h e 6h (hora local da região) em algum dia do cronograma de interrupção listada abaixo.

| Região | Data de início estimada | Data de término estimada |
| :------------------ | :--------------------------- | :--------------------------- |
| Leste da Austrália | Ainda não foi determinado | Ainda não foi determinado |
| Sudeste da Austrália | Ainda não foi determinado | Ainda não foi determinado |
| Canadá Central | 23 de junho de 2016 | 1 de julho de 2016 |
| Leste do Canadá | 23 de junho de 2016 | 1 de julho de 2016 |
| Centro dos EUA | 23 de junho de 2016 | 1 de julho de 2016 |
| Leste da China | Ainda não foi determinado | Ainda não foi determinado |
| Ásia Oriental | 23 de junho de 2016 | 1 de julho de 2016 |
| Leste dos EUA | 23 de junho de 2016 | 1 de julho de 2016 |
| Leste dos EUA 2 | 23 de junho de 2016 | 1 de julho de 2016 |
| Centro da Índia | 23 de junho de 2016 | 1 de julho de 2016 |
| Sul da Índia | 23 de junho de 2016 | 1 de julho de 2016 |
| Leste do Japão | Ainda não foi determinado | Ainda não foi determinado |
| Oeste do Japão | Ainda não foi determinado | Ainda não foi determinado |
| Centro-Norte dos EUA | Ainda não foi determinado | Ainda não foi determinado |
| Norte da Europa | Ainda não foi determinado | Ainda não foi determinado |
| Centro-Sul dos Estados Unidos | 23 de junho de 2016 | 1 de julho de 2016 |
| Sudeste Asiático | 23 de junho de 2016 | 1 de julho de 2016 |
| Europa Ocidental | 23 de junho de 2016 | 1 de julho de 2016 |
| Oeste dos EUA | 23 de junho de 2016 | 1 de julho de 2016 |

## Automigração para o Armazenamento Premium
Se desejar controlar quando o tempo de inatividade deve ocorrer, você poderá usar as etapas a seguir para migrar um Data Warehouse existente no Armazenamento Standard para o Armazenamento Premium. Se você optar por migrar automaticamente, deverá concluir a automigração antes de começar a migração automática na região para evitar o risco da migração automática causar conflito (consulte o [cronograma de migração automática][]).

> [AZURE.NOTE] O SQL Data Warehouse com Armazenamento Premium não tem redundância geográfica atualmente. Isso significa que quando o Data Warehouse é migrado para o Armazenamento Premium, os dados residem apenas em sua região atual. Quando estiverem disponíveis, os Backups Geográficos copiarão o Data Warehouse a cada 24 horas para a [região emparelhada do Azure][], permitindo que você restaure do Backup Geográfico para qualquer região no Azure. Depois que a funcionalidade de Backup Geográfico estiver disponível para automigrações, ela será anunciada em nosso [site de documentação principal][]. Por outro lado, as migrações automáticas não têm essa limitação.

### Determinar o tipo de armazenamento
Se você criou um DW antes das datas abaixo, está usando atualmente o Armazenamento Standard.

| Região | DW criado antes desta data |
| :------------------ | :-------------------------------- |
| Leste da Austrália | Armazenamento Premium ainda não disponível |
| Sudeste da Austrália | Armazenamento Premium ainda não disponível |
| Canadá Central | 25 de maio de 2016 |
| Leste do Canadá | 26 de maio de 2016 |
| Centro dos EUA | 26 de maio de 2016 |
| Leste da China | Armazenamento Premium ainda não disponível |
| Ásia Oriental | 25 de maio de 2016 |
| Leste dos EUA | 26 de maio de 2016 |
| Leste dos EUA 2 | 27 de maio de 2016 |
| Centro da Índia | 27 de maio de 2016 |
| Sul da Índia | 26 de maio de 2016 |
| Leste do Japão | Armazenamento Premium ainda não disponível |
| Oeste do Japão | Armazenamento Premium ainda não disponível |
| Centro-Norte dos EUA | Armazenamento Premium ainda não disponível |
| Norte da Europa | Armazenamento Premium ainda não disponível |
| Centro-Sul dos Estados Unidos | 27 de maio de 2016 |
| Sudeste Asiático | 24 de maio de 2016 |
| Europa Ocidental | 25 de maio de 2016 |
| Oeste dos EUA | 26 de maio de 2016 |


### Instruções de automigração
Se você quiser controlar seu tempo de inatividade, poderá migrar o Data Warehouse por conta própria usando backup e restauração. A parte de restauração da migração deve levar cerca de uma hora por TB de armazenamento por DW. Se você deseja manter o mesmo nome após a conclusão da migração, siga as etapas abaixo para [uma solução alternativa de renomeação][].

1.	[Pausar][] DWs que tenham backup automático
2.	[Restaurar][] do seu instantâneo mais recente
3.	Exclua seu DW existente do Armazenamento Standard. **Se você não conseguir fazer isso, será cobrado pelos dois DWs.**

> [AZURE.NOTE] Estas configurações não serão transferidas como parte da migração:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

#### Opcional: solução alternativa de renomeação 
Dois bancos de dados no mesmo servidor lógico não podem ter o mesmo nome. Atualmente, não há suporte para a capacidade de renomear um DW no SQL Data Warehouse. As instruções a seguir ajudarão você a contornar a ausência dessa funcionalidade durante uma migração automática (observe que as migrações automáticas não têm essa limitação).

Para este exemplo, imagine que o DW existente no armazenamento padrão atualmente é denominado "MyDW".

1.	[Pause][] "MyDW", que usará um backup automático
2.	[Restaure][], do seu instantâneo mais recente, um novo banco de dados com um nome diferente, como "MyDWTemp"
3.	Exclua "MyDW". **Se você não conseguir fazer isso, será cobrado pelos dois DWs.**
4.	Como "MyDWTemp" é um DW recém-criado, o backup não estará disponível para restaurar por determinado período de tempo. É recomendável continuar as operações por umas duas horas em "MyDWTemp" para depois continuar com as etapas 5 e 6.
5.	[Pause][] "MyDWTemp", que usará um backup automático.
6.	[Restaure][], do seu instantâneo “MyDWTemp” mais recente, um novo banco de dados chamado "MyDW".
7.	Exclua "MyDWTemp". **Se você não conseguir fazer isso, será cobrado pelos dois DWs.**

> [AZURE.NOTE] Estas configurações não serão transferidas como parte da migração:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

## Próximas etapas
Se você tiver algum problema com o Data Warehouse, [crie um tíquete de suporte][] e faça referência à "Migração para Armazenamento Premium" como possível causa.

<!--Image references-->

<!--Article references-->
[cronograma de migração automática]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[crie um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[região emparelhada do Azure]: ./best-practices-availability-paired-regions.md
[site de documentação principal]: ./services/sql-data-warehouse.md
[Pause]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Pausar]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Restaurar]: ./sql-data-warehouse-manage-database-restore-portal.md
[Restaure]: ./sql-data-warehouse-manage-database-restore-portal.md
[uma solução alternativa de renomeação]: #optional-rename-workaround

<!--MSDN references-->


<!--Other Web references-->
[Armazenamento Premium para uma maior previsibilidade de desempenho]: https://azure.microsoft.com/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/

<!---HONumber=AcomDC_0615_2016-->