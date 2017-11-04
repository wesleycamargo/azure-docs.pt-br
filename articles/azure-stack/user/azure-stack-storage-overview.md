---
title: "Introdução ao armazenamento do Azure pilha"
description: Saiba mais sobre armazenamento de pilha do Azure
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 8777aa486a627cf8b2d8ba443e115638354d10da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-storage"></a>Introdução ao armazenamento do Azure pilha

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="overview"></a>Visão geral
Pilha de armazenamento do Azure é um conjunto de serviços de armazenamento de nuvem, incluindo Blobs, tabelas e filas que são consistentes com os serviços de armazenamento do Azure.

## <a name="azure-stack-storage-services"></a>Serviços de pilha de armazenamento do Azure
Armazenamento de pilha do Azure fornece os três serviços a seguir:

* **Armazenamento de Blobs** 

    Armazenamento de blob armazena dados de objeto não estruturados. Um blob pode ser qualquer tipo de texto ou dados binários, como um documento, um arquivo de mídia ou um instalador do aplicativo.
* **Armazenamento de Tabelas** 

    O Armazenamento de Tabela armazena conjuntos de dados estruturados. O Armazenamento de Tabela é um repositório de dados de atributo de chave NoSQL, que permite desenvolvimento rápido e acesso rápido a grandes quantidades de dados.
* **Armazenamento de Filas** 

    O Armazenamento de Fila fornece sistema de mensagens confiável para processamento de fluxo de trabalho e para comunicação entre componentes dos serviços de nuvem.

Uma conta de armazenamento do Azure pilha é uma conta de segurança que fornece acesso a serviços no Azure pilha de armazenamento. Sua conta de armazenamento fornece um namespace único para seus recursos de armazenamento. O diagrama a seguir mostra as relações entre os recursos de armazenamento do Azure pilha em uma conta de armazenamento:

![Visão geral de pilha de armazenamento do Azure](media/azure-stack-storage-overview/AzureStackStorageOverview.png)


### <a name="blob-storage"></a>Armazenamento de blob

Para que os usuários com uma grande quantidade de dados não estruturados objetos armazenados na nuvem, o armazenamento de Blob oferece uma solução eficiente e escalonável. Você pode usar o armazenamento de Blob para armazenar conteúdo, como:

* Documentos
* Dados sociais, como fotos, vídeos, música e blogs
* Backups de arquivos, computadores, bancos de dados e dispositivos
* Imagens e texto para aplicativos web
* Dados de configuração de aplicativos de nuvem
* Big data, como logs e outros grandes conjuntos de dados

Cada blob é organizado em um contêiner. Os contêineres também fornecem uma maneira útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contêineres e um contêiner pode conter qualquer número de blobs, até o limite de conta de armazenamento.

Armazenamento de blob oferece três tipos de blobs: 
* **Blobs de bloco** 

    Os blobs de blocos são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar documentos, arquivos de mídia, backups etc.
* **Blobs de acréscimo** 

    Blobs de anexo são semelhantes aos blobs de blocos, mas são otimizados para operações de anexo. Um blob de anexo pode ser atualizado apenas com a adição de um novo bloco no final. Acrescentar blobs é uma boa opção para cenários como registro em log, em que novos dados precisam ser gravados apenas até o fim do blob.
* **Blobs de página** 

    Blobs de página são otimizados para que representam os discos de IaaS e suporte aleatória de gravações que é até 1 TB de tamanho. Uma máquina virtual de pilha do Azure anexados IaaS disco é um VHD armazenado como um blob de página.


### <a name="table-storage"></a>Armazenamento de tabela
Os aplicativos modernos geralmente demandam armazenamento de dados com maior escalabilidade e flexibilidade que as gerações anteriores de software exigiam. O armazenamento de tabela oferece armazenamento altamente disponível e altamente escalonável, para que seu aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabelas é um armazenamento de chaves/atributos NoSQL da Microsoft - tem um design sem esquema, o que o torna diferente dos bancos de dados relacionais tradicionais. Com um repositório de dados sem esquema, é fácil adaptar seus dados conforme as necessidades de seu aplicativo evoluem. O armazenamento de tabela é fácil de usar para que os desenvolvedores possam criar aplicativos rapidamente.

O armazenamento de tabela é um repositório de chave-atributo, o que significa que cada valor em uma tabela é armazenado com um nome de propriedade. Esse nome de propriedade pode ser usado para filtrar e especificar os critérios de seleção. Um conjunto de propriedades e seus valores compõem uma entidade. Como o Armazenamento de tabela é sem esquema, duas entidades na mesma tabela podem conter diferentes coleções de propriedades, e essas propriedades podem ser de tipos diferentes.

Você pode usar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos e qualquer outro tipo de metadados que o serviço requeira. Para os aplicativos baseados na Internet atuais, os bancos de dados NoSQL como o Armazenamento de tabela, oferecem uma alternativa popular aos bancos de dados relacionais tradicionais.

Uma conta de armazenamento pode conter qualquer número de tabelas e uma tabela pode conter qualquer número de entidades, até o limite de capacidade da conta de armazenamento.

### <a name="queue-storage"></a>Armazenamento de filas
Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O Armazenamento de fila fornece uma solução de mensagens confiáveis para comunicação assíncrona entre os componentes do aplicativo, quer estejam em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O Armazenamento de fila também oferece suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

Uma conta de armazenamento pode conter qualquer número de filas e uma fila pode conter qualquer número de mensagens, até o limite de capacidade da conta de armazenamento. Mensagens individuais podem ter até 64 KB de tamanho.

## <a name="next-steps"></a>Próximas etapas
* [Armazenamento do Azure consistente: diferenças e considerações](azure-stack-acs-differences.md)

* Para saber mais sobre o armazenamento do Azure, consulte [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)

