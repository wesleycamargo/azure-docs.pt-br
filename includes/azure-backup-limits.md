<properties
   pageTitle="Tabela de limites de Backup do Azure"
   description="Descreve os limites do sistema para Backup do Azure."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags  ms.service="backup" ms.devlang="NA" ms.topic="article" ms.tgt_pltfrm="NA" ms.workload="TBD" ms.date="08/26/2015" ms.author="jimpark"; "aashishr" />


Os limites a seguir se aplicam ao Backup do Azure.

| Identificador de limite | Limite padrão |
|---|---|
|Número de servidores/computadores que podem ser registrados em cada cofre|50|
|Tamanho de uma fonte de dados para dados armazenados no armazenamento de cofre do Azure|54\.400 GB máx.<sup>1</sup>|
|Número de cofres de backup que podem ser criados em cada assinatura do Azure|25|
|Número de vezes que o backup pode ser agendado por dia|3 por dia para o Windows Server/cliente<br/>2 por dia para SCDPM|
|Número de pontos de recuperação que podem ser criados|366<sup>2</sup>|
|Discos de dados conectados a uma máquina virtual do Azure para backup|5|

- <sup>1</sup>O limite de 54.400 GB não se aplica ao backup de VM de IaaS.
- <sup>2</sup>Você pode usar qualquer permutação para chegar a um número que seja menor do que 366.

<!---HONumber=Sept15_HO2-->