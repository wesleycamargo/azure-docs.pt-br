#Rede do Azure

A maneira mais fácil de se conectar aos aplicativos e dados do Azure é por meio de uma conexão com a Internet. Mas essa solução tão simples nem sempre é a melhor abordagem. O Azure também fornece tecnologias para conectar usuários aos datacenters do Azure.  Este artigo aborda cada uma dessas tecnologias. 

##Sumário      
- [Rede Virtual do Azure](#Vnet)
- [Gerenciador de Tráfego do Azure](#TrafficMngr)

<a name="Vnet"></a>
##Rede virtual do Azure

O Azure permite criar VMs (máquinas virtuais) que são executadas nos datacenters da Microsoft. Suponha que sua organização queira usar essas VMs para executar aplicativos empresariais ou outros softwares que serão usados pelos funcionários da empresa. Talvez você queira criar um farm do SharePoint na nuvem, por exemplo, ou executar um aplicativo de gerenciamento de estoque. Para facilitar a vida dos seus usuários ao máximo, você deseja que esses aplicativos possam ser acessados como se estivessem sendo executados no seu próprio datacenter.

Não há uma solução padrão para esse tipo de problema: criar uma rede virtual privada (VPN). As organizações de todos os tamanhos de hoje fazem isso, digamos que, para vincular computadores das filiais ao principal datacenter do empresa. Essa mesma abordagem pode funcionar com as VMs do Azure, como mostrado na Figura 1.

<a name="Fig1"></a>
  
![01_Networking][01_Networking]

**Figura 1: a Rede Virtual do Azure permite criar uma rede virtual na nuvem que é conectada ao seu datacenter local.**

Como mostra a figura, a Rede Virtual do Azure permite criar um limite lógico em torno de um grupo de VMs, chamado *rede virtual ou VNET*, em um datacenter do Azure. Ela permite estabelecer uma conexão IPsec entre essa VNET e sua rede local.  As VMs em uma VNET podem ser criadas usando as Máquinas Virtuais do Azure, os Serviços de Nuvem do Azure, ou ambos. Em outras palavras, elas podem ser VMs criadas usando a tecnologia IaaS (Infraestrutura como Serviço) do Azure ou sua tecnologia PaaS (Plataforma como Serviço).
Seja qual for sua escolha, a criação da conexão IPsec exige um dispositivo de gateway VPN, um hardware especializado que é anexado à sua rede local, além de exigir os serviços do administrador da rede. Assim que essa conexão for estabelecida, as VMs do Azure em execução na sua VNET parecerão outra parte da rede da sua organização.

Como sugerido pela [Figura 1](#Fig1), você aloca endereços IP para as VMs do Azure a partir do mesmo espaço de endereço IP usado na sua própria rede. No cenário mostrado aqui, que usa endereços IP privados, as VMs na nuvem são apenas outra sub-rede do IP. O software em execução na sua rede local verá essas VMs como se elas fossem locais, assim como fazem com as VPNs tradicionais. É importante observar que como essa conexão acontece no nível de IP, as máquinas virtuais e físicas em ambos os lados podem ser executadas em qualquer sistema operacional. As VMs do Azure em execução no Windows Server ou Linux podem interagir com as máquinas locais que executam sistemas Windows, Linux ou algum outro. Também é possível usar ferramentas de gerenciamento básicas, incluindo System Center, entre outras, para gerenciar as VMs na nuvem e os aplicativos que elas contêm.

Usar a Rede Virtual do Azure faz sentido em muitas situações. Como já foi mencionado, essa abordagem permite que os usuários empresariais acessem aplicativos na nuvem com mais facilidade. Um importante aspecto dessa facilidade de uso é a capacidade de tornar as VMs do Azure parte um domínio Active Directory local existente para fornecer aos usuários logon único aos aplicativos que eles executam. Se preferir, também é possível criar um domínio do Active Directory na nuvem e, em seguida, conectar esse domínio à sua rede local.

A criação de uma VNET em um datacenter do Azure fornece acesso a um grande pool de recursos sob demanda. Você pode criar VMs sob demanda, pagar por elas enquanto estiverem em execução e removê-las (e interromper o pagamento) quando não forem mais necessárias. Isso pode ser útil para cenários que precisam de acesso rápido a uma máquina pré-configurada, como as equipes de desenvolvimento que criam novos softwares. Em vez de esperar que um administrador local configure os recursos necessários, elas mesmas podem criar esses recursos na nuvem pública. 

Assim como a Rede Virtual faz com que as VMs do Azure apareçam nos recursos locais, o inverso também é verdadeiro: software executado em sua rede local agora parece ser um local para os aplicativos executados em seu VNET do Azure. Suponha que você queira mover um aplicativo local existente para o Azure, por exemplo, porque determinou que será menos oneroso operar na nuvem. Mas e se os dados que o aplicativo usa forem exigidos por lei que sejam armazenados nos locais? Em uma situação como essa, usar a Rede Virtual permite que o aplicativo em nuvem veja um sistema de banco de dados no local como se ele fosse local; o acesso a ele torna-se direto. Qualquer cenário que você escolher, o resultado é o mesmo: o Azure torna-se uma extensão do seu próprio datacenter.

<a name="TrafficMngr"></a>
##Gerenciador de Tráfego do Azure

Imagine que você criou com sucesso um aplicativo do Azure. Seu aplicativo é usado por muitas pessoas em muitos países pelo mundo. Isso é ótimo, como é de costume, o sucesso gera novos problemas. Por exemplo, seu aplicativo muito provavelmente é executado em vários datacenters do Azure em diferentes partes do mundo. Como você pode direcionar o tráfego de solicitação de usuários de modo inteligente por esses datacenters para que os usuários sempre obtenham a melhor experiência?

O Gerenciador de Tráfego do Azure foi desenvolvido para resolver esse problema. A Figura 2 mostra como.

<a name="Fig3"></a>
   
![03_TrafficManager][03_TrafficManager]
   
**Figura 2: o Gerenciador de Tráfego do Azure direciona de modo inteligente as solicitações dos usuários pelas instâncias de um aplicativo que é executado em diferentes datacenters do Azure.**

Neste exemplo, o aplicativo é executado em VMs distribuídas em quatro datacenters: dois nos EUA, um na Europa e um na Ásia. Suponha que um usuário em Berlim deseja acessar o aplicativo. Se você estiver usando o Gerenciador de Tráfego, veja o que acontece.

Como sempre, o sistema do usuário procura o nome DNS do aplicativo (Etapa 1). Essa consulta é direcionada ao sistema DNS do Azure (Etapa 2), que procura a política do Gerenciador de Tráfego para esse aplicativo. Cada política é criada pelo proprietário de um aplicativo específico do Azure, seja por meio de uma interface gráfica ou uma API REST. Por mais que ela seja criada, a política especifica uma das três opções de balanceamento de carga:

- **Desempenho:** todas as solicitações são enviadas para o data center com a menor latência do sistema do usuário. 
- **Failover:** todas as solicitações são enviadas ao datacenter especificado pelo criador dessa política, a menos que o datacenter esteja indisponível. Nesse caso, as solicitações são direcionadas para outros datacenters na ordem de prioridade definida pelo criador da política.
- **Round Robin:** todas as solicitações são distribuídas igualmente entre todos os datacenters nos quais o aplicativo é executado.

Com a política certa, o Gerenciador de Tráfego calcula para qual datacenter essa solicitação deve ser encaminhada com base em qual das três opções foi especificada (Etapa 3). Ele então retorna o local do datacenter escolhido ao usuário (Etapa 4), que acessa essa instância do aplicativo (Etapa 5).

Para que isso funcione, o Gerenciador de Tráfego deve ter um quadro atual de quais instâncias do aplicativo estão funcionando em cada datacenter. Para que isso seja possível, o Gerenciador de Tráfego executa ping periodicamente de cada cópia do aplicativo por meio de um HTTP GET e registra se ele recebe uma resposta. Se uma instância de aplicativo para de responder, o Gerenciador de Tráfego interromperá o direcionamento de usuários para essa instância até que ela volte a responder aos pings. 

Nem todos os aplicativos são grandes ou globais o suficiente para precisar do Gerenciador de Tráfego. No entanto, para aqueles que precisam, esse pode ser um serviço bastante útil.

[01_Networking]: ./media/azure-networking/Networking_01Networking.png
[03_TrafficManager]: ./media/azure-networking/Networking_03TrafficManager.png




