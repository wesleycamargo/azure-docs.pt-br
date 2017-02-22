---
title: "Configurar métodos de roteamento do Gerenciador de Tráfego | Microsoft Docs"
description: "Este artigo explica como configurar diferentes métodos de roteamento no Gerenciador de Tráfego"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: d4481115dbf8cc720019096969c55509f33d250c

---

# <a name="configure-traffic-manager-routing-methods"></a>Configurar métodos de roteamento do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure fornece três métodos de roteamento que controlam como o tráfego é roteado para os pontos de extremidade de serviço disponíveis. O método de roteamento de tráfego é aplicado a cada consulta DNS recebida para determinar qual ponto de extremidade deve ser retornado na resposta DNS.

Três métodos de roteamento de tráfego estão disponíveis no Gerenciador de Tráfego:

* **Prioridade:** selecione “Prioridade” quando desejar usar um ponto de extremidade de serviço primário e fornecer backups caso o primário não esteja disponível.
* **Ponderado:** selecione "Ponderado" quando quiser distribuir o tráfego entre um conjunto de pontos de extremidade, seja uniformemente ou de acordo com os pesos que você definir.
* **Desempenho**: selecione "Desempenho" quando tiver pontos de extremidade em diferentes regiões e quiser que os usuários finais usem o ponto de extremidade “mais próximo” em termos de menor latência de rede.

## <a name="configure-priority-routing-method"></a>Configurar método de roteamento de prioridade

Independentemente do modo de site, os Sites do Azure já fornecem funcionalidade de failover para sites dentro de um data center (também conhecido como região). O Gerenciador de Tráfego fornece failover para sites em diferentes data centers.

É um padrão comum para failover de serviço é enviar tráfego para um serviço primário e fornecer um conjunto de serviços de backup idênticos para failover. As etapas a seguir explicam como configurar esse failover priorizado com sites e serviços de nuvem do Azure:

1. No portal clássico do Azure, no painel esquerdo, clique no ícone do **Gerenciador de Tráfego** para abrir o painel do Gerenciador de Tráfego.
2. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações que você deseja modificar. Para abrir a página de configurações de perfil, clique na seta à direita do nome do perfil.
3. Na parte superior da página de seu perfil, clique em **Pontos de extremidade**. Verifique se os serviços de nuvem e os sites que você deseja incluir em sua configuração estão presentes.
4. Clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para **configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **Failover**. Se não for, clique em **Failover** na lista suspensa.
6. Na **Lista de Prioridade de Failover**, ajuste a ordem de failover para seus pontos de extremidade. Quando você seleciona o método de roteamento de tráfego de **Failover** , a ordem dos pontos de extremidade selecionados é importante. O ponto de extremidade primário está no topo. Use as setas para cima e para baixo para alterar a ordem conforme necessário. Para obter informações sobre como definir a prioridade de failover usando o Windows PowerShell, consulte [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verifique se as **Configurações de Monitoramento** estão definidas corretamente. O monitoramento garante que não seja enviado tráfego aos pontos de extremidade que estão offline. Para monitorar os pontos de extremidade, especifique um caminho e um nome de arquivo. A barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
8. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
9. Teste as alterações em sua configuração.
10. Depois que o perfil do Gerenciador de Tráfego estiver funcionando, edite o registro DNS no servidor DNS autoritativo para que o nome de domínio de sua empresa aponte para o nome de domínio do Gerenciador de Tráfego.

## <a name="configure-weighted-routing-method"></a>Configurar o método de roteamento ponderado

Um padrão comum de roteamento de tráfego é fornecer um conjunto de pontos de extremidade idênticos, que incluem serviços de nuvem e sites, e enviar tráfego a cada um deles em um estilo round robin. As etapas a seguir descrevem como configurar esse tipo de método de roteamento de tráfego.

> [!NOTE]
> Os sites do Azure já fornecem funcionalidade de balanceamento de carga round robin para sites dentro de um data center (também conhecido como região). O Gerenciador de Tráfego permite que você especifique o método de roteamento de tráfego de round robin para sites em datacenters diferentes.

1. No portal clássico do Azure, no painel esquerdo, clique no ícone do **Gerenciador de Tráfego** para abrir o painel do Gerenciador de Tráfego.
2. No painel Gerenciador de Tráfego, localize o perfil do Gerenciador de Tráfego que contém as configurações que você deseja modificar. Para abrir a página de configurações de perfil, clique na seta à direita do nome do perfil.
3. Na página de seu perfil, clique em **Pontos de Extremidade** na parte superior da página e verifique se os pontos de extremidade de serviço que você deseja incluir em sua configuração estão presentes.
4. Na página de seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para **Configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **Round Robin**. Se não for, clique em **Round Robin** na lista suspensa.
6. Verifique se as **Configurações de Monitoramento** estão definidas corretamente. O monitoramento garante que não seja enviado tráfego aos pontos de extremidade que estão offline. Para monitorar os pontos de extremidade, especifique um caminho e um nome de arquivo. A barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
7. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
8. Teste as alterações em sua configuração.
9. Depois que o perfil do Gerenciador de Tráfego estiver funcionando, edite o registro DNS no servidor DNS autoritativo para que o nome de domínio de sua empresa aponte para o nome de domínio do Gerenciador de Tráfego.

## <a name="configure-performance-traffic-routing-method"></a>Configurar o método de roteamento de tráfego de Desempenho

O método de roteamento de tráfego de Desempenho permite direcionar o tráfego para o ponto de extremidade com a menor latência de rede do cliente. Normalmente, o data center com a menor latência corresponde ao mais próximo em termos de distância geográfica. Esse método de roteamento de tráfego não consegue considerar as alterações em tempo real à configuração ou à carga de rede.

1. No portal clássico do Azure, no painel esquerdo, clique no ícone do **Gerenciador de Tráfego** para abrir o painel do Gerenciador de Tráfego.
2. No painel Gerenciador de Tráfego, localize o perfil do Gerenciador de Tráfego que contém as configurações que você deseja modificar. Para abrir a página de configurações de perfil, clique na seta à direita do nome do perfil.
3. Na página de seu perfil, clique em **Pontos de Extremidade** na parte superior da página e verifique se os pontos de extremidade de serviço que você deseja incluir em sua configuração estão presentes.
4. Na página de seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para ver as **configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **Desempenho**. Se não for, clique em **Desempenho** na lista suspensa.
6. Verifique se as **Configurações de Monitoramento** estão definidas corretamente. O monitoramento garante que não seja enviado tráfego aos pontos de extremidade que estão offline. Para monitorar os pontos de extremidade, especifique um caminho e um nome de arquivo. A barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).
7. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
8. Teste as alterações em sua configuração.
9. Depois que o perfil do Gerenciador de Tráfego estiver funcionando, edite o registro DNS no servidor DNS autoritativo para que o nome de domínio de sua empresa aponte para o nome de domínio do Gerenciador de Tráfego.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar Perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)
* [Métodos de roteamento do Gerenciador de Tráfego](traffic-manager-routing-methods.md)
* [Testando as Configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md)
* [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md)
* [Gerenciar pontos de extremidade do Gerenciador de Tráfego](traffic-manager-manage-endpoints.md)
* [Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO5-->


