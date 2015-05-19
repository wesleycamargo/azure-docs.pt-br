<properties
   pageTitle="Migrando sua VPN site a site para Rota Expressa"
   description="Este artigo lhe ajudará a migrar sua VPN site a site para Rota Expressa."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# Migração de conexão: da VPN site a site para Rota Expressa

Os clientes do Azure podem se conectar à sua rede virtual de duas maneiras: Rota Expressa e VPN site a site. A conexão VPN site a site passa pela Internet pública. O IPsec é usado para criptografar o tráfego de rede. A Rota Expressa é uma conexão privada ao Azure. Você pode se conectar ao Azure ou por meio de um [EXP \(provedor do Exchange\)](expressroute-exchange-providers.md) ou um [NSP \(provedor de serviço de rede\)](expressroute-network-service-providers.md).

Se você já tiver uma conexão VPN site a site à sua rede virtual, você pode seguir as etapas abaixo para migrá-la para se conectar por meio de Rota Expressa.

1\) Faça logon no Portal do Azure

2\) no painel de navegação, clique em **redes** e clique na rede virtual que tem a conexão VPN

3\) Clique em **PAINEL**. Na parte inferior da página, clique em **EXCLUIR GATEWAY**, em seguida, clique em **SIM**.

  Esta etapa removeu o gateway da VPN site a site.

4\) Após o portal mostrar a mensagem "O GATEWAY NÃO FOI CRIADO" para a rede virtual, clique na página **Configurar**.

a\) Clique na caixa de seleção "Usar Rota Expressa"

b\) Altere o CIDR da sub-rede do gateway para /28

![Compartilhamento de assinatura](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5\) Na parte inferior da página, clique em **SALVAR**, em seguida, clique em **SIM**.

6\) Clique em **PAINEL**. Na parte inferior da página, clique em **CRIAR GATEWAY**, em seguida, clique em **SIM**.

 Esta etapa criou o gateway de Rota Expressa
 
Quando o novo gateway é criado, a rede virtual está pronta para se conectar a um circuito de Rota Expressa. Todas as máquinas virtuais na rede virtual permanecem em funcionamento durante todo o processo. Não é necessário movê-las nem desligá-las.

Vá para a última etapa da configuração da Rota Expressa por meio do tutorial [Provedores do Exchange](expressroute-configuring-exps.md) / [Provedores de Serviços de Rede](expressroute-configuring-nsps.md), para vincular sua rede virtual ao circuito de Rota Expressa.



<!--HONumber=54-->