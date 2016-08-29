<properties
   pageTitle="Tipos de Ponto de Extremidade do Gerenciador de Tráfego | Microsoft Azure"
   description="Este artigo explica os diferentes tipos de pontos de extremidade que podem ser usados com o Gerenciador de Tráfego do Azure"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2016"
   ms.author="jtuliani" />

# Pontos de extremidade do Gerenciador de Tráfego

O Gerenciador de Tráfego do Microsoft Azure permite controlar como o tráfego do usuário é distribuído para as implantações do aplicativo em execução em diferentes data centers ou outros locais em todo o mundo.

Cada implantação do aplicativo precisa ser configurada como um ‘ponto de extremidade’ no Gerenciador de Tráfego. Quando o Gerenciador de Tráfego receber uma solicitação DNS, ele escolherá um desses pontos de extremidade para retornar na resposta DNS, conforme a disponibilidade do ponto de extremidade atual e o método do roteamento de tráfego escolhido. Para obter mais informações, consulte [Como o Gerenciador de Tráfego Funciona](traffic-manager-how-traffic-manager-works.md).

Esta página descreve como o Gerenciador de Tráfego oferece suporte aos pontos de extremidade de diferentes tipos.

## Tipos do ponto de extremidade do Gerenciador de Tráfego

Há três tipos de ponto de extremidade suportados pelo Gerenciador de Tráfego:

- Os **pontos de extremidade do Azure** são usados para os serviços hospedados no Azure.
- Os **pontos de extremidade externos** são usados para os serviços hospedados fora do Azure, no local ou com um provedor de hospedagem diferente.
- Os **pontos de extremidade aninhados** são usados para combinar os perfis do Gerenciador de Tráfego para criar esquemas de roteamento de tráfego mais flexíveis para suportar as necessidades de implantações maiores e mais complexas.

Não há nenhuma restrição sobre como os pontos de extremidade de diferentes tipos são combinados em um único perfil do Gerenciador de Tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto de extremidade.

As seções a seguir descrevem cada tipo de ponto de extremidade com mais detalhes.

### Pontos de extremidade do Azure

Os pontos de extremidade do Azure são usados para configurar os serviços baseados no Azure no Gerenciador de Tráfego. Os pontos de extremidade do Azure suportam atualmente os seguintes tipos de recursos do Azure:

- VMs IaaS 'Clássicas' e serviços de nuvem PaaS.
- Aplicativos Web
- Recursos PublicIPAddress (que podem ser conectados às VMs diretamente ou por meio de um Azure Load Balancer). Observe que o publicIpAddress deve ter um nome DNS atribuído para poder ser usado no Gerenciador de Tráfego.

Os recursos PublicIPAddress são recursos do Azure Resource Manager; eles não existem nas APIs de Gerenciamento de Serviços do Azure. Assim, eles são apenas suportados nas experiências do Azure Resource Manager do Gerenciador de Tráfego. Os outros tipos de ponto de extremidade são suportados por meio das experiências do Gerenciador de Recursos e do Gerenciamento de Serviços no Gerenciador de Tráfego.

Ao usar os pontos de extremidade do Azure, o Gerenciador de Tráfego irá detectar quando uma VM IaaS 'Clássica', serviço de nuvem PaaS ou Aplicativo Web é interrompido e iniciado. Isso é refletido no status do ponto de extremidade (consulte [Monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status) para obter detalhes.) Quando o serviço subjacente for interrompido, o Gerenciador de Tráfego não fará a cobrança para as verificações de integridade do ponto de extremidade e não direcionará o tráfego para o ponto de extremidade. A cobrança retomará e o ponto de extremidade ficará qualificado para receber o tráfego quando o serviço for reiniciado. Isso não se aplica aos pontos de extremidade PublicIpAddress.

### Pontos de extremidade externos

Os pontos de extremidade externos são usados para configurar o Gerenciador de Tráfego para direcionar o tráfego para os serviços fora do Azure, por exemplo, um serviço hospedado no local ou um serviço hospedado com um provedor diferente.

Os pontos de extremidade externos podem ser usados sozinhos ou combinados com os Pontos de Extremidade do Azure no mesmo perfil do Gerenciador de Tráfego. Combinar os pontos de extremidade do Azure com os pontos de extremidade externos permite vários cenários:

