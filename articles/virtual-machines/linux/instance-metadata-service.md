---
title: Serviço de Metadados de Instância do Azure | Microsoft Docs
description: A Interface RESTful para obter informações sobre a computação, a rede e os eventos de manutenção futura da VM do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: 8a7a58581133d98738403bee2e659fae056e1a7f
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782474"
---
# <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância do Azure


O Serviço de metadados de instância do Azure fornece informações sobre instâncias da máquina virtual em execução que podem ser usadas para gerenciar e configurar suas máquinas virtuais.
Isso inclui informações como SKU, configuração de rede e eventos de manutenção futura. Para obter mais informações sobre o tipo de informação que está disponível, consulte [categorias de metadados](#instance-metadata-data-categories).

O Serviço de Metadados da Instância do Azure é um Ponto de Extremidade REST disponível para todas as VMs IaaS criadas por meio do [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). O ponto de extremidade está disponível em um endereço IP não roteável conhecido (`169.254.169.254`) que pode ser acessado somente de dentro da máquina virtual.

> [!IMPORTANT]
> Esse serviço está **disponível para o público geral** nas Regiões do Azure.  Regularmente, ele recebe atualizações para expor informações novas sobre instâncias de máquina virtual. Esta página reflete as atualizadas [categorias de dados](#instance-metadata-data-categories) disponíveis.

## <a name="service-availability"></a>Disponibilidade do serviço
Esse serviço está disponível nas regiões do Azure disponíveis para o público geral. Nem todas as versões de API podem estar disponíveis em todas as regiões do Azure.

Regiões                                        | Disponibilidade?                                 | Versões com suporte
-----------------------------------------------|-----------------------------------------------|-----------------
[Todas as regiões globais do Azure disponíveis](https://azure.microsoft.com/regions/)     | Disponível   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02
[Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)              | Disponível | 2017-04-02, 2017-08-01, 2017-12-01
[Azure China:](https://www.azure.cn/)                                                           | Disponível | 2017-04-02, 2017-08-01, 2017-12-01
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | Disponível | 2017-04-02, 2017-08-01, 2017-12-01

Esta tabela é atualizada quando há atualizações de serviço e/ou novas versões com suporte estão disponíveis

Para testar o serviço de metadados de instância, crie uma VM do [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou [portal do Azure](http://portal.azure.com) nas regiões acima e siga os exemplos abaixo.

## <a name="usage"></a>Uso

### <a name="versioning"></a>Controle de versão
O Serviço de Metadados de Instância tem controle de versão. As versões são obrigatórias e a versão atual no Azure Global é `2018-04-02`. Versões atuais com suporte (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02)

> [!NOTE] 
> Versões de visualização anteriores de eventos agendados compatíveis {mais recentes} como a api-version. Esse formato não é mais suportado e será substituído no futuro.

Conforme versões mais recentes são adicionadas, as versões mais antigas ainda podem ser acessadas para fins de compatibilidade se os scripts tiverem dependências de formatos de dados específicos. Entretanto, a versão prévia anterior (2017-03-01) poderá não estar disponível quando o serviço estiver totalmente disponível.

### <a name="using-headers"></a>Uso de cabeçalhos
Ao consultar o Serviço de Metadados você deverá fornecer o cabeçalho `Metadata: true` para garantir que a solicitação não seja redirecionada de forma involuntária.

### <a name="retrieving-metadata"></a>Configurando e recuperando os metadados

Os metadados de instância estão disponíveis para a execução de máquinas virtuais criadas/gerenciadas usando o [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Acessar todas as categorias de dados para uma instância de máquina virtual usando a seguinte solicitação:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Todas as consultas de metadados de instância diferenciam maiúsculas de minúsculas.

### <a name="data-output"></a>Saída de dados
Por padrão, o serviço de metadados de instância retorna dados em formato JSON (`Content-Type: application/json`). No entanto, diferentes APIs retornam dados em formatos diferentes, se solicitado.
A tabela a seguir é uma referência de outros formatos de dados que pode oferecer suporte a APIs.

API | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/instance | json | texto
/scheduledevents | json | nenhum

Para acessar um formato de resposta não padrão, especifique o formato solicitado como um parâmetro querystring na solicitação. Por exemplo:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Segurança
O ponto de extremidade de metadados de instância está acessível somente dentro da instância de máquina virtual em execução em um endereço IP não roteável. Além disso, qualquer solicitação com `X-Forwarded-For`Cabeçalho é rejeitada pelo serviço.
As solicitações também devem conter um `Metadata: true` cabeçalho para garantir que a solicitação real tenha sido desejada diretamente e não faça parte de um redirecionamento não intencional. 

### <a name="error"></a>Erro
Se houver um elemento de dados não encontrado ou solicitações malformadas, o serviço de metadados da instância retornará o erro de HTTP padrão. Por exemplo:

Código de status HTTP | Motivo
----------------|-------
200 OK |
400 Solicitação Inválida | Faltando `Metadata: true` cabeçalho
404 Não Encontrado | O elemento solicitado não existe 
405 método não permitido | Somente as solicitações `GET` e `POST` são suportadas
429 Número excessivo de solicitações | A API atualmente suporta um máximo de 5 consultas por segundo
500 Erro do serviço     | Aguarde um pouco e tente novamente

### <a name="examples"></a>Exemplos

> [!NOTE] 
> Todas as respostas de API são cadeias de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

#### <a name="retrieving-network-information"></a>Recuperação das informações de rede

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Recuperação do endereço IP público

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recuperação de todos os metadados para uma instância

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperação de metadados na máquina virtual do Windows

**Solicitação**

Os metadados de instância podem ser recuperados no Windows por meio do utilitário do Powershell`curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Ou por meio de `Invoke-RestMethod` cmdlet:
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Categorias de dados de metadados de instância
As seguintes categorias de dados estão disponíveis por meio do serviço de metadados da instância:

Dados | DESCRIÇÃO | Versão introduzida 
-----|-------------|-----------------------
location | Região do Azure na qual a máquina virtual está sendo executada | 2017-04-02 
Nome | Nome da VM | 2017-04-02
oferta | Oferece informações para a imagem VM. Esse valor só está presente para as imagens implantadas na Galeria de imagens do Azure. | 2017-04-02
publicador | Publicador da imagem da máquina virtual | 2017-04-02
sku | SKU específica para a imagem da máquina virtual | 2017-04-02
version | Versão da imagem da máquina virtual | 2017-04-02
osType | Linux ou Windows | 2017-04-02
platformUpdateDomain |  [Domínio de atualização](manage-availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
platformFaultDomain | [Domínio de falha](manage-availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual | 2017-04-02
vmSize | [Tamanho da VM](sizes.md) | 2017-04-02
subscriptionId | Assinatura do Azure para a Máquina Virtual | 2017-08-01
marcas | [Marcas](../../azure-resource-manager/resource-group-using-tags.md) para a sua Máquina Virtual  | 2017-08-01
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md) para a sua Máquina Virtual | 2017-08-01
placementGroupId | [Grupo de Posicionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do conjunto de dimensionamento da sua Máquina Virtual | 2017-08-01
plan | [Plano] (https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/createorupdate#plan) para uma VM em uma imagem do Azure Marketplace, contém nome, produto e editor | 2017-04-02
publicKeys | Coleção de Chaves Públicas [https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/createorupdate#sshpublickey] atribuídas à VM e aos caminhos | 2017-04-02
vmScaleSetName | [Nome do ScaleSet de Máquinas Virtuais] (../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de seu conjunto de dimensionamento de máquinas virtuais | 2017-12-01
zona | [Zona de Disponibilidade](../../availability-zones/az-overview.md) da máquina virtual | 2017-12-01 
IPv4/privateIpAddress | Endereço IPv4 local da máquina virtual | 2017-04-02
IPv4/privateIpAddress | Endereço IPv4 local da máquina virtual | 2017-04-02
subnet/address | Endereço sub-rede da máquina virtual | 2017-04-02 
subnet/prefix | Prefixo de sub-rede, exemplo 24 | 2017-04-02 
ipv6/ipAddress | Endereço IPv6 local da máquina virtual | 2017-04-02 
macAddress | Endereço mac da máquina virtual | 2017-04-02 
scheduledevents | Consulte [Eventos agendados](scheduled-events.md) | 2017-08-01
identidade | Identidade de Serviço Gerenciada (versão prévia) Veja [adquirir um token de acesso](../../active-directory/managed-service-identity/how-to-use-vm-token.md) | 01-02-2018 

## <a name="example-scenarios-for-usage"></a>Cenários de exemplo para uso  

### <a name="tracking-vm-running-on-azure"></a>VM de controle em execução no Azure

Como provedor de serviço, você talvez precise controlar o número de máquinas virtuais que executam o seu software ou ter agentes que precisam controlar a exclusividade da máquina virtual. Para obter uma ID exclusiva de uma máquina virtual, use o `vmId` campo do serviço de metadados de instância.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Resposta**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Posicionamento de contêineres, partições de dados com base em domínio de falha/atualização 

Para determinados cenários nos quais o posicionamento de réplicas diferentes é de vital importância. Por exemplo, o [posicionamento de réplica de HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou o posicionamento do contêiner por meio de um [organizador](https://kubernetes.io/docs/user-guide/node-selection/) podem exigir que você conheça os `platformFaultDomain` e `platformUpdateDomain` nas quais a máquina virtual está sendo executada.
Também aproveite as [Zonas de Disponibilidade](../../availability-zones/az-overview.md) das instâncias para tomar essas decisões. Você pode consultar esses dados diretamente por meio de serviço de metadados.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Resposta**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtendo mais informações sobre a máquina virtual durante a ocorrência de suporte 

Como provedor de serviços, você poderá receber uma chamada de suporte na qual gostaria de ter mais informações sobre a máquina virtual. Pedir ao cliente para informar os metadados de computação pode fornecer informações básicas para o profissional de suporte saber o tipo de VM no Azure. 

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Obter o Ambiente do Azure em que a VM está em execução 

O Azure tem várias nuvens soberanas, como [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/), e algumas vezes o Ambiente do Azure é necessário para tomar algumas decisões em tempo de execução. O exemplo a seguir mostrará como é possível conseguir isso

**Solicitação**

> [!NOTE] 
> Requer que o jq seja instalado. 

```bash
  metadata=$(curl "http://169.254.169.254/metadata/instance/compute?api-version=2018-02-01" -H "Metadata:true")
  endpoints=$(curl "https://management.azure.com/metadata/endpoints?api-version=2017-12-01")
 
  location=$(echo $metadata | jq .location -r)
 
  is_ww=$(echo $endpoints | jq '.cloudEndpoint.public.locations[]' -r | grep -w $location)
  is_us=$(echo $endpoints | jq '.cloudEndpoint.usGovCloud.locations[]' -r | grep -w $location)
  is_cn=$(echo $endpoints | jq '.cloudEndpoint.chinaCloud.locations[]' -r | grep -w $location)
  is_de=$(echo $endpoints | jq '.cloudEndpoint.germanCloud.locations[]' -r | grep -w $location)
 
  environment="Unknown"
  if [ ! -z $is_ww ]; then environment="AzureCloud"; fi
  if [ ! -z $is_us ]; then environment="AzureUSGovernment"; fi
  if [ ! -z $is_cn ]; then environment="AzureChinaCloud"; fi
  if [ ! -z $is_de ]; then environment="AzureGermanCloud"; fi
 
  echo $environment
```


### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exemplos de como chamar o serviço de metadados usando diferentes idiomas dentro da VM 

Linguagem | Exemplo 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata
    

## <a name="faq"></a>Perguntas frequentes
1. Estou recebendo o erro `400 Bad Request, Required metadata header not specified`. O que isso significa?
   * O serviço de metadados de instância exige que o cabeçalho `Metadata: true` seja passado na solicitação. Passar o cabeçalho na chamada de REST permite acessar o serviço de metadados de instância. 
2. Por que não estou obtendo informações de computação para minha máquina virtual?
   * Atualmente o serviço de metadados de instância suporta apenas instâncias criadas com o Gerenciador de recursos do Azure. No futuro, o suporte para VMs de serviço de nuvem pode ser adicionado.
3. Criei minha máquina virtual com o Azure Resource Manager há algum tempo. Por que não consigo ver as informações de metadados de computação?
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver os metadados de computação. Para máquinas virtuais mais antigas (criadas antes de setembro de 2016), adicione ou remova extensões ou dados de discos à máquina virtual para atualizar os metadados.
4. Não estou vendo todos os dados preenchidos para a nova versão
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver os metadados de computação. Para máquinas virtuais mais antigas (criadas antes de setembro de 2016), adicione ou remova extensões ou dados de discos à máquina virtual para atualizar os metadados.
5. Por que estou recebendo o erro `500 Internal Server Error`?
   * Repita a solicitação com base no sistema de retirada exponencial. Se o problema persistir, contate o suporte do Azure.
6. Onde posso publicar comentários/perguntas adicionais?
   * Envie seus comentários em http://feedback.azure.com.
7. Isso funcionaria para Instância do Conjunto de Dimensionamento da Máquina Virtual?
   * Sim, o serviço de metadados está disponível para instâncias de conjunto de escala. 
8. Como posso obter suporte para o serviço?
   * Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a máquina virtual na qual você não consegue obter resposta de metadados após várias tentativas 
9. Eu recebo o tempo limite da solicitação para minha chamada no serviço?
   * Chamadas de metadados devem ser feitas do endereço IP principal atribuído à placa de rede da VM, além disso, caso você tenha alterado lá suas rotas devem ser uma rota para o endereço de 169.254.0.0/16 fora de sua placa de rede.
10. Atualizei minhas marcas no conjunto de dimensionamento de máquinas virtuais, mas elas não aparecem nas instâncias, ao contrário das VMs.
   * Atualmente, para marcas de ScaleSets são exibidas apenas na VM em um reiníco/ao refazer imagem/ou uma alteração de disco na instância. 

   ![Serviço de Metadados de Instância](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Eventos Agendados](scheduled-events.md)

