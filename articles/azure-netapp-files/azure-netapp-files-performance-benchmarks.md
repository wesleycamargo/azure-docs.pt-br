---
title: Avaliações de desempenho do arquivos do Azure NetApp | Microsoft Docs
description: Descreve os resultados dos testes de parâmetro de comparação de desempenho para arquivos do Azure NetApp no nível do volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870876"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Avaliações de desempenho do arquivos do Azure NetApp

Este artigo descreve os resultados de testes de parâmetro de comparação de desempenho para arquivos do Azure NetApp no nível do volume. 

## <a name="sample-application-used-for-the-tests"></a>Aplicativo de exemplo usado para os testes

Testes de desempenho foram executados com um aplicativo de exemplo usando arquivos do Azure NetApp. O aplicativo tem as seguintes características: 

* Um aplicativo baseado em Linux criado para a nuvem
* Pode dimensionar linearmente com adicionado VMs (máquinas virtuais) para aumentar a potência de computação conforme necessário
* Requer a acessibilidade rápida do data lake
* Tem padrões de e/s que às vezes são aleatórios e, às vezes, sequencial 
    * Um padrão aleatório requer baixa latência para grandes quantidades de e/s. 
    * Um padrão sequencial requer grandes quantidades de largura de banda. 

## <a name="about-the-workload-generator"></a>Sobre o gerador de carga de trabalho

Os resultados são provenientes Vdbench arquivos de resumo. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) é um utilitário de linha de comando que gera cargas de trabalho de e/s de disco para validar o desempenho de armazenamento. A configuração de cliente-servidor usada é escalonável.  Ele inclui um único combinados mestre/do cliente e 14 VMs de cliente dedicados.

## <a name="about-the-tests"></a>Sobre os testes

Os testes foram projetados para identificar os limites que o aplicativo de exemplo pode ter e o tempo de resposta que curvas até os limites.  

Os seguintes testes foram executados: 

* 100% 8 KiB aleatório de leitura
* 100% gravação aleatória de KiB 8
* 100% 64 KiB sequencial de leitura
* 100% 64 KiB sequencial de gravação
* 50% 64 KiB sequencial de leitura, gravação sequencial do 50% 64 KiB
* 50%, de 50% 8 KiB aleatório de leitura gravação aleatória de KiB 8

## <a name="bandwidth"></a>Largura de banda

Os arquivos do Azure do NetApp oferece vários [níveis de serviço](azure-netapp-files-service-levels.md). Cada nível de serviço oferece uma quantidade diferente de largura de banda por TiB de capacidade provisionada (cota do volume). O limite de largura de banda para um volume é provisionado com base na combinação de nível de serviço e a cota do volume. Observe que o limite de largura de banda é apenas um fator para determinar a quantidade real de taxa de transferência que será obtida.  

Atualmente, o MiB 4.500 é a taxa de transferência mais alta que foi obtida com uma carga de trabalho em relação a um único volume no teste.  Com o nível de serviço Premium, uma cota do volume de TiB 70.31 provisionará largura de banda suficiente para perceber essa taxa de transferência por cálculo abaixo: 

![Fórmula de largura de banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Nível de serviço e cota](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Cargas de trabalho intensivas de taxa de transferência

O teste de taxa de transferência usada Vdbench e uma combinação de VMs de armazenamento do 12xD32s V3. O volume de exemplo no teste obteve os seguintes números de taxa de transferência:

![Teste de taxa de transferência](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Cargas de trabalho de e/S intensiva

O teste de e/s usado Vdbench e uma combinação de VMs de armazenamento do 12xD32s V3. O volume de exemplo no teste obtido os números de e/s a seguir:

![Teste de e/s](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

A distância entre as VMs de teste e o volume de arquivos do Azure NetApp tem um impacto sobre o desempenho de e/s.  A tabela abaixo compara o IOPS versus curvas de resposta de latência para dois conjuntos diferentes de VMs.  Um conjunto de VMs é quase NetApp serviço arquivos do Azure e o outro conjunto é ainda mais longe.  Observe que o aumento da latência para o conjunto adicional de VMs tem um impacto na quantidade de IOPS obtido em um determinado nível de paralelismo.  Independentemente disso, leituras em um volume podem exceder 300.000 IOPS, conforme ilustrado abaixo: 

![Estudo de latência](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Resumo

Cargas de trabalho sensíveis à latência (bancos de dados) podem ter um tempo de resposta um milissegundo. O desempenho transacional pode ser mais de 300 mil IOPS para um único volume.

Aplicativos sensíveis à taxa de transferência (para streaming e geração de imagens) podem ter 4.5GiB / s de produtividade.
