<properties 
   pageTitle="Configurar o método de roteamento de tráfego de Desempenho | Microsoft Azure"
   description="Este artigo ajudará você a configurar o método de roteamento de tráfego de desempenho no Gerenciador de Tráfego"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# Configurar o método de roteamento de tráfego de Desempenho

Para usar rotear tráfego para serviços de nuvem e sites (pontos de extremidade) que estão localizados em diferentes datacenters em todo o mundo (também conhecidos como regiões), você pode direcionar o tráfego de entrada para o ponto de extremidade com a menor latência por meio do cliente solicitante. Normalmente, o data center com a menor latência corresponde ao mais próximo em termos de distância geográfica. O método de roteamento de tráfego de Desempenho permite que você faça a distribuição com base na menor latência, mas não consegue levar em consideração as alterações em tempo real feitas na carga ou na configuração da rede. Para obter mais informações sobre os diferentes métodos de roteamento de tráfego fornecidos pelo Gerenciador de Tráfego do Azure, consulte [Sobre Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md).

## Rotear o tráfego com base na menor latência em um conjunto de pontos de extremidade:

1. No Portal de Gerenciamento, no painel esquerdo, clique no ícone do **Gerenciador de Tráfego** para abrir o painel do Gerenciador de Tráfego. Se você ainda não tiver criado seu perfil do Gerenciador de Tráfego, consulte [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md) para obter as etapas para criar um perfil básico do Gerenciador de Tráfego.
2. No Portal de Gerenciamento, no painel do Gerenciador de Tráfego, localize o perfil do Gerenciador de Tráfego que contém as configurações que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
3. Na página de seu perfil, clique em **Pontos de Extremidade** na parte superior da página e verifique se os pontos de extremidade de serviço que você deseja incluir em sua configuração estão presentes. Para obter as etapas para adicionar ou remover pontos de extremidade de seu perfil, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).
4. Na página de seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para ver as **configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **Desempenho*. Se não for, clique em **Desempenho** na lista suspensa.
6. Verifique se as **Configurações de Monitoramento** estão definidas corretamente. O monitoramento garante que não seja enviado tráfego aos pontos de extremidade que estão offline. Para monitorar os pontos de extremidade, você deve especificar um caminho e um nome de arquivo. Observe que uma barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão). Para obter mais informações sobre o monitoramento, consulte [Sobre o monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
7. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
8. Teste as alterações em sua configuração. Para obter mais informações, consulte [Testando as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md).
9. Depois que seu perfil do Gerenciador de Tráfego estiver configurado e funcionando, edite o registro de DNS em seu servidor DNS autoritativo para apontar o nome de domínio de sua empresa para o nome de domínio do Gerenciador de Tráfego. Para obter mais informações sobre como fazer isso, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).

## Próximas etapas


[Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md)

[Métodos de roteamento do Gerenciador de Tráfego](traffic-manager-routing-methods.md)

[Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de roteamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)

[Gerenciador de Tráfego - Desabilitar, habilitar ou excluir um perfil](disable-enable-or-delete-a-profile.md)

[Gerenciador de Tráfego - Desabilitar ou habilitar um ponto de extremidade](disable-or-enable-an-endpoint.md)
 

<!---HONumber=Nov15_HO4-->