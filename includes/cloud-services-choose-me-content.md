<a name="tellmecs"></a>
## Fale-me sobre os serviços de nuvem

Os Serviços de Nuvem é um exemplo de PaaS (Plataforma como Serviço). Como os [Serviços de Aplicativos](app-service-web-overview.md), essa tecnologia foi desenvolvida para oferecer suporte a aplicativos escalonáveis, confiáveis e baratos de operar. Assim como os [Serviços de Aplicativos](app-service-web-overview.md) são hospedados em VMs, também o são os Serviços de Nuvem. No entanto, você tem mais controle sobre as VMs. Você pode instalar seu próprio software nas VMs do Serviço de Nuvem e controlá-los remotamente.

![cs\_diagram](./media/cloud-services-choose-me-content/diagram.png)

Mais controle também significa menos facilidade de uso. A menos que você precise de opções de controle adicional, geralmente é mais rápido e fácil colocar um aplicativo Web em funcionamento em Aplicativos Web, Serviço de Aplicativo, em comparação com os serviços de nuvem.

A tecnologia fornece duas opções de VM ligeiramente diferentes: as instâncias das *funções Web* executam uma variante do Windows Server com IIS, enquanto as instâncias das *funções de trabalho* executam a mesma variante do Windows Server sem IIS. Um aplicativo de Serviços de Nuvem depende de uma combinação dessas duas opções.

Qualquer combinação dessas duas opções de hospedagem de VM ligeiramente diferentes estão disponíveis em um serviço de nuvem:

* **Função Web** Executa o Windows Server com o seu aplicativo Web automaticamente implantado no IIS.
* **Função de trabalho** Executa o Windows Server sem o IIS.

Por exemplo, um aplicativo simples pode usar apenas uma função Web, enquanto um aplicativo mais complexo pode usar uma função Web para lidar com solicitações de entrada de usuários, em seguida, passar o trabalho que essas solicitações criam a uma função de trabalho para processamento. (Essa comunicação pode usar o [Barramento de Serviço](../articles/service-bus/fundamentals-service-bus-hybrid-solutions.md) ou as [Filas do Azure](../articles/storage/storage-introduction.md).)

Como a figura acima sugere, todas as VMs em um único aplicativo são executados no mesmo serviço de nuvem. Em razão disso, os usuários acessam o aplicativo por meio de um único endereço IP público, com a carga de solicitações balanceada automaticamente entre as VMs do aplicativo. A plataforma vai [escalar e implantar](../articles/cloud-services/cloud-services-how-to-scale.md) as VMs em um aplicativo de Serviços de Nuvem de uma maneira que evita um único ponto de falha de hardware.

Ainda que os aplicativos sejam executados em máquinas virtuais, é importante entender que os Serviços de Nuvem fornecem PaaS, e não IaaS. Aqui está uma forma de pensar: com a IaaS, como as Máquinas Virtuais do Azure, primeiramente você cria e configura o ambiente em que seu aplicativo será executado, em seguida, implanta o aplicativo nesse ambiente. Você é responsável por gerenciar grande parte desse mundo, realizando tarefas como a implantação de novas versões com aplicações de patches do sistema operacional em cada VM. Em contrapartida, na PaaS, é como se o ambiente já existisse. Tudo o que você precisa fazer é implantar o aplicativo. O gerenciamento da plataforma em que ele é executado, incluindo implantar novas versões do sistema operacional, é manipulado para você.

## Escala e gerenciamento
Com os Serviços de Nuvem, você não cria máquinas virtuais. Em vez disso, fornece um arquivo de configuração que informa ao Azure quantas delas você deseja, como **três instâncias da função Web** e **duas instâncias da função de trabalho**, e a plataforma as cria para você. Você ainda escolhe o [tamanho](../articles/cloud-services/cloud-services-sizes-specs.md) que as VMs devem ter, mas não as cria explicitamente. Se o aplicativo precisar manipular uma carga de trabalho maior, é possível solicitar mais VMs, e o Azure criará essas instâncias. Se a carga diminuir, essas instâncias podem ser desligadas e você não paga mais por elas.

Um aplicativo dos Serviços de Nuvem geralmente é disponibilizado aos usuários por um processo de duas etapas. Primeiro, o desenvolvedor [carrega o aplicativo](../articles/cloud-services/cloud-services-how-to-create-deploy.md) na área de preparo da plataforma. Quando o desenvolvedor estiver pronto para ativar o aplicativo, ele usará o Portal de Gerenciamento do Azure para solicitar que o aplicativo seja colocado em produção. Essa [troca entre o preparo e a produção](../articles/cloud-services/cloud-services-nodejs-stage-application.md) pode ser feita sem tempo de inatividade, o que permite que um aplicativo em execução seja atualizado para uma nova versão sem perturbar seus usuários.

## Monitoramento
Os Serviços de Nuvem também fornecem monitoramento. Assim como as Máquinas Virtuais do Azure, eles detectarão um servidor físico com falha e reiniciarão as VMs que estavam em execução nesse servidor em numa nova máquina. Mas os Serviços de Nuvem também detectam as VMs e os aplicativos com falha, não apenas as falhas de hardware. Diferentemente das Máquinas Virtuais, os Serviços têm um representante dentro de cada função Web e de trabalho, de modo que podem iniciar novas instâncias de aplicativo e VMs quando uma falha ocorre.

A natureza da PaaS dos Serviços de Nuvem tem outras implicações também. Uma das mais importantes é que os aplicativos com base nessa tecnologia devem ser escritos para serem executados corretamente quando houver falha de alguma instância da função de trabalho ou da Web. Para isso, um aplicativo dos Serviços de Nuvem não deve manter o estado de suas próprias VMs no sistema de arquivos. Diferentemente das VMs criadas com as Máquinas Virtuais do Azure, as gravações feitas nas VMs dos Serviços de Nuvem não são persistentes; não há nada parecido com um disco de dados das Máquinas Virtuais. Um aplicativo dos Serviços de Nuvem deve gravar explicitamente todo o estado no Banco de Dados SQL, em blobs, tabelas ou em algum outro armazenamento externo. Criar aplicativos dessa forma facilita o dimensionamento deles e os torna mais resistente a falhas, que são metas importantes dos Serviços de Nuvem.

<!---HONumber=Oct15_HO3-->