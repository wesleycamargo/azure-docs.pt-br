## Perfil de Gerenciador de Tráfego
O gerenciador de tráfego e seu recurso de ponto de extremidade filho permitem a distribuição de tráfego para pontos de extremidade no Azure e fora do Azure. Essa distribuição de tráfego é controlada por políticas. O Gerenciador de Tráfego também permite que a integridade do ponto de extremidade seja monitorada e que o tráfego seja desviado adequadamente, com base na integridade de um ponto de extremidade.

Propriedades principais de um perfil de gerenciador de tráfego incluem:

- **Método de roteamento de tráfego** - os valores possíveis são *Desempenho*, *Ponderado* e *Prioridade*.
- **Configuração DNS** -FQDN para o perfil.
- **Protocolo** - protocolo de monitoramento, os valores possíveis são *HTTP* e *HTTPS*.
- **Porta** - porta de monitoramento. 
- **Caminho** - caminho de monitoramento.
- **Pontos de extremidade** -contêiner para recursos de ponto de extremidade.

### Ponto de extremidade 
Um ponto de extremidade é um recurso de filho de um perfil de Gerenciador de Tráfego. Representa um serviço ou ponto de extremidade Web para o qual o tráfego de usuário é distribuído com base na política configurada no recurso de Perfil de Gerenciador de Tráfego.

As propriedades principais de um ponto de extremidade incluem:
 
- **Tipo** - o tipo do ponto de extremidade, os valores possíveis são *Ponto de extremidade do Azure*, *Ponto de extremidade externo* e *Ponto de extremidade aninhado*. 
- **ID do recurso de destino** - endereço IP público de um ponto de extremidade de serviço ou da Web. Isso pode ser um ponto de extremidade do Azure ou externo.
- **Peso** - peso de ponto de extremidade usado no gerenciamento de tráfego. 
- **Prioridade** - prioridade do ponto de extremidade, usada para definir uma ação de failover. 

<!---HONumber=Oct15_HO3-->