<properties
   pageTitle="Convenções de nomenclatura recomendadas para recursos do Azure | Diretrizes | Microsoft Azure"
   description="Convenções de nomenclatura recomendadas para recursos do Azure. Como nomear máquinas virtuais, contas de armazenamento, redes, redes virtuais, sub-redes e outras entidades do Azure"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Convenções de nomenclatura recomendadas para recursos do Azure

A escolha de um nome para qualquer recurso do Microsoft Azure é importante, pois:

- É difícil alterar um nome posteriormente.
- Os nomes devem atender aos requisitos de seu tipo de recurso específico.

Da mesma forma, convenções de nomenclatura consistentes facilitam a localização dos recursos. Elas também ajudam a compreender a função de um recurso em uma solução.

Este artigo fornece um resumo das regras e restrições de nomenclatura para recursos do Azure, bem como um conjunto de recomendações para convenções de nomenclatura. Você pode usar essas recomendações como um ponto de partida para suas próprias convenções específicas às suas necessidades.

O segredo do sucesso com as convenções de nomenclatura é estabelecer e segui-las em todos os seus aplicativos e organizações, adaptando-as à medida que implanta mais aplicativos e serviços na plataforma do Azure.

## Como nomear assinaturas

Ao nomear as assinaturas do Azure, nomes detalhados facilitam a compreensão do contexto e da finalidade de cada assinatura. Ao trabalhar em um ambiente que pode conter várias assinaturas, seguir uma convenção de nomenclatura compartilhada pode proporcionar muito mais clareza.

Este é um padrão recomendado para nomear assinaturas:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Na maioria dos casos, a empresa seria a mesma para cada assinatura. No entanto, algumas empresas podem ter empresas filho dentro da estrutura organizacional. Essas empresas podem ser gerenciadas por um grupo central de TI e, nesse caso, podem ser diferenciadas apresentando o nome da empresa pai (*Contoso*) e o nome da empresa filho (*North Wind*).

- O departamento é um nome dentro da organização no qual um grupo de indivíduos trabalha. Esse item no namespace é opcional. Isso porque talvez algumas empresas não precisem de tantos detalhes devido ao seu porte.

- A linha de produtos é um nome específico de um produto ou uma função executada de dentro do departamento. Embora normalmente seja opcional para serviços e aplicativos internos, recomendamos veementemente para serviços públicos que exigirão separação e identificação fácil (como no caso da separação clara de registros de cobrança).

- O ambiente é o nome que descreve o ciclo de vida de implantação dos aplicativos ou serviços, como Desenvolvimento, Controle de Qualidade ou Produção.

| Empresa | Departamento | Linha de produto ou serviço | Ambiente | Nome completo |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Produção | Contoso SocialGaming AwesomeService Produção |
| Contoso | SocialGaming | AwesomeService | Desenvolvimento | Contoso SocialGaming AwesomeService Desenvolvimento |
| Contoso | IT | InternalApps | Produção | Contoso IT InternalApps Produção |
| Contoso | IT | InternalApps | Desenvolvimento | Contoso IT InternalApps Desenvolvimento |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## Use afixos para evitar ambiguidade

Ao nomear recursos no Azure, recomendamos o uso de prefixos ou sufixos comuns para identificar o tipo e o contexto do recurso. Embora todas as informações sobre o tipo, os metadados, o contexto estejam disponíveis por meio de programação, aproveitar os afixos comuns simplifica a identificação visual. Ao incorporar afixos à sua convenção de nomenclatura, é importante especificar claramente se o afixo ficará no início do nome (prefixo) ou no final (sufixo).

Por exemplo, veja dois nomes possíveis para um serviço que hospeda um mecanismo de cálculo:

- SvcCalculationEngine (prefixo)
- CalculationEngineSvc (sufixo)

Os afixos podem se referir a diversos aspectos que descrevam os recursos em questão. A tabela a seguir mostra alguns exemplos normalmente usados.

