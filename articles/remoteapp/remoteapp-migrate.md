---
title: "Migrar dados de usuário do Azure RemoteApp | Microsoft Docs"
description: "Saiba como migrar seus dados de usuário para dentro e fora do Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d7e4fbf1-cb42-4430-94a0-ed6d4676fc86
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: ba3cf4c6834279bbd7f94d666fd8abbb7ac05bf0
ms.contentlocale: pt-br
ms.lasthandoff: 07/22/2017

---
# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Como migrar dados para dentro e fora do Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Você pode usar várias ferramentas e métodos diferentes para transferir [dados de usuário](remoteapp-upd.md) para dentro e fora do Azure RemoteApp. Veja alguns métodos:

* Copiar e colar usando o compartilhamento da área de transferência
* Copiar arquivos e dados para um servidor de arquivos
* Copiar arquivos para o OneDrive for Business por meio de um navegador
* Copiar os arquivos usando o redirecionamento

> [!NOTE]
> Não é possível habilitar os agentes de sincronização do OneDrive for Business ou para Consumidor - eles [não têm suporte](remoteapp-onedrive.md) no Azure RemoteApp.
> 
> 

## <a name="use-copy-and-paste-in-file-explorer"></a>Usar copiar e colar no Explorador de Arquivos
Copiar e colar usando a área de transferência está habilitado nas implantações do RemoteApp [por padrão](remoteapp-redirection.md). Isso permite que os usuários copiem arquivos entre seus PCs locais e aplicativos do RemoteApp. Muitas vezes, durante o curso normal de utilização de aplicativos no RemoteApp, os usuários salvam arquivos em seus UPDs – e é fácil mover dados para fora do RemoteApp:

1. [Publicar o Explorador de Arquivos como um aplicativo](remoteapp-publish.md) em uma coleção de RemoteApp. (Observe que se trata de uma tarefa administrativa).
2. Direcione os usuários para iniciar o aplicativo do Explorador de Arquivos que você publicou e usá-lo para copiar e colar arquivos em seu UPD e para fora dele.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Carregar arquivos e dados em um servidor de arquivos usando a cópia de arquivos de rede padrão
Normalmente, as organizações usam servidores de arquivos para armazenar dados gerais. Se você souber o nome ou o local do servidor, os usuários poderão procurá-lo na rede local e copiar seus arquivos, assim como fizeram acima. Novamente, convém publicar o Explorador de Arquivos no RemoteApp e compartilhá-lo com seus usuários.

> [!NOTE]
> O servidor de arquivos deve estar na rede roteável na qual o RemoteApp foi implantado.
> 
> 

## <a name="copy-files-to-onedrive-for-business"></a>Copiar um arquivo no OneDrive for Business
Embora não seja possível habilitar o agente de sincronização do OneDrive for Business no RemoteApp, você ainda pode copiar arquivos de seu UPD para o OneDrive for Business por meio de um navegador. 

1. Publique o Explorador de Arquivos no RemoteApp e, depois, peça aos usuários para acessarem os arquivos por meio desse aplicativo. 
2. É mais fácil transferir arquivos se eles estiverem compactados. Portanto, os usuários devem criar um arquivo .zip contendo todos os arquivos a fim de movê-lo para o OneDrive for Business.
3. Peça aos usuários para acessarem o Portal do Office 365 e, em seguida, acesse o OneDrive e carregue o arquivo .zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copiar arquivos usando o redirecionamento de unidade
Se tiver habilitado o [redirecionamento de unidade](remoteapp-redirection.md), você já terá criado uma unidade mapeada para seus usuários. Nesse caso, eles poderão compactar seus arquivos na unidade redirecionada e salvá-los em seu computador local.

## <a name="how-administrators-can-export-data"></a>Como os administradores podem exportar dados

Os administradores do Azure RemoteApp podem exportar todos os UPDs (discos de perfil do usuário) de todas as coleções de uma assinatura para o Armazenamento do Azure usando o cmdlet Export-AzureRemoteAppUserDisk do Azure PowerShell.  Não é possível selecionar UPDs individuais.  Quando o comando do PowerShell for executado, cada disco de usuário terá 50 GB de disco fixo e será exportado para o armazenamento do Azure.  Os custos de armazenamento do Azure incorrerão imediatamente para esse armazenamento.  Ao executar esse comando, verifique se não existem sessões; caso contrário, a exportação falhará.

UPDs para implantações do Azure RemoteApp ingressadas no domínio só podem ser usados novamente em uma implantação do RDS; implantações ingressadas fora do domínio não podem ser usadas.  Se esses discos forem ser usados em uma implantação do RDS, recomendamos o uso de nossos [scripts automatizados](https://github.com/arcadiahlyy/aramigration), que exportarão, converterão e importarão os UDPs para uma implantação do RDS.


