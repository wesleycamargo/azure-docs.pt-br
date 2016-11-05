
---
title: Migrar dados de usuário do Azure RemoteApp | Microsoft Docs
description: Saiba como migrar seus dados de usuário para dentro e fora do Azure RemoteApp.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Como migrar dados para dentro e fora do Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
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

## Usar copiar e colar no Explorador de Arquivos
Copiar e colar usando a área de transferência está habilitado nas implantações do RemoteApp [por padrão](remoteapp-redirection.md). Isso permite que os usuários copiem arquivos entre seus PCs locais e aplicativos do RemoteApp. Muitas vezes, durante o curso normal de utilização de aplicativos no RemoteApp, os usuários salvam arquivos em seus UPDs – e é fácil mover dados para fora do RemoteApp:

1. [Publicar o Explorador de Arquivos como um aplicativo](remoteapp-publish.md) em uma coleção de RemoteApp. (Observe que se trata de uma tarefa administrativa).
2. Direcione os usuários para iniciar o aplicativo do Explorador de Arquivos que você publicou e usá-lo para copiar e colar arquivos em seu UPD e para fora dele.

## Carregar arquivos e dados em um servidor de arquivos usando a cópia de arquivos de rede padrão
Normalmente, as organizações usam servidores de arquivos para armazenar dados gerais. Se você souber o nome ou o local do servidor, os usuários poderão procurá-lo na rede local e copiar seus arquivos, assim como fizeram acima. Novamente, convém publicar o Explorador de Arquivos no RemoteApp e compartilhá-lo com seus usuários.

> [!NOTE]
> O servidor de arquivos deve estar na rede roteável na qual o RemoteApp foi implantado.
> 
> 

## Copiar um arquivo no OneDrive for Business
Embora não seja possível habilitar o agente de sincronização do OneDrive for Business no RemoteApp, você ainda pode copiar arquivos de seu UPD para o OneDrive for Business por meio de um navegador.

1. Publique o Explorador de Arquivos no RemoteApp e, depois, peça aos usuários para acessarem os arquivos por meio desse aplicativo.
2. É mais fácil transferir arquivos se eles estiverem compactados. Portanto, os usuários devem criar um arquivo .zip contendo todos os arquivos a fim de movê-lo para o OneDrive for Business.
3. Peça aos usuários para acessarem o Portal do Office 365 e, em seguida, acesse o OneDrive e carregue o arquivo .zip.

## Copiar arquivos usando o redirecionamento de unidade
Se tiver habilitado o [redirecionamento de unidade](remoteapp-redirection.md), você já terá criado uma unidade mapeada para seus usuários. Nesse caso, eles poderão compactar seus arquivos na unidade redirecionada e salvá-los em seu computador local.

<!---HONumber=AcomDC_0817_2016-->