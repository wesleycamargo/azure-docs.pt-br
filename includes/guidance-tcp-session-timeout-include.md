##Configurações de TCP para VMs do Azure

As VMs do Azure se comunicam com a Internet pública usando [NAT][nat] \(Network Address Translation). Os dispositivos NAT atribuem um endereço IP público e uma porta a uma VM do Azure, permitindo que a VM estabeleça um soquete para comunicação com outros dispositivos. Se os pacotes deixarem de fluir por esse soquete após um tempo específico, o dispositivo NAT eliminará o mapeamento, e o soquete estará livre para ser usado por outras VMs.

Esse é um comportamento NAT comum, que pode causar problemas de comunicação em aplicativos baseados em TCP que esperam que um soquete seja mantido além de um período de tempo limite. Há duas configurações de tempo limite de ociosidade a serem consideradas para sessões em um estado de *conexão estabelecida*:

- **entrada** por meio do [Azure load balancer][azure-lb-timeout]. Esse tempo limite usa como padrão quatro minutos e pode ser ajustado para até 30 minutos.
- **saída** usando [SNAT][snat] \(NAT de Origem). Esse tempo limite é definido como quatro minutos e não pode ser ajustado.

Para garantir que as conexões não sejam perdidas além do tempo limite, você deve verificar se o aplicativo mantém a sessão ativa ou pode configurar o sistema operacional subjacente para fazer isso. As configurações a serem usadas são diferentes para sistemas Linux e Windows, conforme mostrado abaixo.

Para [Linux][linux], você deve alterar as variáveis de kernel abaixo. net.ipv4.tcp\_keepalive\_time = 120 net.ipv4.tcp\_keepalive\_intvl = 30 net.ipv4.tcp\_keepalive\_probes = 8
 
Para [Windows][windows], você deve alterar os valores de registro abaixo. KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


As configurações acima garantem que um pacote keep alive seja enviado após dois minutos (120 segundos) de tempo ocioso e, em seguida, enviado a cada 30 segundos. E se oito desses pacotes falharem, a sessão será descartada.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md