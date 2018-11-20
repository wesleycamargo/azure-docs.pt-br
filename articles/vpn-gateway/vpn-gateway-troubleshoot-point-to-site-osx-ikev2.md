---
title: Solucionar problemas de conexões VPN ponto a site do Azure dos clientes Mac OS X | Microsoft Docs
description: Etapas para solucionar problemas de conexões de cliente SO P2S Mac OS X VPN
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: c370808d06f31f9d79c99ca0f20f613d9e335d60
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339043"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Solucionar problemas de conexões VPN ponto a site do Azure dos clientes Mac OS X

Este artigo ajuda você a solucionar problemas de conectividade de Ponto a Site do Mac OS X usar o cliente VPN nativo e IKEv2. O cliente VPN no Mac para IKEv2 é bem básico e não permite muita personalização. Há apenas quatro configurações que precisam ser verificados:

* Endereço do servidor
* ID remota
* ID Local
* Configurações de Autenticação
* Versão do SO (10.11 ou superior)


## <a name="VPNClient"></a>Solucionar problemas de autenticação baseada em certificado
1. Verifique as configurações do cliente VPN. Vá para a **Configuração de Rede** pressionando o comando + Shift e digite "VPN" para verificar as configurações do cliente VPN. Na lista, clique na entrada VPN que precisa ser investigada.

  ![Autenticação baseada em certificado IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. A **ID remota** deve ser a mesma que o endereço do servidor (FQDN do Gateway).
4. A **ID Local** deve ser a mesma que o **Assunto** do certificado do cliente.
5. Clique em **Configurações de Autenticação** para abrir a página de Configurações de Autenticação.

  ![Configurações de autenticação](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Verifique se **Certificado** está selecionado na lista suspensa.
7. Clique no botão **Selecionar** e verifique se o certificado correto está selecionado. Clique em **OK** para salvar quaisquer alterações.

## <a name="ikev2"></a>Solucionar problemas de autenticação do nome de usuário e senha

1. Verifique as configurações do cliente VPN. Vá para a **Configuração de Rede** pressionando o comando + Shift e digite "VPN" para verificar as configurações do cliente VPN. Na lista, clique na entrada VPN que precisa ser investigada.

  ![Nome de usuário e senha IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Verifique se o **Endereço do Servidor** é o FQDN completo e inclui o cloudapp.net.
3. A **ID remota** deve ser a mesma que o endereço do servidor (FQDN do Gateway).
4. A **ID Local** pode ficar em branco.
5. Clique no **Configuração de Autenticação** e verifique se o "Nome de usuário" está selecionado na lista suspensa.

  ![Configurações de autenticação](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Verifique se as credenciais corretas estão inseridas.

## <a name="additional"></a>Etapas adicionais

Se você tentar realizar as etapas anteriores e tudo estiver configurado corretamente, faça o download do [Wireshark](https://www.wireshark.org/#download) e execute uma captura de pacote.

1. Filtrar *isakmp* e examine o **IKE_SA** pacotes. Você poderá examinar os detalhes da proposta de SA em **Payload: Associação de Segurança**. 
2. Verifique se o cliente e o servidor têm um conjunto comum.

  ![pacote](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Se não houver resposta do servidor nos rastreamentos de rede, verifique se você ativou o protocolo IKEv2 na página Configuração do Gateway do Azure no site do Portal do Azure.

## <a name="next-steps"></a>Próximas etapas
Para obter mais ajuda, entre em contato com o [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
