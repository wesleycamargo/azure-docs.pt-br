---
title: Armazenamento do Azure Governamental | Microsoft Docs
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: e9287ab74e9bed8681b66fa9a168c8d7c403c63b
ms.openlocfilehash: 8c9488e302540ca0c67926f6aa6fad591c6ce037


---
# <a name="azure-government-storage"></a>Armazenamento do Azure Governamental
## <a name="azure-storage"></a>Armazenamento do Azure
Para obter detalhes sobre esse serviço e sobre como usá-lo, veja a [documentação pública do Cofre de Chaves do Azure](../storage/index.md).

### <a name="storage-service-availability-by-azure-government-region"></a>Disponibilidade do serviço de armazenamento por região do Azure Governamental

| O Barramento de | Gov. dos EUA – Virgínia | Gov. dos EUA – Iowa | Observações
| --- | --- | --- | --- |
| [Armazenamento de Blobs] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [Armazenamento de Tabelas] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [Armazenamento de Filas] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [Armazenamento de Arquivos] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [Armazenamento de Blobs quente/frio] (../storage/storage-blob-storage-tiers.md) |ND |ND |
| [Criptografia do serviço de armazenamento] (../storage/storage-service-encryption.md) |GA |GA |
| [Armazenamento Premium] (../storage/storage-premium-storage.md) |GA |ND | Inclui Máquinas Virtuais da série DS. |
| [Importação/Exportação de blob] (../storage/storage-import-export-service.md) |GA |GA | Somente contas de armazenamento clássico têm suporte no momento. |

### <a name="variations"></a>Variações
As URLs para contas de armazenamento no Azure Governamental são diferentes:

| Tipo de serviço | Público do Azure | Azure Government |
| --- | --- | --- |
| Armazenamento de Blob |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Armazenamento de Fila |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Armazenamento de tabela |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| Armazenamento de arquivo |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> Todos os seus scripts e códigos precisam levar em conta os pontos de extremidade apropriados.  Veja [Configurar cadeias de conexão do Armazenamento do Azure](../storage/storage-configure-connection-string.md). 
>
>

Para saber mais sobre APIs, veja o <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx"> Construtor de Conta de Armazenamento em Nuvem</a>.

O sufixo de ponto de extremidade a ser usado nessas sobrecargas é core.usgovcloudapi.net

> [!NOTE]
> O [Gerenciador de Armazenamento do Microsoft Azure] (../vs-azure-tools-storage-manage-with-storage-explorer.md) atualmente não oferece suporte à [conexão a uma assinatura do Azure] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription), adicionando uma conta no Azure Governamental. Usar outros métodos de [conexão com uma conta de armazenamento] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service).
Ao [anexar a uma conta de armazenamento externo] (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account), na **etapa 3**, selecione **Outro (especifique abaixo)** como domínio de ponto de extremidade de armazenamento e especifique **core.usgovcloudapi.net** para o Azure Gorvernamental.
>
>

> [!NOTE]
> Se o erro 53 "O caminho da rede não foi encontrado." for retornado ao [montar o compartilhamento de arquivos] (../storage/storage-dotnet-how-to-use-files.md#mount-the-file-share). Isso pode ser causado pelo firewall bloqueando a porta de saída. Tente montar o compartilhamento de arquivos na VM que está na mesma Assinatura do Azure que a conta de armazenamento.
>
>

### <a name="considerations"></a>Considerações
As informações a seguir identificam o limite do Azure Governamental para o Armazenamento do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Os dados inseridos, armazenados e processados em um produto de Armazenamento do Azure podem conter dados de exportação controlados. Autenticadores estáticos, como senhas e PINs de cartão inteligente para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerenciar os componentes da plataforma Azure. Outros informações/segredos de segurança, como certificados, chaves de criptografia, chaves mestras e chaves de armazenamento armazenadas nos serviços do Azure. |Metadados do Armazenamento do Azure não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu produto de armazenamento.  Não insira dados Regulamentados/controlados nos seguintes campos: Grupos de recursos, Nomes de implantação, Nomes de recursos, Marcas de recurso |

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>



<!--HONumber=Dec16_HO3-->


