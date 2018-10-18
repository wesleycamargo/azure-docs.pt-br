---
title: Arquitetura de armazenamento do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Arquitetura de rede de como implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 614d6aef4a2b7be551574fd3c8e25e2a3e3c1c07
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44030180"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Arquitetura de armazenamento do SAP HANA (Instâncias Grandes)

O layout de armazenamento para o SAP HANA do Azure (Instâncias Grandes) é configurado pelo SAP HANA no modelo de implantação clássico por meio das diretrizes recomendadas do SAP. As diretrizes estão documentadas no white paper [Requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

O SAP HANA em Instâncias Grandes da classe do Tipo I é fornecido com quatro vezes o volume de memória como volume de armazenamento. Para a classe do Tipo II de unidades do HANA em Instâncias Grandes, o armazenamento não é quatro vezes maior. As unidades são fornecidas com um volume destinado ao armazenamento de backups do log de transações do HANA. Para obter mais informações, consulte [ Instalar e configurar o SAP HANA (Instâncias Grandes) do Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte a tabela a seguir em termos de alocação de armazenamento. A tabela lista a capacidade aproximada dos diferentes volumes fornecidos com as diferentes unidades do SAP HANA Instâncias Grandes.

| SKU de Instância Grande do HANA | hana/data | Hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3.328 GB | 768 GB |1.280 GB | 768 GB |
| S192 | 4.608 GB | 1.024 GB | 1.536 GB | 1.024 GB |
| S192m | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S192xm |  11.520 GB |  1.536 GB |  1.792 GB |  1.536 GB |
| S384 | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S384m | 12.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xm | 16.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xxm |  20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576m | 20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576xm | 31.744 GB | 4.096 GB | 2.048 GB | 4.096 GB |
| S768m | 28.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S768xm | 40.960 GB | 6.144 GB | 4.096 GB | 6.144 GB |
| S960m | 36.000 GB | 4.100 GB | 2.050 GB | 4.100 GB |


Os volumes implantados reais podem variar com base na implantação e na ferramenta utilizada para mostrar os tamanhos do volume.

Se você subdividir uma SKU do SAP HANA em Instâncias Grandes, alguns exemplos de possíveis divisões poderão parecer:

| Partição de memória em GB | hana/data | Hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| 1.024 | 1.792 GB | 640 GB | 1.024 GB | 640 GB |
| 1.536 | 3.328 GB | 768 GB | 1.280 GB | 768 GB |


Esses tamanhos são números de volume aproximado que podem variar um pouco com base na implantação e nas ferramentas utilizadas para examinar os volumes. Há também outros tamanhos de partição, como 2,5 TB. Esses tamanhos de armazenamento são calculados com uma fórmula semelhante àquela usada para as partições anteriores. O termo "partições" não significa que o sistema operacional, a memória ou os recursos de CPU sejam de alguma forma particionados. Ele indica as partições de armazenamento para as diferentes instâncias do HANA que talvez você quira implantar em uma única unidade do SAP HANA em Instâncias Grandes. 

Talvez seja necessário mais armazenamento. É possível adicionar armazenamento, comprando armazenamento adicional em unidades de 1 TB. Esse armazenamento adicional pode ser adicionado como volume adicional. Também pode ser usado para estender um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes conforme originalmente implantados e principalmente documentados pelas tabelas anteriores. Também não é possível alterar os nomes dos volumes ou nomes de montagem. Os volumes de armazenamento descritos anteriormente são anexados às unidades do SAP HANA em Instâncias Grandes como volumes NFS4.

É possível usar instantâneos de armazenamento para fins de backup, restauração e recuperação de desastre. Para obter mais informações, consulte [Alta disponibilidade e recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte os [cenários suportados pela HLI](hana-supported-scenario.md) para obter detalhes do layout de armazenamento para seu cenário.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Executar várias instâncias do SAP HANA em uma unidade do SAP HANA em Instâncias Grandes

É possível hospedar mais de uma instância ativa do SAP HANA das unidades do SAP HANA em Instâncias Grandes. Para fornecer os recursos de instantâneos de armazenamento e recuperação de desastre, essa configuração requer um conjunto de volumes por instância. Atualmente, as unidades do SAP HANA em Instâncias Grandes podem ser subdivididas da seguinte maneira:

- **S72, S72m, S144, S192**: em incrementos de 256 GB, com 256 GB, a menor unidade inicial. Diferentes incrementos, como 256 GB e 512 GB, podem ser combinados ao máximo da memória da unidade.
- **S144m e S192m**: em incrementos de 256 GB, com 512 GB, a menor unidade. Diferentes incrementos, como 512 GB e 768 GB, podem ser combinados ao máximo da memória da unidade.
- **Classe do Tipo II**: em incrementos de 512 GB, com a menor unidade inicial de 2 TB. Diferentes incrementos, como 512 GB, 1 TB e 1,5 TB, podem ser combinados ao máximo da memória da unidade.

Alguns exemplos de execução de várias instâncias do SAP HANA podem ser semelhantes aos a seguir.

| SKU | Tamanho da memória | Tamanho do armazenamento | Tamanhos com vários bancos de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x instância de 768 GB do HANA<br /> ou 1x instância de 512 GB + 1x instância de 256 GB<br /> ou 3x instâncias de 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x instâncias do HANA de 512GB<br />ou 1x instância de 512 GB + 1x instância de 1 TB<br />ou 6x instâncias de 256 GB<br />ou 1x instância de 1,5 TB | 
| S192m | 4 TB | 16 TB | 8x instâncias de 512 GB<br />ou 4x instâncias de 1 TB<br />ou 4x instâncias de 512 GB + 2x instâncias de 1 TB<br />ou 4x instâncias de 768 GB + 2x instâncias de 512 GB<br />ou 1x instância de 4 TB |
| S384xm | 8 TB | 22 TB | 4x instâncias de 2 TB<br />ou 2x instâncias de 4 TB<br />ou 2x instâncias de 3 TB + 1x instância de 2 TB<br />ou 2x instâncias de 2,5 TB + 1x instância de 3 TB<br />ou 1x instância de 8 TB |


Também há outras variações. 

## <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso
O armazenamento usado para o SAP HANA em Instâncias Grandes permite uma criptografia transparente dos dados conforme são armazenados nos discos. Quando uma unidade do SAP HANA em Instâncias Grandes é implantada, é possível ativar esse tipo de criptografia. Além disso, será possível alterar para volumes criptografados após a implantação. A passagem de volumes não criptografados para volumes criptografados é transparente e não requer tempo de inatividade. 

Com a classe do Tipo I de SKUs, o volume no qual o LUN de inicialização está armazenado é criptografado. Para a classe do Tipo II de SKUs do SAP HANA em Instâncias Grandes, é necessário criptografar o LUN de inicialização com os métodos do SO. Para obter mais informações, contate a equipe de Gerenciamento de Serviços da Microsoft.

**Próximas etapas**
- Veja [Cenários com suporte para HANA em Instâncias Grandes](hana-supported-scenario.md)