---
title: "Serviço de Metadados de Instância do Azure para Windows | Microsoft Docs"
description: "A Interface RESTful para obter informações sobre a de computação, a rede e os eventos de manutenção futura da VM do Windows."
services: virtual-machines-windows
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: harijay
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 55b97b89cb297dc08dc73f6714c5159d4565a97c
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---

# <a name="azure-instance-metadata-service-for-windows-vms"></a>Serviço Metadados da Instância do Azure para máquinas virtuais do Windows


O Serviço de metadados de instância do Azure fornece informações sobre instâncias da máquina virtual em execução que podem ser usadas para gerenciar e configurar suas máquinas virtuais.
Isso inclui informações como SKU, configuração de rede e eventos de manutenção futura. Para obter mais informações sobre o tipo de informação que está disponível, consulte [categorias de metadados](#instance-metadata-data-categories).

O serviço de metadados de instância do Azure é um ponto de extremidade REST disponível para todas as máquinas virtuais de IaaS criadas por meio [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). O ponto de extremidade está disponível em um endereço IP não roteável conhecido (`169.254.169.254`) que pode ser acessado somente de dentro da máquina virtual.



> [!IMPORTANT]
> Esse serviço é **disponível geralmente** em regiões do Azure globais. Ele está em visualização pública para o governo, China e em nuvem alemã do Azure. Regularmente, ele recebe atualizações para expor informações novas sobre instâncias de máquina virtual. Esta página reflete as atualizadas [categorias de dados](#instance-metadata-data-categories) disponíveis.



## <a name="service-availability"></a>Disponibilidade do serviço
Esse serviço é disponível geralmente em regiões do Azure globais. O serviço está em visualização pública nas regiões governamentais, China ou Alemanha.

Regiões                                        | Disponibilidade?
-----------------------------------------------|-----------------------------------------------
[Todas as regiões globais do Azure disponíveis](https://azure.microsoft.com/regions/)     | Disponível 
[Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)              | Na visualização 
[Azure China:](https://www.azure.cn/)                                                           | Na visualização
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | Na visualização

Esta tabela é atualizada quando o serviço está disponível em outras nuvens do Azure.

Para testar o serviço de metadados de instância, crie uma VM do [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou [portal do Azure](http://portal.azure.com) nas regiões acima e siga os exemplos abaixo.

## <a name="usage"></a>Uso

### <a name="versioning"></a>Controle de versão
O Serviço de Metadados de Instância tem controle de versão. As versões são obrigatórias e a versão atual é `2017-04-02`.

> [!NOTE] 
> Versões de visualização anteriores de eventos agendados compatíveis {mais recentes} como a api-version. Esse formato não é mais suportado e será substituído no futuro.

Como adicionamos versões mais recentes, as versões mais antigas ainda podem ser acessadas para fins de compatibilidade se os scripts tiverem dependências de formatos de dados específicos. Entretanto, observe que a versão de visualização atual (2017-03-01) poderá não estar disponível quando o serviço estiver totalmente disponível.

### <a name="using-headers"></a>Uso de cabeçalhos
Ao consultar o Serviço de Metadados você deverá fornecer o cabeçalho `Metadata: true` para garantir que a solicitação não seja redirecionada de forma involuntária.

### <a name="retrieving-metadata"></a>Configurando e recuperando os metadados

Os metadados de instância estão disponíveis para a execução de máquinas virtuais criadas/gerenciadas usando o [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Acessar todas as categorias de dados para uma instância de máquina virtual usando a seguinte solicitação:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> Todas as consultas de metadados de instância diferenciam maiúsculas de minúsculas.

### <a name="data-output"></a>Saída de dados
Por padrão, o serviço de metadados de instância retorna dados em formato JSON (`Content-Type: application/json`). No entanto, diferentes APIs podem retornar dados em formatos diferentes, se solicitado.
A tabela a seguir é uma referência de outros formatos de dados que pode oferecer suporte a APIs.

API | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/instance | json | texto
/scheduledevents | json | nenhum

Para acessar um formato de resposta não padrão, especifique o formato solicitado como um parâmetro querystring na solicitação. Por exemplo:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>Segurança
O ponto de extremidade de metadados de instância está acessível somente dentro da instância de máquina virtual em execução em um endereço IP não roteável. Além disso, qualquer solicitação com `X-Forwarded-For`Cabeçalho é rejeitada pelo serviço.
Também exigimos que as solicitações contenham um `Metadata: true` cabeçalho para garantir que a solicitação real tenha sido desejada diretamente e não faça parte de um redirecionamento não intencional. 

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

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-04-02"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```
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

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recuperação de todos os metadados para uma instância

**Solicitação**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```
{
  "compute": {
    "location": "westcentralus",
    "name": "IMDSSample",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "Canonical",
    "sku": "16.04.0-LTS",
    "version": "16.04.201610200",
    "vmId": "5d33a910-a7a0-4443-9f01-6a807801b29b",
    "vmSize": "Standard_A1"
  },
  "network": {
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
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperação de metadados em uma máquina virtual do Windows

**Solicitação**

Os metadados de instância podem ser recuperados no Windows por meio do utilitário do Powershell`curl`: 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

Ou por meio de `Invoke-RestMethod` cmdlet:
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```
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

Dados | Descrição
-----|------------
location | Região do Azure na qual a máquina virtual está sendo executada
name | Nome da VM 
oferta | Oferece informações para a imagem VM. Esse valor só está presente para as imagens implantadas na Galeria de imagens do Azure.
publicador | Publicador da imagem da máquina virtual
sku | SKU específica para a imagem da máquina virtual  
version | Versão da imagem da máquina virtual 
osType | Linux ou Windows 
platformUpdateDomain |  [Domínio de atualização](manage-availability.md) no qual a máquina virtual está sendo executada
platformFaultDomain | [Domínio de falha](manage-availability.md) no qual a máquina virtual está sendo executada
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual
vmSize | [Tamanho da VM](sizes.md)
IPv4/privateIpAddress | Endereço IPv4 local da máquina virtual 
IPv4/privateIpAddress | Endereço IPv4 local da máquina virtual
subnet/address | Endereço sub-rede da máquina virtual
subnet/prefix | Prefixo de sub-rede, exemplo 24
ipv6/ipAddress | Endereço IPv6 local da máquina virtual
macAddress | Endereço mac da máquina virtual 
scheduledevents | No momento em Consulte de visualização pública [aventosagendados](scheduled-events.md)

## <a name="example-scenarios-for-usage"></a>Cenários de exemplo para uso  

### <a name="tracking-vm-running-on-azure"></a>VM de controle em execução no Azure

Como provedor de serviço, você talvez precise controlar o número de máquinas virtuais que executam o seu software ou ter agentes que precisam controlar a exclusividade da máquina virtual. Para obter uma ID exclusiva de uma máquina virtual, use o `vmId` campo do serviço de metadados de instância.

**Solicitação**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**Resposta**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Posicionamento de contêineres, partições de dados com base em domínio de falha/atualização 

Para determinados cenários nos quais o posicionamento de réplicas diferentes é de vital importância. Por exemplo, o [posicionamento de réplica de HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou o posicionamento do contêiner por meio de um [organizador](https://kubernetes.io/docs/user-guide/node-selection/) podem exigir que você conheça os `platformFaultDomain` e `platformUpdateDomain` nas quais a máquina virtual está sendo executada.
Você pode consultar esses dados diretamente por meio de serviço de metadados.

**Solicitação**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**Resposta**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtendo mais informações sobre a máquina virtual durante a ocorrência de suporte 

Como provedor de serviços, você poderá receber uma chamada de suporte na qual gostaria de ter mais informações sobre a máquina virtual. Pedir ao cliente para informar os metadados de computação pode fornecer informações básicas para o profissional de suporte saber o tipo de VM no Azure. 

**Solicitação**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**Resposta**

> [!NOTE] 
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exemplos de como chamar o serviço de metadados usando diferentes idiomas dentro da VM 

Linguagem | Exemplo 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Vá Lan   | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Powershell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
    

## <a name="faq"></a>Perguntas frequentes
1. Estou recebendo o erro `400 Bad Request, Required metadata header not specified`. O que isso significa?
   * O serviço de metadados de instância exige que o cabeçalho `Metadata: true` seja passado na solicitação. Passar o cabeçalho na chamada de REST permite acessar o serviço de metadados de instância. 
2. Por que não estou obtendo informações de computação para minha máquina virtual?
   * Atualmente o serviço de metadados de instância suporta apenas instâncias criadas com o Gerenciador de recursos do Azure. No futuro, poderemos adicionar suporte para VMs de serviço de nuvem.
3. Criei minha máquina virtual com o Azure Resource Manager há algum tempo. Por que não consigo ver as informações de metadados de computação?
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver os metadados de computação. Para máquinas virtuais mais antigas (criadas antes de setembro de 2016), adicione ou remova extensões ou dados de discos à máquina virtual para atualizar os metadados.
4. Por que estou recebendo o erro `500 Internal Server Error`?
   * Repita a solicitação com base no sistema de retirada exponencial. Se o problema persistir, contate o suporte do Azure.
5. Onde posso publicar comentários/perguntas adicionais?
   * Envie seus comentários em http://feedback.azure.com.
7. Isso funcionaria para Instância do Conjunto de Dimensionamento da Máquina Virtual?
   * Sim, o serviço de metadados está disponível para instâncias de conjunto de escala. 
6. Como posso obter suporte para o serviço?
   * Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a máquina virtual na qual você não consegue obter resposta de metadados após várias tentativas 

   ![Serviço de Metadados de Instância](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a API dos [Eventos Agendados](scheduled-events.md) **em visualização pública** fornecida pelo serviço Metadados de Instância.

