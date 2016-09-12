<properties
	pageTitle="Diretrizes de nomenclatura de infraestrutura | Microsoft Azure"
	description="Saiba mais sobre as principais diretrizes de design e implementação referentes à nomenclatura em serviços de infraestrutura do Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="iainfou"/>

# Diretrizes de nomenclatura de infraestrutura

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artigo destaca as noções básicas sobre como abordar as convenções de nomenclatura para todos os vários recursos do Azure para compilar um conjunto de recursos lógico e facilmente identificável em seu ambiente.

## Diretrizes de implementação de convenções de nomenclatura

Decisões:

- Quais são as suas convenções de nomenclatura de recursos do Azure?

Tarefas:

- Definir os afixos que serão usados entre os recursos para que a consistência seja mantida.
- Definir os nomes de conta de armazenamento de acordo com o requisito para que elas possam ser globalmente exclusivas.
- Documentar a convenção de nomenclatura a ser usada e distribuí-la para todas as partes envolvidas, a fim de garantir a consistência entre as implantações.

## Convenções de nomenclatura

Você deve ter uma boa convenção de nomenclatura definida para criar qualquer coisa no Azure. Uma convenção de nomenclatura garante que todos os recursos tenham um nome previsível, o que ajuda a reduzir a carga administrativa associada ao gerenciamento desses recursos.

Você pode optar por seguir um conjunto específico de convenções de nomenclatura definido para toda a organização ou para uma determinada conta ou assinatura do Azure. Embora seja fácil para as pessoas das organizações estabelecerem regras implícitas ao trabalharem com recursos do Azure, você precisa ter a capacidade de dimensionar para equipes que trabalham juntas no Azure.

Entrem em um acordo sobre um conjunto de convenções de nomenclatura antecipadamente. Há algumas considerações sobre convenções de nomenclatura que abrangem vários conjuntos de regras.

## Afixos

Durante a definição de uma convenção de nomenclatura, surge uma decisão sobre se o afixo deverá ficar:

- No início do nome (prefixo)
- No final do nome (sufixo)

Por exemplo, estes são dois nomes possíveis para um Grupo de Recursos usando o afixo `rg`:

- Rg-WebApp (prefixo)
- WebApp-Rg (sufixo)

Os afixos podem se referir a diversos aspectos que descrevam os recursos em questão. A tabela a seguir mostra alguns exemplos normalmente usados.

| Aspecto | Exemplos | Observações |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Ambiente | dev, stg, prod | Dependendo da finalidade e do nome de cada ambiente. |
| Local | usw (Oeste dos EUA), use (Leste dos EUA 2) | Dependendo da região do datacenter ou da região da organização. |
| Componente, serviço ou produto do Azure | Rg para o grupo de recursos, VNet para a rede virtual | Dependendo do produto para o qual o recurso oferece suporte. |
| Função | db, app, web | Dependendo da função da máquina virtual. |
| Instância | 01, 02, 03 etc. | Para recursos com mais de uma instância. Por exemplo, servidores Web com balanceamento de carga em um serviço de nuvem. |


Ao estabelecer as convenções de nomenclatura, verifique se elas determinam claramente quais afixos devem ser usados para cada tipo de recurso e em qual posição (sufixo versus prefixo).

## Datas

Muitas vezes é importante determinar a data da criação do nome de um recurso. Recomendamos que o formato de data seja AAAAMMDD. Esse formato garante não apenas que a data completa seja registrada, mas também que dois recursos cujos nomes diferem apenas na data sejam classificados em ordem alfabética e cronológica.

## Recursos de nomenclatura

Defina cada tipo de recurso na convenção de nomenclatura, que deve ter regras que definem como atribuir nomes a cada recurso criado. Essas regras devem se aplicar a todos os tipos de recursos, por exemplo:

- Assinaturas
- Contas
- Contas de armazenamento
- Redes virtuais
- Sub-redes
- Conjuntos de disponibilidade
- Grupos de recursos
- Máquinas virtuais
- Pontos de extremidade
- Grupos de segurança de rede
- Funções

Para garantir que o nome possa fornecer informações suficientes para determinar a qual recurso ele se refere, você deve usar nomes descritivos.

## Nomes de computadores

Quando você cria uma VM (máquina virtual), o Azure exige um nome da VM de até 64 caracteres que é usado para o nome do recurso. O Azure usa o mesmo nome para o sistema operacional instalado na VM. No entanto, esses nomes nem sempre serão os mesmos.

Se uma VM for criada por meio de um arquivo de imagem .vhd que já contenha um sistema operacional, o nome da VM no Azure poderá ser diferente do nome do computador do sistema operacional da VM. Essa situação pode adicionar um grau de dificuldade ao gerenciamento da VM, que, portanto, não é recomendável. Atribua ao recurso da VM do Azure o mesmo nome do computador atribuído ao sistema operacional da VM.

Recomendamos que o nome da VM do Azure seja igual ao nome do computador do sistema operacional subjacente.

## Nomes de contas de armazenamento

As contas de armazenamento têm regras especiais para os nomes. Você pode usar apenas letras minúsculas e números. Consulte [Como criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) para saber mais. Além disso, o nome da conta de armazenamento com core.windows.net deve ser um nome DNS exclusivo e globalmente válido. Por exemplo, se a conta de armazenamento for chamada de mystorageaccount, os seguintes nomes DNS resultantes devem ser exclusivos:

- minhacontadearmazenamento.blob.core.windows.net
- minhacontadearmazenamento.table.core.windows.net
- minhacontadearmazenamento.queue.core.windows.net


## Próximas etapas
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0831_2016-->