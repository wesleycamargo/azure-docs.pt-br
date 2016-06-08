<properties
	pageTitle="Usar a CDN do Azure com o CORS"
	description="Saiba como usar a CDN (Rede de Distribuição de Conteúdo) do Azure com o CORS (Compartilhamento de Recursos entre Origens)."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="casoper"/>
    
# Usar a CDN do Azure com o CORS     

## O que é CORS?

O CORS (Compartilhamento de Recursos entre Origens) é um recurso HTTP que permite que um aplicativo web em execução em um domínio acesse recursos em outro domínio. Para reduzir a possibilidade de ataques de script entre sites, todos os navegadores modernos implementam uma restrição de segurança conhecida como [política de mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy). Isso impede que uma página da Web chame APIs em um domínio diferente. O CORS fornece uma maneira segura para permitir que um domínio (o domínio de origem) chame APIs em outro domínio.
 
## Como ele funciona
1.	O navegador envia a solicitação OPTIONS com um cabeçalho HTTP **Origin**. O valor desse cabeçalho é o domínio que forneceu a página pai. Quando uma página de https://www.contoso.com tenta acessar os dados de um usuário no domínio fabrikam.com, o seguinte cabeçalho de solicitação é enviado para fabrikam.com: 
    
    `Origin: https://www.contoso.com`
 
2.	O servidor pode responder com o seguinte:
    - Um cabeçalho **Access-Control-Allow-Origin** em sua resposta indicando quais sites de origem são permitidos. Por exemplo:
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Uma página de erro se o servidor não permite a solicitação entre origens
    - Um cabeçalho **Access-Control-Allow-Origin** com um caractere curinga que permite todos os domínios:
        
        `Access-Control-Allow-Origin: *`
 
Para solicitações HTTP complexas, uma solicitação de "simulação" é feita primeiro para determinar se tem permissão antes de enviar a solicitação inteira.
 
## Cenários de origem única ou curinga

O CORS na Azure CDN funcionará automaticamente sem nenhuma configuração adicional quando o cabeçalho **Access-Control-Allow-Origin** for definido como caractere curinga (*) ou uma origem única. A CDN armazenará a primeira resposta em cache e as solicitações subsequentes usarão o mesmo cabeçalho.
 
Se já tiverem sido feitas solicitações à CDN antes de o CORS ser definido na origem, você precisará limpar o conteúdo no conteúdo do ponto de extremidade para recarregar o conteúdo com o cabeçalho **Access-Control-Allow-Origin**.
 
## Cenários de várias origens

Se você precisar permitir que uma lista específica de origens seja permitida para o CORS, isso será um pouco mais complicado. O problema ocorre quando a CDN armazena o cabeçalho **Access-Control-Allow-Origin** em cache para a primeira origem do CORS. Quando uma origem do CORS diferente fizer uma solicitação subsequente, a CDN terá fornecido o cabeçalho **Access-Control-Allow-Origin** armazenado em cache que não é correspondente. Há várias maneiras de corrigir o problema.
 
### CDN Premium do Azure da Verizon

A melhor maneira de habilitar essa opção é usar o **Azure CDN Premium da Verizon**, que expõe algumas funcionalidades avançadas.
 
Você precisará [criar uma regra](cdn-rules-engine.md) para verificar o cabeçalho **Origin** na solicitação. Se for uma origem válida, a regra definirá o cabeçalho **Access-Control-Allow-Origin** com a origem fornecida na solicitação. Se a origem especificada no cabeçalho **Origin** não for permitida, a regra deverá omitir o cabeçalho **Access-Control-Allow-Origin**, o que fará com que o navegador rejeite a solicitação.
 
Há duas maneiras de fazer isso com o mecanismo de regras. Em ambos os casos, o cabeçalho **Access-Control-Allow-Origin** do servidor de origem do arquivo é totalmente ignorado. O mecanismo de regras da CDN gerencia completamente as origens do CORS permitidas.

#### Uma expressão regular com todas as origens válidas
 
Nesse caso, você criará uma expressão regular que inclua todas as origens que deseja permitir:

	https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] O **Azure CDN da Verizon** usa [Expressões Regulares Compatíveis com Perl](http://pcre.org/) como seu mecanismo de expressões regulares. Você pode usar uma ferramenta como [Regular Expressions 101](https://regex101.com/) para validar a expressão regular. Observe que o caractere "/" é válido em expressões regulares e não precisa ter um caractere de escape. No entanto, adicionar um caractere de escape a esse caractere é considerado uma prática recomendada e é esperado por alguns validadores de regex.

Se a expressão regular for correspondente, a borda da CDN da regra substituirá o cabeçalho **Access-Control-Allow-Origin** (se houver) da origem pela origem que enviou a solicitação. Você também pode adicionar outros cabeçalhos CORS, como **Access-Control-Allow-Methods**.

![Exemplo de regras com expressões regulares](./media/cdn-cors/cdn-cors-regex.png)
 
#### Solicitar a regra de cabeçalho para cada origem.

Em vez de expressões regulares, você pode criar uma regra separada para cada origem que deseja permitir usando a **condição de correspondência** de [Curinga de Cabeçalho de Solicitação](cdn-rules-engine-details.md#match-conditions). Assim como acontece com o método de expressão regular, apenas o mecanismo de regras define os cabeçalhos do CORS.
  
![Exemplo de regras sem expressões regulares](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] No exemplo acima, o uso do caractere curinga * instrui o mecanismo de regras a corresponder HTTP e HTTPS.
 
### Padrão do Azure CDN

Em perfis Padrão do Azure CDN, o único mecanismo para permitir várias origens sem o uso da origem curinga é usar o [armazenamento em cache da cadeia de caracteres de consulta](cdn-query-string.md). Você precisa habilitar a configuração da cadeia de caracteres de consulta para o ponto de extremidade da CDN e usar uma cadeia de caracteres de consulta exclusiva para solicitações de cada domínio permitido. Isso fará com que a CDN armazene em cache um objeto separado para cada cadeia de caractere de consulta exclusiva. No entanto, essa abordagem não é ideal, pois resultará em várias cópias do mesmo arquivo armazenadas em cache na CDN.

<!---HONumber=AcomDC_0525_2016-->