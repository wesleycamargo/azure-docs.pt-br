---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a929149f115d716bf7f9d850abe5ba97bd5a8189
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
### <a name="gwipnoconnection"></a> Para modificar o endereço IP do gateway de rede local - sem conexão de gateway

Use o exemplo para modificar um gateway de rede local que não tenha uma conexão de gateway. Ao modificar esse valor, você também pode modificar os prefixos do endereço ao mesmo tempo.

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Na caixa **Endereço IP**, modifique o endereço IP.
3. Clique em **Salvar** para salvar as configurações.

### <a name="gwipwithconnection"></a>Para modificar o endereço IP do gateway de rede local- conexão de gateway existente

Para modificar um gateway de rede local com uma conexão, você precisa primeiro remover a conexão. Após a conexão ser removida, você pode modificar o endereço IP do gateway e recriar uma nova conexão. Você também pode modificar os prefixos do endereço ao mesmo tempo. Isso resulta em algum tempo de inatividade para a conexão VPN. Ao modificar o endereço IP de gateway, você não precisa excluir o gateway de VPN. Você precisa apenas remover a conexão.
 
#### <a name="1-remove-the-connection"></a>1. Remova a conexão.

1. No recurso de Gateway de Rede Local, na seção **Configurações**, clique em **Conexões**.
2. Clique em **...** na linha para a conexão e então clique em **Excluir**.
3. Para salvar suas configurações, clique em **Salvar** .

#### <a name="2-modify-the-ip-address"></a>2. Modifique o endereço IP.

Você também pode modificar os prefixos do endereço ao mesmo tempo.

1. Na caixa **Endereço IP**, modifique o endereço IP.
2. Clique em **Salvar** para salvar as configurações.

#### <a name="3-recreate-the-connection"></a>3. Recrie a conexão.

1. Navegue até o Gateway de Rede Virtual para sua rede virtual. (Não o Gateway de Rede Local).
2. No Gateway de Rede Virtual, na seção **Configurações**, clique em **Conexões**.
3. Clique em **+ Adicionar** para abrir a folha **Adicionar conexão**.
4. Recrie sua conexão.
5. Clique em **OK** para criar a conexão.