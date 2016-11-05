---
title: Configurar um Application Gateway para descarregamento SSL usando o portal | Microsoft Docs
description: Esta página fornece instruções para criar um Application Gateway com descarregamento SSL usando o portal
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2016
ms.author: gwallace

---
# Configurar um Application Gateway para descarregamento SSL usando o portal
> [!div class="op_single_selector"]
> -[Azure portal](application-gateway-ssl-portal.md)
> -[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> -[Azure Classic PowerShell](application-gateway-ssl.md)
> 
> 

O Azure Application Gateway pode ser configurado para encerrar a sessão SSL no gateway para evitar que a onerosa tarefa de descriptografia de SSL aconteça no web farm. O descarregamento SSL também simplifica a configuração do servidor front-end e o gerenciamento do aplicativo Web.

## Cenário
O cenário a seguir passa pela configuração do descarregamento SSL em um Application Gateway. O cenário pressupõe que você já seguiu as etapas para [Criar um Application Gateway](application-gateway-create-gateway-portal.md).

## Antes de começar
Para configurar o descarregamento SSL com um Gateway de Aplicativo, é necessário um certificado. Esse certificado é carregado no Application Gateway e usado para criptografar e descriptografar o tráfego enviado via SSL. O certificado precisa estar no formato pfx (Troca de Informações Pessoais). Esse formato de arquivo permite que a chave privada seja exportada, o que é exigido pelo Application Gateway para executar criptografia e descriptografia de tráfego.

## Adicionar um ouvinte HTTPS
O ouvinte HTTPS procura o tráfego com base em sua configuração e ajuda a rotear o tráfego para os pools de back-end.

### Etapa 1
Navegue até o portal do Azure e selecione um gateway de aplicativo existente

![folha de visão geral do gateway de aplicativo][1]

### Etapa 2
Clique em Ouvintes e clique no botão Adicionar para adicionar um ouvinte.

### Etapa 3
Preencha as informações necessárias para o ouvinte e carregue o certificado .pfx. Quando terminar, clique em OK.

**Nome**: um nome amigável do ouvinte.

**Configuração de IP de front-end**: a configuração de IP de front-end usada para o ouvinte.

**Porta de front-end (Nome/Porta)**: um nome amigável para a porta usada no front-end do Application Gateway e a porta real usada.

**Protocolo**: uma opção para determinar se o https ou http é usado para o front-end.

**Certificado (Nome/Senha)**: se o descarregamento SSL for usado, um certificado .pfx for exigido para essa configuração, serão necessários um nome amigável e uma senha.

![folha adicionar ouvinte][2]

## Criar uma regra e associá-la ao ouvinte
O ouvinte foi criado. É hora de criar uma regra para lidar com o tráfego do ouvinte.

### Etapa 1
Clique nas **Regras** do Application Gateway e clique em Adicionar.

![folha de regras do gateway de aplicativo][3]

### Etapa 2
Na folha **Adicionar regra básica**, digite o nome amigável da regra e selecione o ouvinte criado na etapa anterior. Escolha o pool de back-end apropriado e a configuração de http e clique em **OK**

![janela de configurações de https][4]

Agora as configurações são salvas no Application Gateway. O processo de salvamento dessas configurações pode demorar um pouco antes que elas estejam disponíveis para exibição pelo portal ou pelo PowerShell. Após salvas, o Application Gateway lida com a criptografia e descriptografia do tráfego. Todo o tráfego entre o Application Gateway e os servidores Web de back-end será tratado por http. Toda a comunicação para o cliente, se iniciada por https, será retornada criptografada para o cliente.

## Próximas etapas
Para saber como configurar uma investigação de integridade personalizada com o Azure Application Gateway, consulte [Criar uma investigação de integridade personalizada](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

<!---HONumber=AcomDC_0921_2016-->