| Aspecto | Exemplo | Observações |
| ------ | ------- | ----- |
| Ambiente | dev, prod, qa | Identifica o ambiente do recurso |
| Local padrão | uw (Oeste dos EUA), ue (Leste dos EUA) | Identifica a região na qual o recurso foi implantado |
| Instância | 01, 02 | Para recursos com mais de uma instância nomeada (servidores Web etc). |
| Produto ou serviço | propriedade serviço | Identifica o produto, o aplicativo ou o serviço que recebe suporte do recurso |
| Função | sql, web, messaging | Identifica a função do recurso associado |

Ao desenvolver uma convenção de nomenclatura específica para sua empresa ou projetos, é importante escolher um conjunto comum de afixos, bem como sua posição (sufixo ou prefixo).

## Regras e restrições de nomenclatura

Cada tipo de recurso ou serviço no Azure impõe um conjunto de restrições e escopo de nomenclatura. Qualquer convenção ou padrão de nomenclatura deve aderir às regras e escopo exigidos de nomenclatura. Por exemplo, enquanto o nome de uma VM é mapeado para um nome DNS (e, portanto, deve ser exclusivo em todo o Azure), o nome de uma rede virtual tem como escopo o Grupo de recursos no qual ela é criada.

Em geral, evite caracteres especiais (`-` ou `_`) como o primeiro ou último caractere em qualquer nome, pois isso causará falha na maioria das regras de validação.

| Categoria | Serviço ou entidade | Escopo | Comprimento | Capitalização | Caracteres válidos | Padrão sugerido | Exemplo |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Grupo de recursos | Grupo de recursos | Global | 1-64 | Não diferencia maiúsculas de minúsculas | Alfanumérico, sublinhado e hífen | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Grupo de recursos | Conjunto de disponibilidade | Grupo de recursos | 1-80 | Não diferencia maiúsculas de minúsculas | Alfanumérico, sublinhado e hífen | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Geral | Marca | Entidade associada | 512 (nome), 256 (valor) | Não diferencia maiúsculas de minúsculas | Alfanumérico | `"key" : "value"` | `"department" : "Central IT"` |
| Computação | Máquina Virtual | Grupo de recursos | 1-15 | Não diferencia maiúsculas de minúsculas | Alfanumérico, sublinhado e hífen | `<name>-<role>-<instance>` | `profx-sql-001` |
| Armazenamento | Nome da conta de armazenamento (dados) | Global | 3-24 | Minúscula | Alfanumérico | `<service short name><type><number>` | `profxdata001` |
| Armazenamento | Nome da conta de armazenamento (discos) | Global | 3-24 | Minúscula | Alfanumérico | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Armazenamento | Nome do contêiner | Conta de armazenamento | 3-63 |	Minúscula | Alfanumérico e traço | `<context>` | `logs` |
| Armazenamento | Nome de blob | Contêiner | 1-1024 | Diferencia maiúsculas de minúsculas | Qualquer caractere de URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Armazenamento | Nome da fila | Conta de armazenamento | 3-63 | Minúscula | Alfanumérico e traço | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Armazenamento | Nome da tabela | Conta de armazenamento | 3-63 |Não diferencia maiúsculas de minúsculas | Alfanumérico | `<service short name>-<context>` | `awesomeservice-logs` |
| Armazenamento | Nome do arquivo | Conta de armazenamento | 3-63 | Minúscula | Alfanumérico | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Rede | Rede virtual (VNet) | Grupo de recursos | 2-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, traço, sublinhado e ponto | `<service short name>-[section]-vnet` | `profx-vnet` |
| Rede | Sub-rede | Rede virtual pai | 2-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, sublinhado, traço e ponto | `<role>-subnet` | `gateway-subnet` |
| Rede | Interface de rede | Grupo de recursos | 1-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, traço, sublinhado e ponto | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Rede | Grupo de Segurança de Rede | Grupo de recursos | 1-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, traço, sublinhado e ponto | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Rede | Regra de grupo de segurança de rede | Grupo de recursos | 1-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, traço, sublinhado e ponto | `<descriptive context>` | `sql-allow` |
| Rede | Endereço IP público | Grupo de recursos | 1-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, traço, sublinhado e ponto | `<vm or service name>-pip` | `profx-sql1-pip` |
| Rede | Balanceador de carga | Grupo de recursos | 1-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, traço, sublinhado e ponto | `<service or role>-lb` | `profx-lb` |
| Rede | Configuração de regras de balanceamento de carga | Balanceador de carga | 1-80 | Não diferenciam maiúsculas de minúsculas | Alfanumérico, traço, sublinhado e ponto | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## Organização de recursos com marcas

