<properties title="Balanceamento de carga para Servi&ccedil;os de infraestrutura do Azure" pageTitle="Balanceamento de carga para Servi&ccedil;os de infraestrutura do Azure" description="Descreve os recursos para executar o balanceamento de carga com o Gerenciador de Tr&aacute;fego e o balanceador de carga." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="cherylmc" videoId="" scriptId="" manager="adinah" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="cherylmc" />

# Balanceamento de carga para Serviços de infraestrutura do Azure

Existem dois níveis de balanceamento de carga para serviços de infraestrutura do Azure:

-   **Nível DNS**: O balanceamento de carga para tráfego de diferentes serviços de nuvem localizados em centros de dados diferentes, para sites do Azure diferentes localizados em centros de dados diferntes, ou para pontos de extremidade externos. Isso é feito com o Gerenciador de Tráfego e o método de balanceamento de carga Round Robin.
-   **Nível de rede**: O balanceamento de carga de tráfego de internet de entrada para máquinas virtuais diferentes de um serviço de nuvem, ou balanceamento de carga de tráfego entre máquinas virtuais em um serviço de nuvem ou rede virtual. Isso é feito com balanceador de carga do Azure.

## Balanceamento de carga do Gerenciador de Tráfego para serviços de nuvem e sites

O Gerenciador de Tráfego do Azure permite que você controle a distribuição de tráfego de usuário para os pontos de extremidade, o que inclui serviços de nuvem, sites, sites externos e outros perfis do Gerenciador de Tráfego. O Gerenciador de Tráfego funciona aplicando um mecanismo de políticas inteligente às consultas DNS (Domain Name System) para os nomes de domúnio de seus recursos da Internet. Seus serviços de nuvem ou sites podem ser executados em datacenters diferentes em todo o mundo.

Você deve usar tanto o REST como o Windows PowerShell para configurar os pontos de extremidade externos ou os perfis do Gerenciador de Tráfego como pontos de extremidade.

O Gerenciador de Tráfego do Azure usa três métodos de balanceamento de carga para distribuir o tráfego:

-   **Failover**: Use este método quando desejar usar um ponto de extremidade primário para todos os seus tráfegos, mas forneça backups no caso de um primário se tornar disponível.
-   **Desempenho**: Use este método quando tiver pontos de extremidade em locais geográficos diferentes e você quiser solicitar aos clientes que usem o ponto de extremidade “mais próximo” em termos de menor latência.
-   **Round Robin:** Use este método quando quiser distribuir a carga através de um conjunto de serviços de nuvem no mesmo datacenter ou através dos serviços de nuvem ou sites em diversos datacenters.

Para obter mais informações, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego][Sobre métodos de balanceamento de carga do Gerenciador de Tráfego].

A seguinte imagem mostra um exemplo de método de balanceamento de carga Round Robin para distribuir tráfego entre diferentes serviços de nuvem.

![loadbalancing][loadbalancing]

O processo básico é o seguinte:

1.  Um cliente da Internet consulta um nome de domínio correspondente ao serviço web.
2.  O DNS encaminha a solicitação de consulta do nome para o Gerenciador de Tráfego.
3.  O Gerenciador de Tráfego envia de volta o nome DNS do serviço de nuvem na lista de Round Robin. O servidor DNS do cliente da Internet soluciona o nome para um endereço IP e o envia para o cliente da Internet.
4.  O cliente da Internet se conecta com um serviço de nuvem escolhido.

Para obter mais informações, consulte [Gerenciador de Tráfego][Gerenciador de Tráfego].

## Balanceamento de carga do Azure para máquinas virtuais

As máquinas virtuais no mesmo serviço de nuvem ou rede virtual pode comunicar entre si diretamente usando seus endereços de IP privados. Os computadores e serviços fora do serviço de nuvem ou rede virtual somente podem se comunicar com máquinas virtuais em um serviço de nuvem ou rede virtual com um ponto de extremidade configurado. Um ponto de extremidade é um mapeamento de um endereço IP público e porta para esse endereço IP privado e porta de uma máquina virtual ou função web dentro de um serviço de nuvem do Azure.

O Balanceador de Carga do Azure distribui randomicamente um tipo específico de tráfego de entrada através de várias máquinas virtuais ou serviços em uma configuração conhecida como conjunto balanceado de carga. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web ou funções web.

A figura a seguir mostra um ponto de extremidade do balanceamento de carga para o tráfego de web padrão (não criptografado) que é compartilhado entre as três máquinas virtuais para a porta TCP pública e privada de 80. Essas três máquinas virtuais estão em um conjunto de balanceamento de carga.

![loadbalancing][1]

Para obter mais informações, consulte [Balanceador de carga do Azure][Balanceador de carga do Azure]. Para conhecer as etapas para criar um conjunto balanceado de carga, consulte [Configurar um conjunto balanceador de carga][Configurar um conjunto balanceador de carga].

O Azure também pode balancear a carga dentro de um serviço de nuvem ou rede virtual. Isso é conhecido como balanceamento de carga interno e pode ser usado das seguintes maneiras:

-   Para balancear a carga entre os servidores em diferentes camadas de um aplicativo de multi-camadas (por exemplo, entre as camadas da web e do banco de dados).
-   Balanceamento de carga para aplicativos de linha de negócios (LOB) hospedados no Azure sem exigir hardware ou software do balanceador de carga adicional.
-   Incluindo servidores locais no conjunto de computadores, cujo tráfego é de carga balanceada.

Semelhante ao balanceamento de carga do Azure, o balanceamento de carga interno é facilitado pela configuração de um conjunto de carga balanceada interno.

A seguinte imagem mostra um exemplo de um ponto de extremidade de carga balanceada interna para um aplicativo de linha de negócios (LOB), que é compartilhado entre três máquinas virtuais em uma rede virtual entre instalações.

![loadbalancing][2]

Para obter mais informações, consulte [Balanceamento de carga interno][Balanceamento de carga interno]. Para conhecer as etapas para criar um conjunto balanceado de carga, consulte [Configurar um conjunto balanceado de carga interno][Configurar um conjunto balanceado de carga interno].

<!-- LINKS -->

  [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego]: http://msdn.microsoft.com/pt-br/library/azure/dn339010.aspx
  [loadbalancing]: ./media/load-balancing-vms/TMSummary.png
  [Gerenciador de Tráfego]: http://msdn.microsoft.com/pt-br/library/azure/hh745750.aspx
  [1]: ./media/load-balancing-vms/LoadBalancing.png
  [Balanceador de carga do Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn655058.aspx
  [Configurar um conjunto balanceador de carga]: http://msdn.microsoft.com/pt-br/library/azure/dn655055.aspx
  [2]: ./media/load-balancing-vms/LOBServers.png
  [Balanceamento de carga interno]: http://msdn.microsoft.com/pt-br/library/azure/dn690121.aspx
  [Configurar um conjunto balanceado de carga interno]: http://msdn.microsoft.com/pt-br/library/azure/dn690125.aspx
