<properties 
   pageTitle="Continuidade dos negócios do Banco de Dados SQL durante a atualização de aplicativo" 
   description="Esta seção fornece diretrizes para evitar tempo de inatividade durante uma atualização de aplicativo." 
   services="sql-database"
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/27/2016"
   ms.author="carlrab"/>

#Atualizar um aplicativo sem tempo de inatividade

No contexto do Microsoft Azure, o termo 'aplicativo' refere-se aos componentes como front-ends, serviços implantados em um serviço de nuvem e a camada de dados usada para persistir os metadados ou dados de aplicativo. Os aplicativos de nuvem geralmente são projetados para fornecer serviço ininterrupto, 24 horas por dia, sete dias por semana. Distribuir uma nova versão do aplicativo, quando alterações na camada de dados são aplicadas no site ativo pode, potencialmente, causar alguma interrupção, como redução de recursos disponibilizados ou, até mesmo, tempo de inatividade completo.

Ao desenvolver o processo de atualização do aplicativo, a meta principal deve ser eliminar ou reduzir consideravelmente a duração de tempo quando o recurso de aplicativo é reduzido. Para isso, o processo geralmente envolve a criação de uma cópia temporária do aplicativo a ser usada como um backup no caso de falha da atualização. Os seguintes fatores devem ser considerados ao projetar e planejar a atualização:

1.	Tempo máximo aceitável quando o aplicativo terá o recurso reduzido 
2.	Conjunto mínimo de recursos que estarão disponíveis durante o processo de atualização
3.	Recurso de reversão em caso de erros durante a atualização.
4.	Custo total envolvido. Inclui o custo de componentes de aplicativo adicionais necessários para criar uma cópia temporária (como bancos de dados adicionais para replicação geográfica ativa) e custos incrementais para implantações temporárias usadas pelo processo de atualização. 

Se o aplicativo puder operar temporariamente no modo somente leitura, o fluxo de trabalho de atualização poderá ser projetado para eliminar efetivamente o tempo de inatividade no geral. Para entender como implementar o fluxo de trabalho atualização para a sua topologia de aplicativo específica, consulte [Gerenciando atualizações sem interrupção de aplicativos de nuvem usando a Replicação Geográfica Ativa do Banco de Dados SQL](sql-database-manage-application-rolling-upgrade.md).
 
 
 

<!---HONumber=AcomDC_0615_2016-->