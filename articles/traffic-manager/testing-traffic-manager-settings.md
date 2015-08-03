<properties
   pageTitle="Testando as configurações do Gerenciador de Tráfego"
   description="Este artigo o ajudará a testar as configurações do Gerenciador de Tráfego."
   services="traffic-manager"
   documentationCenter="na"
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

# Testando as configurações do Gerenciador de Tráfego

A melhor maneira de testar suas configurações do Gerenciador de Tráfego é configurar vários clientes e desativar os pontos de extremidade, que consistem em serviços de nuvem e sites, um de cada vez em seu perfil. As dicas a seguir o ajudarão a testar seu perfil do Gerenciador de Tráfego:

## Etapas básicas de teste

-**Defina o TTL do DNS bem baixo** de modo que as alterações se propaguem rapidamente - 30 segundos, por exemplo.

-**Conheça os endereços IP de seus serviços de nuvem do Azure e sites** no perfil que você está testando.

-**Use ferramentas que lhe permitem resolver um nome DNS para um endereço IP** e exiba esse endereço. Você está verificando se o nome de domínio de sua empresa é resolvido na forma de endereços IP dos pontos de extremidade em seu perfil. Eles devem ser resolvidos de maneira consistente com o método de balanceamento de carga de sua política do Gerenciador de Tráfego. Se estiver em um computador que executa o Windows, você poderá usar a ferramenta Nslookup.exe em um prompt de comando ou do Windows PowerShell. Outras ferramentas disponíveis publicamente que permitem que você "se aprofunde" em um endereço IP também estão prontamente disponíveis na Internet.

### Para verificar um perfil do Gerenciador de Tráfego usando nslookup

1 - Abra um prompt de comando ou do Windows PowerShell como administrador.

2 - Digite `ipconfig /flushdns` para liberar o cache do solucionador do DNS.

3 - Digite `nslookup <your Traffic Manager domain name>`. Por exemplo, o comando a seguir verifica o nome de domínio com o prefixo *myapp.contoso* nslookup myapp.contoso.trafficmanager.net. Um resultado típico mostrará o seguinte: - o nome DNS e endereço IP do servidor DNS que está sendo acessado para resolver esse nome de domínio do Gerenciador de Tráfego. - O nome de domínio do Gerenciador de Tráfego que você digitou na linha de comando após "nslookup" e o endereço IP para o qual o domínio do Gerenciador de Tráfego resolve. O segundo endereço IP é o importante para a verificação. Ele deve corresponder a um endereço VIP (IP virtual) público de um dos serviços de nuvem ou sites no perfil do Gerenciador de Tráfego que você está testando.

## Testando métodos de balanceamento de carga


### Para testar um método de balanceamento de carga de failover

1 - Mantenha todos os pontos de extremidade ativados. 2 - Use um único cliente. 3 - Solicite a resolução de DNS para o nome de domínio de sua empresa usando a ferramenta Nslookup.exe ou um utilitário semelhante. 4-Verifique se o endereço IP resolvido que você obtém é para o ponto de extremidade primário 5-Traga seu ponto de extremidade primário ou remova o monitoramento de arquivos para que o Gerenciador de Tráfego pense que ele está desativado. 6 - Aguarde durante a TTL (vida útil) do DNS do perfil do Gerenciador de Tráfego, mais dois minutos adicionais. Por exemplo, se a TTL do DNS for de 300 segundos (cinco minutos), você deverá aguardar por sete minutos. 7 - Libere o cache do cliente DNS e solicite a resolução de DNS. No Windows, você pode liberar o cache DNS com o comando ipconfig /flushdns emitido em um prompt de comando ou no prompt do Windows PowerShell. 8 - Verifique se o endereço IP obtido é para o ponto de extremidade secundário. Repita o processo desativando o ponto de extremidade secundário, o terciário e assim por diante. A cada vez, verifique se a resolução de DNS retorna o endereço IP do próximo ponto de extremidade na lista. Quando todos os pontos de extremidade estiverem desativados, você deverá obter o endereço IP do ponto de extremidade primário novamente.

### Para testar um método de balanceamento de carga de round robin

1 - Mantenha todos os pontos de extremidade ativados. 2 - Use um único cliente. 3 - Solicite a resolução de DNS para o domínio de sua empresa usando a ferramenta Nslookup.exe ou um utilitário semelhante. 4 - Verifique se o endereço IP obtido é um dos incluídos em sua lista. 5 - Libere o cache do cliente DNS e repita as etapas 3 e 4 repetidamente. Você deverá ver diferentes endereços IP retornados para cada um de seus pontos de extremidade. Em seguida, o processo será repetido.

### Para testar uma método de balanceamento de carga de desempenho

Para testar efetivamente um método de balanceamento de carga de desempenho, você deve ter clientes localizados em diferentes partes do mundo. Você pode criar clientes no Azure que tentarão chamar seus serviços por meio do nome de domínio de sua empresa. Como alternativa, se a sua empresa for global, você poderá fazer logon remotamente em clientes em outras partes do mundo e realizar testes por meio desses clientes.

Há serviços gratuitos de dig e pesquisa de DNS baseados na Web disponíveis. Alguns deles possibilitam que você verifique a resolução de nome DNS a partir de vários locais. Pesquise "Pesquisa de DNS" para obter exemplos. Outra opção é usar uma solução de terceiros, como Gomez ou Keynote, para confirmar que os perfis estão distribuindo o tráfego conforme o esperado.

## Consulte também

[Sobre os métodos de balanceamento de carga do Gerenciador de Tráfego](../about-traffic-manager-balancing-methods.md) [Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx) [Gerenciador de Tráfego](../traffic-manager.md)
 

<!---HONumber=July15_HO4-->