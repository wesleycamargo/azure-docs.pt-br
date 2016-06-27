<properties 
   pageTitle="Configurar o método de roteamento de tráfego de failover do Gerenciador de Tráfego | Microsoft Azure"
   description="Este artigo ajudará você a configurar o método de roteamento de tráfego de failover no Gerenciador de Tráfego"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="joaoma" />

# Configurar o método de roteamento de Failover

Geralmente, uma organização deseja fornecer confiabilidade para seus serviços. Ela faz isso fornecendo serviços de backup no caso de seu serviço principal falhar. Um padrão comum para failover de serviço é fornecer um conjunto de serviços idênticos hospedados idênticos e enviar tráfego para um serviço primário, mantendo uma lista configurada de um ou mais serviços de backup. Você pode configurar esse tipo de backup com sites e serviços de nuvem do Azure executando os procedimentos a seguir.

Observe que os Sites do Azure já fornecem funcionalidade de roteamento de tráfego failover para sites em um datacenter (o que também é conhecido como região) independentemente do modo do site. O Gerenciador de Tráfego permite que você especifique o método de roteamento de tráfego de failover para sites em datacenters diferentes.

## Para configurar o método de roteamento de tráfego de failover:

1. No portal clássico do Azure, no painel esquerdo, clique no ícone do **Gerenciador de Tráfego** para abrir o painel do Gerenciador de Tráfego. Se você ainda não tiver criado seu perfil do Gerenciador de Tráfego, consulte [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md) para obter as etapas para criar um perfil básico do Gerenciador de Tráfego.
2. No painel do Gerenciador de Tráfego no Portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
3. Em sua página de perfil, clique em **Pontos de extremidade** na parte superior da página e verifique se os serviços de nuvem e sites (pontos de extremidade) que você quer incluir em sua configuração estão presentes. Para obter as etapas para adicionar ou remover pontos de extremidade, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).
4. Na página de seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para **configurações do método de roteamento de tráfego**, verifique se o método de roteamento de tráfego é **Failover**. Se não for, clique em **Failover** na lista suspensa.
6. Na **Lista de Prioridade de Failover**, ajuste a ordem de failover para seus pontos de extremidade. Quando você seleciona o método de roteamento de tráfego de **Failover**, a ordem dos pontos de extremidade selecionados é importante. O ponto de extremidade primário está no topo. Use as setas para cima e para baixo para alterar a ordem conforme necessário. Para obter informações sobre como definir a prioridade de failover usando o Windows PowerShell, consulte [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verifique se as **Configurações de Monitoramento** estão definidas corretamente. O monitoramento garante que não seja enviado tráfego aos pontos de extremidade que estão offline. Para monitorar os pontos de extremidade, você deve especificar um caminho e um nome de arquivo. Observe que uma barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão). Para obter mais informações sobre o monitoramento, consulte [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
8. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
9. Teste as alterações em sua configuração. Consulte [Testar as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md) para obter mais informações.
10. Depois que seu perfil do Gerenciador de Tráfego estiver configurado e funcionando, edite o registro de DNS em seu servidor DNS autoritativo para apontar o nome de domínio de sua empresa para o nome de domínio do Gerenciador de Tráfego. Para obter mais informações sobre como fazer isso, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).

## Próximas etapas

[Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md)

[Métodos de roteamento do Gerenciador de Tráfego](traffic-manager-routing-methods.md)

[Configurar o método de roteamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)

[Gerenciador de Tráfego - Desabilitar, habilitar ou excluir um perfil](disable-enable-or-delete-a-profile.md)

[Gerenciador de Tráfego - Desabilitar ou habilitar um ponto de extremidade](disable-or-enable-an-endpoint.md)

 

<!---HONumber=AcomDC_0615_2016-->