---
title: "Instalar SAP HANA em SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Como instalar SAP HANA em um SAP HANA no Azure (Instância Grande)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 3827e74433a859e65071a1041c5ed6c8253db8df
ms.openlocfilehash: be17b2de227ef6d93f8d326ff060c3c4788eb622


---
# <a name="install-sap-hana-on-sap-hana-on-azure-large-instances"></a>Instalar o SAP HANA no SAP HANA no Azure (Instâncias Grandes)

A instalação do SAP HANA é responsabilidade sua, e você pode fazer isso imediatamente após a entrega de um novo SAP HANA no servidor do Azure (Instâncias Grandes). Observe que, de acordo com a política do SAP, a instalação do SAP HANA deve ser executada pelo instalador do certificado do SAP HANA (alguém que tenha sido aprovado no exame de certificação de instalação Certified SAP Technology Associate – SAP HANA) ou por um SI (integrador de sistema) certificado pela SAP.

Há considerações de conectividade específicas relacionadas ao SAP HANA (lado do servidor) e ao SAP HANA (lado do cliente) que precisam ser levadas em conta. Em muitos casos, o servidor SAP HANA envia seu endereço IP ao cliente em que está armazenado em cache e a utiliza para tentativas de conexão subsequentes. Como o SAP HANA no Azure (Instâncias Grandes) utiliza NAT para o endereço IP do servidor interno usado na rede do locatário para um intervalo de endereços IP fornecido para VNets do Azure especificadas, por design, o servidor de banco de dados do SAP HANA envia o intervalo de endereços IP &quot;interno&quot;. Por exemplo, para a resolução de nome de host, em vez de o HANA SAP fornecer o endereço IP NATed, é usado o endereço IP interno em cache. Portanto, um aplicativo que usa um cliente SAP HANA (ODBC, JDBC etc.) não seria capaz de se conectar com esse endereço IP. Para instruir o servidor SAP HANA a propagar o endereço IP NATed para o cliente, o arquivo de configuração do sistema global SAP HANA (global.ini) deve ser editado.

Adicione o seguinte a global.ini (diretamente ou por meio do SAP HANA Studio):
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
Confira a seção _Mapeamento de nomes de host para acesso de cliente de banco de dados_ no [Guia de Administração do SAP HANA](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf) para obter mais detalhes e exemplos.

No lado do cliente (para servidores de aplicativos SAP no Azure), edite o arquivo /etc/hosts (no Linux) ou /system32/drivers/etc/hosts (no Windows Server) e inclua uma entrada para o nome do host do SAP HANA no locatário do Azure (Instâncias Grandes) e seu respectivo endereço IP NATed.

>[!NOTE] 
>Ao instalar o cliente de banco de dados do SAP HANA, se houver erros durante a conexão ao SAP HANA no Azure (Instâncias Grandes) durante a instalação do Cliente de Banco de Dados do SAP HANA, use o endereço IP NAT do locatário de Instâncias Grandes HANA implicitamente, em vez do nome do host.

## <a name="storage"></a>Armazenamento

O layout de armazenamento do SAP HANA no Azure (Instâncias Grandes) é configurado pelo SAP HANA no Gerenciamento de Serviços do Azure por meio das práticas recomendadas do SAP. Confira o white paper [Requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As configurações exatas de armazenamento para diferentes Instâncias Grandes do HANA é semelhante a:

| Tamanho da memória | HANA/data | HANA/log | HANA/compartilhado | HANA/log/backups |
| --- | --- | --- | --- | --- |
| 768 GB | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1536 GB | 3456 GB | 768 GB | 1024 GB | 768 GB |
| 3072 GB | 7680 GB | 1536 GB | 1024 GB | 1536 GB |

Além disso, os clientes podem adquirir capacidade de armazenamento adicional em incrementos de 1 TB. Esse armazenamento adicional pode ser incluído como novos volumes para Instâncias Grandes HANA.

O controlador de armazenamento e os nós nos carimbos de Instância Grande são sincronizados com os servidores NTP. Quando você sincroniza SAP HANA em unidades do Azure (Instâncias Grandes) e VMs do Azure em relação a um servidor NTP, não deve haver um descompasso de tempo significativo entre a infraestrutura e as unidades de computação no Azure ou carimbos de Instância Grande.

## <a name="operating-system"></a>Sistema operacional

[SUSE Linux Enterprise Server 12 SP1 para aplicativos SAP](https://www.suse.com/products/sles-for-sap/hana) é a distribuição do Linux instalada para SAP HANA no Azure (Instâncias Grandes). Essa distribuição particular fornece recursos específicos do SAP &quot;prontos para uso&quot; (incluindo parâmetros predefinidos para executar o SAP em SLES de forma eficaz).

Confira [Biblioteca de recursos/White Papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SCN (Rede da Comunidade SAP) para obter vários recursos úteis relacionados à implantação do SAP HANA em SLES (incluindo configuração de alta disponibilidade, proteção de segurança específica para operações do SAP e muito mais).

Links adicionais e úteis relacionados a SLES:

- [SAP HANA no Site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Práticas recomendadas para SAP: replicação de enfileiramento – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – proteção antivírus de SLES para SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicativos SAP)

Notas de suporte do SAP aplicáveis à implementação do SAP HANA no SLES 12 SP1:

- [Observação de suporte SAP 1944799 – diretrizes SAP HANA para instalação do sistema operacional SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [Observação de suporte SAP 2205917 – configurações do SO recomendadas para o banco de dados do SAP HANA para SLES 12 para aplicativos SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Observação de suporte SAP 1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
- [Observação de suporte SAP 171356 – software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
- [Observação de suporte SAP 1391070 – soluções UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>Sincronização de horário

O SAP é muito sensível a diferenças de hora para os vários componentes que compõem o sistema SAP. Os despejos de memória curtos do SAP ABAP com o título de erro ZDATE\_LARGE\_TIME\_DIFF provavelmente são familiares se você trabalha com o SAP (Base) há muito tempo, pois esses despejos curtos aparecem quando a hora do sistema de servidores diferentes ou VMs se torna muito diferente.

Para SAP HANA no Azure (instâncias grandes), a sincronização de tempo feita no Azure não se aplica a unidades de computação nos carimbos de data/hora de Instâncias Grandes. Isso não é aplicável para executar aplicativos SAP de forma nativa no Azure (em VMs), pois o Azure garante que a hora do sistema seja sincronizada corretamente. Como resultado, deve ser configurado um servidor de tempo separado que possa ser usado pelos servidores de aplicativos SAP em execução em VMs do Azure e instâncias de banco de dados HANA SAP em execução em instâncias grandes HANA. A infraestrutura de armazenamento em carimbos de Instância Grande tem seu horário sincronizado com os servidores NTP.





<!--HONumber=Dec16_HO2-->


