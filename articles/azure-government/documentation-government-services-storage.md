---
title: Armazenamento do Azure Governamental | Microsoft Docs
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/22/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 831334f2b835d00aa3fd22292764b69e85d735a6
ms.openlocfilehash: a6b61df5884031eb5b53f983b834357bd23a622e


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
| [Importação/Exportação de blob] (../storage/storage-import-export-service.md) |GA |GA |
| [StorSimple] (../storsimple/storsimple-ova-overview.md) |GA |GA |

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

> [!NOTE]
> Ao implantar o serviço StorSimple Manager, use as URLs https://portal.azure.us/ e https://manage.windowsazure.us/ do Portal do Azure e clássico, respectivamente. Para obter instruções de implantação para o StorSimple Virtual Array, confira [Requisitos do sistema da StorSimple Virtual Array] (../storsimple/storsimple-ova-system-requirements.md), e para o StorSimple série 8000, confira [Requisitos de software, alta disponibilidade e rede do StorSimple] (../storsimple/storsimple-system-requirements.md) e vá para a seção Implantar no painel de navegação à esquerda. Para obter a documentação geral do StorSimple, confira [O que é StorSimple?] (../storsimple/index.md).
>
>

### <a name="considerations"></a>Considerações
As informações a seguir identificam o limite do Azure Governamental para o Armazenamento do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Os dados inseridos, armazenados e processados em um produto de Armazenamento do Azure podem conter dados de exportação controlados. Autenticadores estáticos, como senhas e PINs de cartão inteligente para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerenciar os componentes da plataforma Azure. Outros informações/segredos de segurança, como certificados, chaves de criptografia, chaves mestras e chaves de armazenamento armazenadas nos serviços do Azure. |Metadados do Armazenamento do Azure não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu produto de armazenamento.  Não insira dados Regulamentados/controlados nos seguintes campos: Grupos de recursos, Nomes de implantação, Nomes de recursos, Marcas de recurso |

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>



<!--HONumber=Feb17_HO2-->


