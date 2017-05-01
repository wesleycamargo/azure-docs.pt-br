---
title: "Visão geral do serviço de metadados de instância do Azure | Microsoft Docs"
description: "Interface RESTful para obter informações sobre a de computação, a rede e os eventos de manutenção futura da máquina virtual."
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Serviço de Metadados de Instância do Azure --visualização

> [!NOTE] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para saber mais, veja [Termos de Uso do Microsoft Azure para Visualizações do Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

O serviço de metadados de instância fornece informações sobre as instâncias de máquina virtual em execução. Essas informações podem ser usadas para gerenciar e configurar suas instâncias no Azure. O serviço de metadados de instância do Azure é um ponto de extremidade RESTful disponível para todas as máquinas virtuais de IaaS criadas por meio do novo [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). O ponto de extremidade está disponível em um endereço IP não roteável conhecido (*169.254.169.254*) que podem ser acessados somente de dentro da máquina virtual. Esse serviço deve fornecer dados importantes sobre a instância de máquina virtual, sua configuração de rede e eventos de manutenção futura. Para obter informações adicionais, veja [categorias de metadados](#instance-metadata-data-categories).

### <a name="important-information"></a>Informações importantes
No momento, este serviço está em **visualização** e hospeda informações sobre instância de máquina virtual e eventos de manutenção futura. O serviço continua a receber atualizações e esta página reflete as [categorias de dados](#instance-metadata-data-categories) específicas disponíveis.


## <a name="service-availability"></a>Disponibilidade do serviço
A visualização atual está disponível na região **Centro-Oeste dos EUA** do Azure. A tabela a seguir está atualizada em regiões em que a visualização do serviço estará disponível.
Para experimentar o Serviço de Metadados de Instância, crie uma VM do [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) ou [portal do Azure](http://portal.azure.com) e siga os exemplos na seção de exemplos para acessar o serviço de metadados. 

Regiões em que a visualização do Serviço de Metadados de Instância está disponível|
------------------------------------------------------------|
Centro-Oeste dos EUA |



## <a name="retrieving-instance-metadata"></a>Recuperação de metadados de instância 

Os metadados de instância estão disponíveis para a execução de máquinas virtuais criadas/gerenciadas usando o [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). Acessar todas as categorias de dados para uma instância de máquina virtual usando o seguinte URI

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

A saída padrão para todos os metadados da instância está no formato json (tipo de conteúdo Application/json)

## <a name="usage-examples"></a>Exemplos de uso
Veja a seguir um conjunto de exemplos e semântica de uso para o serviço de metadados de instância

### <a name="versioning"></a>Controle de versão 
O Serviço de Metadados de Instância tem controle de versão. As versões são obrigatórias e a versão de visualização atual é 2017-03-01.


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

Como adicionamos versões mais recentes, as versões mais antigas ainda podem ser acessadas para fins de compatibilidade se os scripts tiverem dependências de formatos de dados específicos. Observe que a versão de visualização atual poderá não estar disponível quando o serviço estiver totalmente disponível.


### <a name="data-output"></a>Saída de dados
Por padrão, os metadados da instância retornam dados em json (tipo de conteúdo = application/json). Os elementos de nó diferentes podem retornar dados em formatos diferentes do padrão conforme for aplicável. A tabela a seguir é uma referência rápida para formatos de dados 

Elemento | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/instance | json | texto
/scheduledevents | json | nenhum

Para o formato de texto, use format=text na URL da solicitação, por exemplo 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>Segurança
O ponto de extremidade de metadados de instância está acessível somente dentro da instância de máquina virtual em execução em um endereço IP 169.254.169.254 não roteável. Além disso, qualquer solicitação com **Cabeçalho X-Forwarded-For** é rejeitada pelo serviço de metadados. Também exigimos que as solicitações que contém cabeçalho **Metadata:true** sejam enviadas para garantir que a solicitação real tenha sido desejada diretamente e não faça parte de redirecionamento não intencional. 
### <a name="error"></a>Erro
Se houver um elemento de dados não encontrado ou solicitações malformadas, o serviço de metadados da instância retornará o erro de HTTP padrão. Veja a seguir alguns exemplos de códigos de retorno 

Código de retorno de HTTP | Motivo
----------------|-------
200 | OK
400 | Bad Request, missing header, pass -H Metadata:true
404 | Não Encontrado, o elemento solicitado não existe 
405 | Método sem suporte 
429 | Número excessivo de solicitações, atualmente só há suporte para no máximo 5 consultas por segundo

### <a name="examples"></a>Exemplos
#### <a name="retrieving-the-network-information"></a>Recuperação das informações de rede 

**Solicitação**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**Resposta**
> [!NOTE] 
> A resposta é uma cadeia de caracteres json. A saída a seguir é json formatado com o utilitário como [jq](https://stedolan.github.io/jq/).
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>Recuperação do endereço IP público

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recuperação de todos os metadados para uma instância

**Solicitação**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**Resposta**
> [!NOTE]
> A resposta é uma cadeia de caracteres json. A saída a seguir é json formatado com o utilitário como [jq](https://stedolan.github.io/jq/).
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperação de metadados na máquina virtual do Windows

Os metadados de instância podem ser recuperados no Windows por meio do curl do utilitário do Powershell. Em um prompt do Powershell, execute o seguinte comando: 

**Solicitação**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**Resposta**
>[!NOTE]
> A resposta é uma cadeia de caracteres json. A saída a seguir é json formatado com o utilitário ConvertTo-Json do Powershell.
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>Categorias de dados de metadados de instância
A tabela a seguir contém uma lista de todas as categorias de dados disponíveis por meio de metadados da instância

Dados | Descrição
-----|------------
location | Região do Azure em que a máquina virtual está sendo executada 
name | Nome da VM 
oferta | Oferece informações para a imagem da máquina virtual; esses valores estão presentes apenas para imagens implantadas na galeria de imagens do Azure 
publicador | Publicador da imagem da máquina virtual
sku | SKU específica para a imagem da máquina virtual  
version | Versão da imagem da máquina virtual 
osType | Linux ou Windows 
platformUpdateDomain |  [Domínio de atualização](virtual-machines-windows-manage-availability.md) no qual a máquina virtual está sendo executada. 
platformFaultDomain | [Domínio de falha](virtual-machines-windows-manage-availability.md) no qual a máquina virtual está sendo executada.
vmId | Identificador exclusivo para a máquina virtual; mais informações [aqui](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [Tamanho da VM](virtual-machines-windows-sizes.md)
ipv4/ipaddress | Endereço IP local da máquina virtual 
ipv4/publicip | Endereço IP público para a instância 
subnet/address | Endereço para a sub-rede 
subnet/dnsservers/ipaddress1 | Servidor DNS primário
subnet/dnsservers/ipaddress2 | Servidor DNS secundário
subnet/prefix | Prefixo de sub-rede, exemplo 24
ipv6/ipaddress | Endereço IPv6 para a máquina virtual
mac | Endereço mac da máquina virtual 
scheduledevents | consulte [scheduledevents](virtual-machines-scheduled-events.md)



## <a name="example-scenarios-for-usage"></a>Cenários de exemplo para uso  

### <a name="tracking-vm-running-on-azure"></a>VM de controle em execução no Azure 

Como provedor de serviço, você talvez precise controlar o número de máquinas virtuais que executam o seu software ou ter agentes que precisam controlar a exclusividade da máquina virtual. Para obter uma ID exclusiva de uma máquina virtual, use o campo vmId do serviço de metadados de instância 

**Solicitação**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**Resposta**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Posicionamento de contêineres, partições de dados com base em domínio de falha/atualização 

Para determinados cenários onde o posicionamento de réplicas diferentes é de vital importância. Por exemplo, o [posicionamento de réplica de HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou para o posicionamento do contêiner por meio de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/), você precisa conhecer a platformFaultDomain e a platformUpdateDomain nas quais a máquina virtual está sendo executada. Você pode consultar esse ponto de dados diretamente por meio de metadados da instância

**Solicitação**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**Resposta**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtendo mais informações sobre a máquina virtual durante a ocorrência de suporte 

Como provedor de serviços, você poderá receber uma chamada de suporte onde gostaria de saber mais informações sobre a máquina virtual. Pedir ao cliente para informar os metadados de computação pode fornecer informações básicas para o profissional de suporte saber o tipo de VM no Azure. 

**Solicitação**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**Resposta**
> [!NOTE] 
> A resposta é uma cadeia de caracteres json. A saída a seguir é json formatado com o utilitário como [jq](https://stedolan.github.io/jq/).
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }

}
```

## <a name="faq"></a>Perguntas frequentes
1. Estou recebendo o erro 400 Solicitação inválida, o cabeçalho de metadados necessários não foi especificado. O que isso significa?
   * O serviço de metadados de instância exige que o cabeçalho Metadata:true seja passado na solicitação. Passar o cabeçalho na chamada de REST permite acessar o serviço de metadados de instância. 
2. Por que não estou obtendo informações de computação para minha máquina virtual?
   * Atualmente, o serviço de metadados de instância oferece suporte apenas a instâncias criadas pelo Azure Resource Manager; no futuro, poderemos adicionar suporte para máquinas virtuais dos serviços de nuvem. 
3. Criei minha máquina virtual com o Azure Resource Manager há algum tempo. Por que não consigo ver as informações de metadados de computação?
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../azure-resource-manager/resource-group-using-tags.md) para começar a ver os metadados de computação. Para máquinas virtuais mais antigas (criadas antes de setembro de 2016), adicione ou remova extensões ou dados de discos à máquina virtual para atualizar os metadados.
4. Por que estou recebendo o Erro 500 - Erro interno do servidor?
   * Atualmente a visualização de metadados da instância está disponível apenas na região Centro-Oeste dos EUA. Implante suas VMs em regiões com suporte.  
4. Onde posso publicar comentários/perguntas adicionais?
   * Envie seus comentários em feedback.azure.com.
    
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [scheduledevents] (virtual-machines-scheduled-events.md)

