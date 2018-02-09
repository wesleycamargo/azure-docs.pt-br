---
title: "Testando a taxa de transferência de rede da VM do Azure | Microsoft Docs"
description: "Saiba como testar a taxa de transferência de rede da máquina virtual do Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: d65b86cc63a4fd39824a6421afd5ce9abb7fd270
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Teste de Largura de Banda/Taxa de Transferência (NTTTCP)

Ao testar o desempenho de taxa de transferência de rede no Azure, é melhor usar uma ferramenta que se destina à rede para teste e que minimiza o uso de outros recursos que poderiam afetar o desempenho. É recomendável usar o NTTTCP.

Copie a ferramenta para duas VMs do Azure do mesmo tamanho. Uma VM funciona como REMETENTE e a outra como RECEPTOR.

#### <a name="deploying-vms-for-testing"></a>Implantando VMs para teste
Para fins deste teste, as duas VMs devem estar no mesmo Serviço de Nuvem ou no mesmo Conjunto de Disponibilidade, para que possamos usar seus IPs internos e excluir os Balanceadores de Carga do teste. É possível testar com o VIP, mas essa variante de teste está fora do escopo deste documento.
 
Anote o endereço IP do RECEPTOR. Vamos chamar esse IP de “a.b.c.r”

Anote o número de núcleos da VM. Vamos chamar isso de “\#num\_cores”
 
Execute o teste NTTTCP durante 300 segundos (ou 5 minutos) na VM remetente e na VM receptora.

Dica: ao configurar esse teste pela primeira vez, você poderá tentar um período de teste mais curto para obter comentários mais rápidos. Depois que a ferramenta estiver funcionando como esperado, estenda o período de teste para 300 segundos para obter os resultados mais precisos.

> [!NOTE]
> O remetente **e** o receptor devem especificar **o mesmo** parâmetro de duração de teste (-t).

Para testar um único fluxo TCP por 10 segundos:

Parâmetros do receptor: ntttcp -r -t 10 -P 1

Parâmetros do remetente: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> A amostra anterior só deve ser usada para confirmar a configuração. Exemplos válidos de teste são abordados adiante neste documento.

## <a name="testing-vms-running-windows"></a>Testando VMs que executam o WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Instale o NTTTCP nas VMs.

Baixe a última versão: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Se preferir, pesquise-a, caso tenha sido movida: <https://www.bing.com/search?q=ntttcp+download>\< – deve ser a primeira ocorrência

Considere colocar o NTTTCP em uma pasta separada, como c:\\tools

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Permitir o NTTTCP pelo firewall do Windows
No RECEPTOR, crie uma regra de Permissão no Firewall do Windows para permitir a chegada do tráfego NTTTCP. É mais fácil permitir todo o programa NTTTCP por nome, em vez de permitir portas TCP de entrada específicas.

Permita o NTTTCP pelo Firewall do Windows desta maneira:

netsh advfirewall firewall add rule program=\<PATH\>\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Por exemplo, se você tiver copiado ntttcp.exe para a pasta “c:\\tools”, este será o comando: 

netsh advfirewall firewall add rule program=c:\\tools\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>Executando testes NTTTCP

Inicie o NTTTCP no RECEPTOR (**execute no CMD**, não no PowerShell):

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

Se a VM tiver quatro núcleos e um endereço IP 10.0.0.4, ela será parecida com esta:

ntttcp -r –m 8,\*,10.0.0.4 -t 300


Inicie o NTTTCP no REMETENTE (**execute no CMD**, não no PowerShell):

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

Aguarde os resultados.


## <a name="testing-vms-running-linux"></a>Testando VMs que executam o LINUX:

Use nttcp-for-linux. Ele está disponível em <https://github.com/Microsoft/ntttcp-for-linux>

Nas VMs Linux (REMETENTE e RECEPTOR), execute estes comandos para preparar o ntttcp-for-linux nas VMs:

CentOS – Instalar o Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu – Instalar o Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Crie e instale nas duas:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Como no exemplo do Windows, vamos supor que o IP do RECEPTOR do Linux é 10.0.0.4

Inicie NTTTCP-for-Linux no RECEPTOR:

``` bash
ntttcp -r -t 300
```

E no REMETENTE, execute:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
O tamanho do teste usa como padrão 60 segundos se nenhum parâmetro de tempo é fornecido

## <a name="testing-between-vms-running-windows-and-linux"></a>Testar entre VMs executando o Windows e LINUX:

Nesses cenários, devemos habilitar o modo sem sincronização para que o teste possa ser executado. Isso é feito utilizando o **sinalizador -N** para Linux e **sinalizadores -ns** para Windows.

#### <a name="from-linux-to-windows"></a>Do Linux para Windows:

Receptor <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Remetente <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Do Windows para Linux:

Receptor <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Remetente <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testando as Instâncias de Serviço de Nuvem:
Você precisa adicionar a seção a seguir em seu ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Próximas etapas
* Dependendo dos resultados, pode haver espaço para [Otimizar a taxa de transferência de rede nos computadores](virtual-network-optimize-network-bandwidth.md) para seu cenário.
* Leia mais sobre como [a largura de banda é alocada a máquinas virtuais] (virtual-machine-network-throughput.md)
* Saiba mais com as [Perguntas frequentes sobre a rede virtual do Azure](virtual-networks-faq.md)
