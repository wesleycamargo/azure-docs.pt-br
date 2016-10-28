<properties
   pageTitle="Solucionando problemas de status degradado do Gerenciador de Tráfego"
   description="Como solucionar problemas de perfis do Gerenciador de Tráfego quando ele aparece com status de degradado."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Solucionando problemas de status degradado do Gerenciador de Tráfego do Azure

Esta página descreve como solucionar um problema com o perfil do Gerenciador de Tráfego do Azure que está mostrando um estado degradado e fornece alguns pontos importantes para melhorar sua compreensão sobre as investigações do gerenciador de tráfego.

Você configurou um perfil do Gerenciador de Tráfego apontando para alguns dos seus serviços hospedados no .cloudapp.net, e após alguns segundos vê o Status como Degradado.

![degradedstate](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Se você acessar a guia Pontos de extremidade do perfil, você verá um ou mais pontos de extremidade com um status Offline:

![offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## Observações importantes sobre as investigações do Gerenciador de Tráfego

- O Gerenciador de Tráfego considera apenas um ponto de extremidade como ONLINE se a investigação receber uma resposta 200 do caminho de investigação.
- Um redirecionamento 30x (ou qualquer outra resposta que não seja 200) falhará, mesmo que a URL redirecionado retorne uma resposta 200.

- Para investigações de HTTPs, os erros de certificado são ignorados.
 
- O conteúdo real do caminho de investigação não importa, contanto que uma resposta 200 retorne. Uma técnica comum, caso o conteúdo real do site não retorne uma resposta 200 (ou seja, se as páginas ASP redirecionarem para uma página de logon do ACS ou alguma outra URL CNAME), é definir o caminho como "/favicon.ico" ou algo parecido.
 
- A prática recomendada é definir o caminho de investigação como algo que tenha lógica suficiente para determinar se o site está ativo ou inativo. No exemplo acima, a definição do caminho como "/favicon.ico" faz com que você apenas teste se w3wp.exe está respondendo, mas não se o site está íntegro. Uma opção melhor seria definir um caminho como "/Probe.aspx" ou algo parecido, e no arquivo Probe.aspx incluir uma lógica suficiente para determinar se o site está íntegro (ou seja, observar os contadores de desempenho para verificar se você não está utilizando 100% da CPU ou recebendo uma grande quantidade de solicitações com falha, tentar acessar recursos como o banco de dados ou o estado da sessão para ter certeza de que a lógica do aplicativo está funcionando etc).
 
- Se todos os pontos de extremidade em um perfil estiverem degradados, o Gerenciador de Tráfego tratará todos os pontos de extremidade como íntegros e roteará o tráfego para todos eles. Isso serve para garantir que qualquer possível problema com o mecanismo de investigação que resulte em falhas incorretas não cause uma falha completa do seu serviço.

  

## Solucionar problemas

Uma ferramenta para solucionar problemas de falhas de investigação do Gerenciador de Tráfego é a wget. Você pode obter o pacote de binários e dependências em [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). Observe que você pode usar outros programas, como o Fiddler, ou curl em vez do wget – basicamente, você precisa apenas de algo que mostre a resposta HTTP bruta.

Após a instalação do wget, acesse um prompt de comando e execute wget na URL + porta e caminho de investigação configurados no Gerenciador de Tráfego. Para este exemplo, seria http://watestsdp2008r2.cloudapp.net:80/Probe.

![solucionar problemas](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

Usando o Wget:

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

Observe que o wget indica que a URL retornou um redirecionamento 301 para http://watestsdp2008r2.cloudapp.net/Default.aspx. Como sabemos graças à seção "Observações importantes sobre as investigações do Gerenciador de Tráfego" logo acima, um redirecionamento 30x é considerado uma falha pela investigação do Gerenciador de Tráfego, e isso fará com que a investigação apareça Offline. Neste ponto, é uma simples questão de verificar a configuração de site e certificar-se de que uma resposta 200 retornou do caminho /Probe (ou reconfigurar a investigação do Gerenciador de Tráfego para apontar para um caminho que retornará uma resposta 200).

 

Se a investigação estiver usando o protocolo HTTPs, convém adicionar o parâmetro “--no-check-certificate” ao wget para que ele ignore a incompatibilidade do certificado na URL do cloudapp.net.


## Próximas etapas


[Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md)

[O que é o Gerenciador de Tráfego](traffic-manager-overview.md)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de Tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=AcomDC_0824_2016-->