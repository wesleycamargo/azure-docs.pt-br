<properties 
   pageTitle="Testando configurações Gerenciador de Tráfego"
   description="Este artigo o ajudará a testar as configurações do Gerenciador de Tráfego"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Testando configurações Gerenciador de Tráfego

A melhor maneira de testar suas configurações do Gerenciador de Tráfego é configurar um número de clientes e então desativar em seu perfil os pontos de extremidade, que consistem em serviços de nuvem e sites, um por vez. As dicas a seguir ajudarão você a testar seu perfil do Gerenciador de Tráfego.

## Etapas básicas de teste

- **Defina o TTL DNS como muito baixo**, de modo que as alterações sejam propagadas rapidamente (30 segundos, por exemplo).
- **Conheça os endereços IP dos sites e serviços de nuvem do Azure** no perfil que você está testando.
- **Use ferramentas que permitam resolver um nome DNS para um endereço IP** e exibir esse endereço. Você está verificando se o nome de domínio de sua empresa é resolvido para os endereços IP dos pontos de extremidade em seu perfil. Eles devem ser resolvidos de maneira consistente com o método de balanceamento de carga de seu perfil do Gerenciador de Tráfego. Se você estiver em um computador executando o Windows, você pode usar a ferramenta de Nslookup.exe em um prompt de comando ou do Windows PowerShell. Outras ferramentas disponíveis publicamente que permitem que você obter um endereço IP também estão prontamente disponíveis na Internet.

### Para verificar um perfil do Gerenciador de Tráfego usando nslookup

1. Abra um prompt de comando ou do Windows PowerShell como administrador.
2. Digite `ipconfig /flushdns` para liberar o cache do resolvedor DNS.
3. Digite `nslookup <your Traffic Manager domain name>`. Por exemplo, o comando a seguir verifica o nome de domínio com o prefixo *myapp.contoso*:
    nslookup myapp.contoso.trafficmanager.net
   Um resultado típico mostrará o seguinte:
   - O nome DNS e o endereço IP do servidor DNS que está sendo acessado para resolver o nome de domínio do Gerenciador de Tráfego.
   - O nome de domínio do Gerenciador de Tráfego digitado na linha de comando após "nslookup" e o endereço IP para o qual o domínio do Gerenciador de Tráfego é resolvido. O segundo endereço IP é aquele cuja verificação é importante. Ele deve corresponder a um VIP (endereço IP virtual) público para um dos serviços de nuvem ou sites no perfil do Gerenciador de Tráfego que você está testando.

## Métodos de balanceamento de carga de teste

### Para testar um método de balanceamento de carga de failover

1. Deixe todos os pontos de extremidade ativados.
2. Use um único cliente.
3. Solicite a resolução de DNS para o nome de domínio da empresa usando a ferramenta Nslookup.exe ou um utilitário semelhante.
4. Verifique se o endereço IP resolvido obtido por você é para seu ponto de extremidade primário
5. Desativar seu ponto de extremidade primário ou remover o arquivo de monitoramento, para que o Gerenciador de Tráfego pense que ele está desativado.
6. Aguarde o TTL (período de vida útil) do DNS do perfil do Gerenciador de Tráfego, mais um tempo adicional de 2 minutos. Por exemplo, se o TTL do DNS é 300 segundos (5 minutos), você deve aguardar 7 minutos.
7. Libere o cache de seu cliente DNS e solicite a resolução do DNS. No Windows, você pode liberar o cache DNS com o comando ipconfig/flushdns emitido em um prompt de comando ou prompt do Windows PowerShell.
8. Certifique-se de que o endereço IP obtido é para o ponto de extremidade secundário.
9. Repita o processo, desativando o ponto de extremidade secundário, seguido pelo terciário e assim por diante. Certifique-se, a cada vez, de que a resolução de DNS retorne o endereço IP do próximo ponto de extremidade na lista. Quando todos os pontos de extremidade estiverem desativados, você deverá obter o endereço IP do ponto de extremidade primário novamente.

### Para testar um método de balanceamento de carga Round Robin

1. Deixe todos os pontos de extremidade ativados.
2. Use um único cliente.
3. Solicite a resolução de DNS para o domínio de sua empresa usando a ferramenta Nslookup.exe ou um utilitário semelhante.
4. Certificar-se de que o endereço IP obtido seja um dos incluídos em sua lista.
5. Libere o cache do cliente DNS e repita as etapas 3 e 4 repetidamente. Você deve ver diferentes endereços IP retornados para cada um dos pontos de extremidade. Em seguida, o processo será repetido.

### Para testar um método de balanceamento de carga de desempenho

Para efetivamente testar um método de balanceamento de carga de desempenho, você deve ter clientes localizados em diferentes partes do mundo. Você pode criar clientes no Azure que tentarão chamar seus serviços por meio do nome de domínio de sua empresa. Como alternativa, se a sua empresa for global, você poderá fazer logon remotamente em clientes em outras partes do país e realizar testes por meio desses clientes.

Há serviços gratuitos de pesquisa de DNS e de obtenção de detalhes baseados em Web. Alguns deles possibilitam que você verifique a resolução de nome DNS a partir de vários locais. Faça uma pesquisa procurando por "Pesquisa de DNS" para obter exemplos. Outra opção é usar uma solução de terceiros, como Gomez ou Keynote, para confirmar que os perfis estão distribuindo o tráfego conforme o esperado.

## Consulte também

[Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md)

[Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operações para Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)


<!--HONumber=49-->