O Azure Resource Manager dá suporte à marcação de entidades com cadeias de caracteres de texto aleatórias com o objetivo de identificar o contexto e simplificar a automação. Por exemplo, uma marca como `"sqlVersion: "sql2014ee"` pode identificar quaisquer VMs em uma implantação que estão executando o SQL Server 2014 Enterprise Edition com o objetivo de executar um script automatizado nelas. As marcas devem ser usadas para ampliar e aprimorar o contexto junto com as convenções de nomenclatura escolhidas.

> [AZURE.TIP] Outra vantagem das marcas é que elas abrangem grupos de recursos, permitindo que você vincule e correlacione entidades em implantações diferentes.

Cada recurso ou grupo de recursos pode ter, no máximo, **15** marcas. O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres.

Para saber mais sobre marcação de recursos, veja [Uso de marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

Estes são alguns dos casos de uso de marcação mais comuns:

- **Cobrança**; agrupar recursos e associá-los a códigos de cobrança ou de estorno.
- **Identificação de contexto do serviço**; identificar grupos de recursos entre Grupos de recursos para operações comuns e agrupamento
- **Controle de acesso e contexto de segurança**; identificação de função administrativa com base no portfólio, sistema, serviço, aplicativo, instância etc... 

> [AZURE.TIP] Marque no começo, marque com frequência. É melhor ter uma esquema base de marcação aplicado e ajustar ao longo do tempo em vez de ter que fazer ajustes após o fato.

Veja exemplos de algumas abordagens comuns de marcação:

| Nome da marca | Chave | Exemplo | Comentário |
| -------- | --- | ------- | ------- |
| Cobrar de/ID de estorno interno | billTo | `IT-Chargeback-1234` | Um código interno de E/S ou cobrança |
| Operador ou DRI (Pessoa diretamente responsável) | managedBy | `joe@contoso.com` | Alias ou endereço de email |
| Nome do projeto | project-name | `myproject` | Nome do projeto ou da linha de produto |
| Versão do projeto | project-version | `3.4` | Versão do projeto ou da linha de produto |
| Ambiente | environment | `<Production, Staging, QA >` | Identificador de ambiente | 
| Camada | tier | `Front End, Back End, Data` | Identificação da camada ou da função/contexto |
| Perfil de dados | dataProfile | `Public, Confidential, Restricted, Internal` | Confidencialidade dos dados armazenados no recurso |
 
## Dicas e truques

Dependendo do tipo de aplicativo, determinados tipos de recursos podem exigir cuidados adicionais em relação à nomenclatura e convenções. Veja abaixo detalhes adicionais e contexto.

### Máquinas Virtuais

Especialmente em topologias maiores, a nomenclatura cuidadosa das máquinas virtuais simplificará muito a identificação da função e a finalidade de cada máquina, bem como permitirá scripts mais previsíveis.

> [AZURE.WARNING] Observe que cada máquina virtual no Azure tem um nome de recurso do Azure e um nome de host do sistema operacional. Se o nome do recurso e o nome do host forem diferentes, o gerenciamento dessas VMs pode ser desafiador (por exemplo, se a máquina virtual for criada de um .vhd que já contiver um sistema operacional configurado com um nome de host) e deve ser evitado.

- [Convenções de nomenclatura para VMs com o Windows Server](https://support.microsoft.com/pt-BR/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	Contas de armazenamento e entidades de armazenamento

Há dois casos de uso primário para contas de armazenamento: backup de discos para VMs e armazenamento de dados em blobs, em filas e em tabelas. As contas de armazenamento usadas para discos de VM devem seguir a convenção de nomenclatura de associação com o nome da VM pai (e com a possível necessidade de várias contas de armazenamento para SKUs de VM de alto nível também aproveitarem um sufixo numérico).

> [AZURE.TIP] Contas de armazenamento: quer seja para dados ou discos, devem seguir uma convenção de nomenclatura que permita o uso de várias conta de armazenamento (ou seja, sempre usar um sufixo numérico).

É possível configurar um nome de domínio personalizado para acessar dados de blob em sua conta de armazenamento do Azure. O ponto de extremidade padrão para o serviço Blob é `https://mystorage.blob.core.windows.net`.

Porém, se você mapear um domínio personalizado (como www.contoso.com) para o ponto de extremidade do blob de sua conta de armazenamento, também poderá acessar dados do blob em sua conta de armazenamento usando esse domínio. Por exemplo, com um nome de domínio personalizado, `http://mystorage.blob.core.windows.net/mycontainer/myblob` pode ser acessada como `http://www.contoso.com/mycontainer/myblob`.

Para saber mais sobre como configurar esse recurso, consulte [Configurar um nome de domínio personalizado para seu ponto de extremidade de Armazenamento de Blobs](../storage/storage-custom-domain-name.md).

Para saber mais sobre como nomear tabelas, contêineres e blobs:

- [Nomeando e referenciando contêineres, blobs e metadados](https://msdn.microsoft.com/library/dd135715.aspx)
- [Nomeação de filas e de metadados](https://msdn.microsoft.com/library/dd179349.aspx)
- [Tabelas de nomenclatura](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Um nome de blob pode conter qualquer combinação de caracteres, mas os caracteres reservados de URL devem ser escapados corretamente. Evite nomes de blob que terminem com um ponto (.), com uma barra (/) ou com uma sequência ou uma combinação dos dois. Por convenção, a barra é o separador do diretório **virtual**. Não use uma barra invertida () em um nome de blob. As APIs de cliente podem permitir o uso dela, mas não conseguem aplicar o hash corretamente, e as assinaturas não corresponderão.

Não é possível modificar o nome de uma conta de armazenamento ou um contêiner após sua criação. Você deverá excluí-lo e criar um novo, se quiser usar um novo nome.

> [AZURE.TIP] Recomendamos o estabelecimento de uma convenção de nomenclatura para todos os tipos e contas de armazenamento antes de embarcar no desenvolvimento de um novo serviço ou aplicativo.

## Exemplo - implantação de um serviço de N camadas

Neste exemplo, vamos definir a configuração de um serviço de N camadas, composto por servidores IIS de front-end (hospedados em VMs com Windows Server), com SQL Server (hospedado em duas VMs com Windows Server), um cluster ElasticSearch (hospedado em seis VMs com o Linux) e contas de armazenamento, redes virtuais, recursos de grupo e balanceador de carga associados.

Vamos começar definindo as convenções contextuais para esse aplicativo:

| Entidade | Convenção | Descrição |
| ------ | ---------- | ------------ |  
| Nome do Serviço | `profx` | O nome curto do aplicativo ou do serviço que está sendo implantado |
| Ambiente | `prod` | Isso serve para a implantação em produção (ao contrário de qa, teste etc.) |

Da linha de base, podemos mapear as convenções para cada um dos tipos de recursos:

| Tipo de recurso | Convenção base | Exemplo |
| ------------- | --------------- | ------- |
| Assinatura | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Grupo de recursos | `servicename-rg` | `profx-rg` |
| Rede Virtual | `servicename-vnet` | `profx-vnet` |
| Sub-rede | `role-subnet` | `sql-vnet` |
| Balanceador de carga | `servicename-lb` | `profx-lb` |
| Máquina Virtual | `servicename-role[number]` | `profx-sql0` |
| Conta de armazenamento | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Como visto no diagrama a seguir:

![diagrama de topologia do aplicativo](media/guidance-naming-conventions/guidance-naming-convention-example.png "Exemplo de topologia do aplicativo")

## Exemplo - script da CLI do Azure para implantar o exemplo acima

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->