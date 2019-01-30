---
title: Delegar um subdomínio DNS do Azure
description: Saiba como delegar um subdomínio de DNS do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/22/2019
ms.author: victorh
ms.openlocfilehash: 87a80703c473245660a850645ca3fef21bbd80f6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452710"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegar um subdomínio DNS do Azure

Você pode usar o portal do Azure para delegar um subdomínio de DNS. Por exemplo, se você tem o domínio contoso.com, pode delegar um subdomínio chamado *engineering* a outra zona separada, que pode ser administrada separadamente da zona contoso.com.

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio de DNS do Azure, primeiro você deve delegar seu domínio público ao DNS do Azure. Veja [Delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores de nome para delegação. Depois que seu domínio tiver sido delegado para a zona DNS do Azure, você poderá delegar um subdomínio.

Os exemplos neste artigo usam o domínio contoso.com. Ao usar esses procedimentos, você deve substituir seu próprio domínio.

## <a name="create-a-zone-for-your-subdomain"></a>Criar uma zona para o subdomínio

Primeiro, crie a zona para o subdomínio **engineering**.

1. No portal do Azure, selecione **Criar um recurso**.
2. Na caixa de pesquisa, digite **DNS**e selecione **Zona DNS**.
3. Selecione **Criar**.
4. No painel **Criar zona DNS**, digite **engineering.contoso.com** na caixa de texto **Nome**.
5. Selecione o grupo de recursos para sua zona. Você talvez queira usar o mesmo grupo de recursos como a zona pai para manter recursos semelhantes juntos.
6. Clique em **Criar**.
7. Após a implantação bem-sucedida, vá para a nova zona.

## <a name="note-the-name-servers"></a>Observe os servidores de nome

Em seguida, copie os quatro servidores de nome para seu subdomínio.

1. No painel da zona **engineering**, observe os quatro servidores de nomes para a zona. Você usará esses servidores de nome posteriormente.
2. Crie um registro **A** para uso em testes. Por exemplo, crie um registro **www** A e configure-o com um endereço IP **10.10.10.10**.

## <a name="create-an-ns-record"></a>Criar um registro NS

Em seguida, crie um registro NS (servidor de nome) para a zona **engenharia**.

1. Navegue até a zona para o domínio pai.
2. Selecione **+ Conjunto de registros**.
3. No painel **Adicionar conjunto de registros**, digite **engineering** na caixa de texto **Nome**.
4. Para **Tipo**, selecione **NS**.
5. Em **Servidor de nomes**, insira os quatro servidores de nome que você registrou anteriormente na zona **engineering**.
6. Clique em **OK**.

## <a name="test-the-delegation"></a>Testar a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No prompt de comando, digite `nslookup www.engineering.<your domain name>.`
3. Você deve receber uma resposta não autoritativa mostrando o endereço **10.10.10.10**.



## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).