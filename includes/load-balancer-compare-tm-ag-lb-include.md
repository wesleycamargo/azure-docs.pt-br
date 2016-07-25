## Diferenças do balanceador de carga

Há diferentes opções para distribuir o tráfego de rede usando o Microsoft Azure. Essas opções funcionam de forma diferente uma da outra, com conjuntos diferentes de recursos e permitindo cenários diferentes. Cada uma pode ser usada isoladamente ou em combinação.

- O Azure Load Balancer funciona na camada de transporte (nível 4 na pilha de referência de rede OSI). Ele fornece a distribuição do tráfego no nível de rede entre instâncias de um aplicativo em execução no mesmo data center do Azure.

- O Application Gateway funciona na camada de aplicativo (nível 7 na pilha de referência de rede OSI). Ele atua como um serviço de proxy inverso, encerrando a conexão do cliente e encaminhando solicitações aos pontos de extremidade de back-end.

- 	O Gerenciador de Tráfego funciona no nível de DNS. Ele usa respostas de DNS para direcionar o tráfego do usuário final para pontos de extremidade globalmente distribuídos. Assim, os clientes se conectam a esses pontos de extremidade diretamente. A tabela a seguir resume os recursos oferecidos por cada serviço:

| O Barramento de | Balanceador de Carga do Azure | Application Gateway | Gerenciador de Tráfego |
|---|---|---|---|
|Tecnologia| Nível de transporte (nível 4) | Nível de aplicativo (nível 7) | Nível de DNS |
| Protocolos de aplicativo com suporte |	Qualquer | HTTP e HTTPS | 	Qualquer um (um ponto de extremidade HTTP/S é exigido para monitoramento do ponto de extremidade) |
| Pontos de extremidade | VMs do Azure e instâncias de função dos Serviços de Nuvem | Qualquer endereço IP interno do Azure ou endereço IP público de Internet | VMs do Azure, Serviços de Nuvem, Aplicativos Web do Azure e pontos de extremidade externos |
| Suporte à rede virtual | Pode ser usado para aplicativos voltados para a Internet e internos (rede virtual) | Pode ser usado para aplicativos voltados para a Internet e internos (rede virtual) |	Suporte apenas para aplicativos voltados para a Internet |
Monitoramento do ponto de extremidade | Tem suporte por meio de investigações | Tem suporte por meio de investigações | Tem suporte por meio de HTTP/HTTPS GET | 
<BR> O Balanceador de Carga e o Application Gateway do Azure direcionam o tráfego de rede para pontos de extremidade, mas eles têm diferentes cenários de uso para tratamento do tráfego. A tabela abaixo ajuda a entender a diferença entre os dois balanceadores de carga:


| Tipo | Balanceador de carga do Azure | Application Gateway |
|---|---|---|
| Protocolos | UDP/TCP | HTTP/HTTPS |
| Reserva de IP | Suportado | Sem suporte | 
| Modo de balanceamento de carga | 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) | CookieBasedAffinity = false,rules = basic (Round-Robin) | 
| Modo de balanceamento de carga (IP de origem/sessões complexas) | 2 tuplas (IP de origem e IP de destino), 3 tuplas (IP de origem, IP de destino e porta). Pode ser escalonado vertical ou horizontalmente com base no número de máquinas virtuais | CookieBasedAffinity = true,rules = basic (Roud-Robin) para novas conexões. |
| investigações de integridade | Padrão: intervalo de investigação - 15 segundos. Retirada da rotação: 2 falhas contínuas. Oferece suporte a investigações definidas pelo usuário | Intervalo de investigação ociosa de 30 segundos. Retirado após 5 falhas consecutivas do tráfego ativo ou uma única falha de investigação no modo ocioso. Oferece suporte a investigações definidas pelo usuário | 
| Descarregamento de SSL | sem suporte | com suporte | 


  

<!---HONumber=AcomDC_0713_2016-->