- Usando o Azure para fornecer maior redundância para um aplicativo local existente, em um modelo de failover ativo-ativo ou ativo-passivo.
- Usando o Azure para estender um aplicativo local existente para outros locais geográficos, junto com o [roteamento do tráfego de ‘Desempenho’ do Gerenciador de Tráfego](traffic-manager-routing-methods.md#performance-traffic-routing-method), para reduzir a latência do aplicativo e melhorar o desempenho para os usuários finais.
- Usando o Azure para fornecer uma capacidade adicional para um aplicativo local existente, continuamente ou como uma 'intermitência para a nuvem' para lidar com um aumento na demanda.

Em alguns casos, pode ser útil usar pontos de extremidade externos para fazer referência aos serviços do Azure (para obter exemplos, consulte a [FAQ](#faq)). Neste caso, a cobrança para as verificações de integridade é a taxa dos pontos de extremidade do Azure, não a taxa dos pontos de extremidade externos. No entanto, diferentemente dos pontos de extremidade do Azure, se você interromper ou excluir o serviço subjacente, continuará a ser cobrado pelas verificações de integridade correspondentes até desabilitar ou excluir explicitamente o ponto de extremidade no Gerenciador de Tráfego.

### Pontos de extremidade aninhados

Os pontos de extremidade aninhados são usados para combinar os perfis do Gerenciador de Tráfego para criar esquemas de roteamento de tráfego mais flexíveis para suportar as necessidades de implantações maiores e mais complexas.

Com os pontos de extremidade aninhados, um perfil 'filho' é adicionado como um ponto de extremidade a um perfil 'pai' para criar uma hierarquia. Os perfis pai e filho podem conter outros pontos de extremidade de qualquer tipo, incluindo outros perfis aninhados.

Para obter mais detalhes, consulte [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

## Aplicativos Web como pontos de extremidade

Algumas considerações adicionais se aplicam ao configurar os Aplicativos Web como pontos de extremidade no Gerenciador de Tráfego:

1. Somente os Aplicativos Web na SKU 'Standard' ou superior estão qualificados para o uso com o Gerenciador de Tráfego. As chamadas para o Gerenciador de Tráfego para adicionar um Aplicativo Web de uma SKU inferior falhará. Fazer o downgrade da SKU de um Aplicativo Web existente resultará no Gerenciador de Tráfego não enviando mais o tráfego para esse Aplicativo Web e o ponto de extremidade poderá ser removido do Gerenciador de Tráfego.

2. Quando uma solicitação HTTP é recebida pelo serviço de Aplicativos Web, ele usa o cabeçalho de 'host' na solicitação para determinar qual Aplicativo Web deve atender à solicitação. O cabeçalho de host contém o nome DNS usado para iniciar a solicitação, por exemplo, 'contosoapp.azurewebsites.net'. Para usar um nome DNS diferente com seu Aplicativo Web, o nome DNS deve ser registrado como um domínio personalizado para o Aplicativo. Ao adicionar um ponto de extremidade do Aplicativo Web a um perfil do Gerenciador de Tráfego como um ponto de extremidade do Azure, o nome DNS do perfil do Gerenciador de Tráfego é registrado automaticamente como um domínio personalizado para o Aplicativo. Esse registro é removido automaticamente quando o ponto de extremidade é excluído.

3. Normalmente, um Aplicativo Web será configurado como um ponto de extremidade do Azure. No entanto, há circunstâncias em que é útil configurar um Aplicativo Web usando um ponto de extremidade externo (por exemplo, consulte o próximo item). Os Aplicativos Web só podem ser configurados como um ponto de extremidade externo no Gerenciador de Tráfego ao usar as experiências do Gerenciador de Tráfego do Gerenciador de Recursos; isso não é suportado via experiências do Gerenciamento de Serviços.

4. Cada perfil do Gerenciador de Tráfego pode ter, no máximo, um ponto de extremidade do aplicativo Web de cada região do Azure. Uma solução alternativa para essa restrição é dada na [FAQ](#faq).

## Habilitando e desabilitando os pontos de extremidade

Desabilitar um ponto de extremidade no Gerenciador de Tráfego é muito útil para remover temporariamente o tráfego de um ponto de extremidade que está no modo de manutenção ou sendo reimplantado. Depois do ponto de extremidade estar funcionando novamente, ele poderá ser reabilitado.

Os pontos de extremidade podem ser habilitados e desabilitados por meio do portal do Gerenciador de Tráfego, PowerShell, CLI ou API REST, que têm suporte nas experiências do Gerenciador de Recursos e Gerenciamento de Serviços.

>[AZURE.NOTE] Desabilitar um ponto de extremidade do Azure não tem nenhuma relação com o estado de implantação no Azure. Um serviço do Azure (como uma VM ou Aplicativo Web) permanecerá em execução e poderá receber tráfego mesmo quando desabilitado no Gerenciador de Tráfego se o tráfego for endereçado diretamente ao serviço em vez do nome DNS do perfil do Gerenciador de Tráfego. Para obter mais informações, consulte [como o Gerenciador de Tráfego funciona](traffic-manager-how-traffic-manager-works.md).

Quando um ponto de extremidade é desabilitado, ele não é mais retornado nas respostas DNS e, portanto, nenhum tráfego é direcionado para o ponto de extremidade. Além disso, as verificações de integridade para o ponto de extremidade são interrompidas e não serão cobradas. Desabilitar um ponto de extremidade é equivalente à exclusão de um ponto de extremidade, exceto que é mais fácil reabilitar novamente.

A qualificação atual de cada ponto de extremidade para receber o tráfego depende do status do perfil (habilitado/desabilitado), status do ponto de extremidade (habilitado/desabilitado) e resultados das verificações de integridade desse ponto de extremidade. Para obter detalhes, consulte [Monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Ao direcionar o tráfego entre os pontos de extremidade (alterando as configurações de perfil ou durante um failover ou failback), o Gerenciador de Tráfego direcionará novas conexões aos pontos de extremidade disponíveis. No entanto, outros pontos de extremidade podem continuar recebendo tráfego por meio das conexões existentes, até que essas sessões sejam encerradas. Para permitir que o tráfego saia das conexões existentes, os aplicativos devem limitar a duração da sessão usada com cada ponto de extremidade.

Se todos os pontos de extremidade em um perfil estiverem desabilitados ou se o próprio perfil estiver desabilitado, então, as consultas DNS serão atendidas com uma resposta 'NXDOMAIN'. É como se o perfil tivesse sido excluído.

## Perguntas frequentes

### Posso usar o Gerenciador de Tráfego com pontos de extremidade de várias assinaturas?
Para os Aplicativos Web do Azure, isso não é possível. Isso ocorre porque os Aplicativos Web exigem que qualquer nome de domínio personalizado usado com eles seja usado somente em uma única assinatura. Não é possível usar os Aplicativos Web de várias assinaturas com o mesmo nome de domínio e, portanto, eles não podem ser usados com o Gerenciador de Tráfego.

Para outros tipos de ponto de extremidade, é possível usar o Gerenciador de Tráfego com pontos de extremidade de mais de uma assinatura. Como você faz isso depende de estar usando as APIs do Gerenciamento de Serviços ou as APIs do Gerenciador de Recursos para o Gerenciador de Tráfego. O [portal do Azure](https://portal.azure.com) usa o Resource Manager, enquanto o [portal “clássico”](https://manage.windowsazure.com) usa o Gerenciamento de Serviços.

No Gerenciador de Recursos, os pontos de extremidade de qualquer assinatura podem ser adicionados ao Gerenciador de Tráfego, desde que a pessoa que configura o perfil do Gerenciador de Tráfego tenha o acesso de leitura ao ponto de extremidade. Essas permissões podem ser concedidas usando o [RBAC (controle de acesso baseado em função) do Azure Resource Manager](../active-directory/role-based-access-control-configure.md).

No Gerenciamento de Serviços, o Gerenciador de Tráfego requer que qualquer serviço de nuvem ou aplicativo Web configurado como um ponto de extremidade do Azure resida na mesma assinatura do perfil do Gerenciador de Tráfego. Os pontos de extremidade do serviço de nuvem em outras assinaturas podem ser adicionados ao Gerenciador de Tráfego como pontos de extremidade 'externos' (eles ainda serão cobrados na taxa do ponto de extremidade 'interno').

### Posso usar o Gerenciador de Tráfego com os slots de 'Preparo' do Serviço de Nuvem?
Sim. Os slots de 'preparo' do Serviço de Nuvem podem ser configurados no Gerenciador de Tráfego como pontos de extremidade externos.

Como o serviço referenciado pelo ponto de extremidade reside no Azure, as verificações de integridade ainda serão cobradas na taxa usada para os Pontos de Extremidade do Azure.

Como o tipo do ponto de extremidade externo está em uso, as alterações no serviço subjacente não serão selecionadas automaticamente. Portanto, se o Serviço de Nuvem for interrompido ou excluído, o ponto de extremidade do Gerenciador de Tráfego continuará a ser cobrado pelas verificações de integridade até o ponto de extremidade ser desabilitado ou excluído do Gerenciador de Tráfego.

### O Gerenciador de Tráfego dá suporte aos pontos de extremidade IPv6?

Sim. Apesar do Gerenciador de Tráfego no momento não oferecer servidores de nome com endereço IPv6, ele ainda pode ser usado pelos clientes IPv6 que conectam os pontos de extremidade IPv6.

Um cliente somente IPv6 ainda pode usar o Gerenciador de Tráfego, uma vez que o cliente não faz solicitações de DNS diretamente ao Gerenciador de Tráfego, pelo contrário, ele usa um serviço DNS recursivo. Ele pode fazer solicitações a esse serviço por meio do IPv6 e o serviço recursivo deve ser capaz de contatar os servidores de nome do Gerenciador de Tráfego usando o IPv4.

Assim que receber uma consulta DNS, o Gerenciador de Tráfego responderá com o nome DNS do ponto de extremidade ao qual o cliente deve conectar-se. Um ponto de extremidade IPv6 pode ser suportado simplesmente configurando um registro DNS AAAA apontando o nome DNS do ponto de extremidade para o endereço IPv6.

Observe que para as verificações de integridade do Gerenciador de Tráfego funcionarem corretamente, o serviço também precisará expor um ponto de extremidade do IPv4. Isso precisa ser mapeado a partir o mesmo nome DNS do ponto de extremidade usando um registro DNS A.

### Posso usar o Gerenciador de Tráfego com mais de um Aplicativo Web na mesma região?

Normalmente, o Gerenciador de Tráfego é usado para direcionar o tráfego para os aplicativos implantados em regiões diferentes. No entanto, ele também pode ser usado onde um aplicativo tem mais de uma implantação na mesma região.

No caso dos Aplicativos Web, os pontos de extremidade do Azure do Gerenciador de Tráfego não permitem que mais de um ponto de extremidade do Aplicativo Web na mesma região do Azure seja adicionado ao mesmo perfil do Gerenciador de Tráfego. As etapas a seguir fornecem uma solução alternativa para essa restrição:

1.	Verifique se seus Aplicativos Web na mesma região estão em 'unidades de escala' diferentes do aplicativo Web, ou seja, diferentes instâncias do serviço do Aplicativo Web. Para tanto, verifique o caminho DNS para a entrada DNS <...>.azurewebsites.net; a unidade de escala será algo parecido com ‘waws-prod-xyz-123.vip.azurewebsites.net’. Um nome de domínio fornecido deve mapear um único site em uma unidade de escala e por esse motivo, dois Aplicativos Web na mesma unidade de escala não podem compartilhar um perfil do Gerenciador de Tráfego.
2.	Supondo que cada Aplicativo Web está em uma unidade de escala diferente, adicione seu nome de domínio intuitivo como um nome de host personalizado a cada Aplicativo Web. Isso requer que todos os Aplicativos Web pertençam à mesma assinatura.
3.	Adicione um ponto de extremidade do Aplicativo Web (e somente um) como faria normalmente para seu perfil do Gerenciador de Tráfego, como um ponto de extremidade do Azure.
4.	Adicione cada ponto de extremidade do Aplicativo Web extra ao seu perfil do Gerenciador de Tráfego como um ponto de extremidade externo. Isso requer que você utilize a experiência do Gerenciador de Recursos para o Gerenciador de Tráfego, não a experiência do Gerenciamento de Serviços.
5.	Crie um registro DNS CNAME do seu domínio intuitivo (conforme usado na etapa 2 acima) para seu nome DNS de perfil do Gerenciador de Tráfego (<…>.trafficmanager.net).
6.	Acesse seu site usando o nome de domínio intuitivo, não o nome DNS do perfil do Gerenciador de Tráfego.

## Próximas etapas

- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-traffic-manager-works.md).

- Saiba mais sobre [o monitoramento de ponto de extremidade e failover automático](traffic-manager-monitoring.md) do Gerenciador de Tráfego.

- Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) do Gerenciador de Tráfego.

<!---HONumber=AcomDC_0817_2016-->