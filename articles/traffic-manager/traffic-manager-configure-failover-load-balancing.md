<properties 
   pageTitle="Configurar o balanceamento de carga de Failover"
   description="Este artigo o ajudará a configurar o balanceamento de carga de failover no Gerenciador de Tráfego"
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
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Configurar o balanceamento de carga de Failover

Geralmente, uma organização deseja fornecer confiabilidade para seus serviços. Ela faz isso fornecendo serviços de backup no caso de seu serviço principal falhar. Um padrão comum para failover de serviço é fornecer um conjunto de serviços idênticos hospedados idênticos e enviar tráfego para um serviço primário, mantendo uma lista configurada de um ou mais serviços de backup. Você pode configurar esse tipo de backup com sites e serviços de nuvem do Azure executando os procedimentos a seguir.

Observe que os sites do Azure já fornecem funcionalidade de balanceamento de carga de failover para sites em um data center (o que também é conhecido como região) independentemente do modo de site. O Gerenciador de Tráfego permite que você especifique o balanceamento de carga de failover para sites em data centers diferentes.

## Para configurar o balanceamento de carga de failover:

1. No Portal de Gerenciamento, no painel esquerdo, clique no ícone do **Gerenciador de Tráfego** para abrir o painel do Gerenciador de Tráfego. Se você ainda não tiver criado seu perfil do Gerenciador de Tráfego, consulte [Gerenciar perfis do Gerenciador de Tráfego](traffic-manager-manage-profiles.md) para obter as etapas para criar um perfil básico do Gerenciador de Tráfego.
2. No painel do Gerenciador de Tráfego no Portal de Gerenciamento, localize o perfil do Gerenciador de Tráfego que contém as configurações que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
3. Em sua página de perfil, clique em **Pontos de extremidade** na parte superior da página e verifique se os serviços de nuvem e sites (pontos de extremidade) que você quer incluir em sua configuração estão presentes. Para obter as etapas para adicionar ou remover pontos de extremidade, consulte [Gerenciar pontos de extremidade no Gerenciador de Tráfego](traffic-manager-endpoints.md).
4. Na página de seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para **Configurações de método de balanceamento de carga**, verifique se o método de balanceamento de carga é **Failover**. Se não for, clique em **Failover** na lista suspensa.
6. Na **Lista de Prioridade de Failover**, ajuste a ordem de failover para seus pontos de extremidade. Quando você seleciona o método de balanceamento de carga de **Failover**, a ordem dos pontos de extremidade selecionados é importante. O ponto de extremidade primário está no topo. Use as setas para cima e para baixo para alterar a ordem conforme necessário. Para obter informações sobre como definir a prioridade de failover usando o Windows PowerShell, consulte [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verifique se as **Configurações de Monitoramento** estão definidas corretamente. O monitoramento garante que não seja enviado tráfego aos pontos de extremidade que estão offline. Para monitorar os pontos de extremidade, você deve especificar um caminho e um nome de arquivo. Observe que uma barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão). Para obter mais informações sobre o monitoramento, consulte [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md).
8. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
9. Teste as alterações em sua configuração. Consulte [Testar as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md) para obter mais informações.
10. Depois que seu perfil do Gerenciador de Tráfego estiver configurado e funcionando, edite o registro de DNS em seu servidor DNS autoritativo para apontar o nome de domínio de sua empresa para o nome de domínio do Gerenciador de Tráfego. Para obter mais informações sobre como fazer isso, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](traffic-manager-point-internet-domain.md).

## Consulte também

[Sobre os métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md)

[Tarefas de Configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Visão geral do Gerenciador de Tráfego](../traffic-manmager-overview.md)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=July15_HO4-->