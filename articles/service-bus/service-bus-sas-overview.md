<properties
   pageTitle="Visão geral das Assinaturas de acesso compartilhado"
   description="O que são Assinaturas de acesso compartilhado, como elas funcionam e como usá-las por meio do nó, PHP e C#."
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="darosa"/>

# As Assinaturas de Acesso Compartilhado

*Assinaturas de acesso compartilhado*(SAS) são o mecanismo de segurança principal para o Barramento de Serviço, incluindo Hubs de eventos, sistema de mensagens agenciado (filas e tópicos) e sistema de mensagens de retransmissão. Este artigo discute as Assinaturas de acesso compartilhado, como elas funcionam e como usá-las de maneira independente da plataforma.

## Visão geral das SAS

Assinaturas de acesso compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. As SAS são um mecanismo extremamente poderoso usado por todos os serviços do Barramento de Serviço. Na utilização real, as SAS têm dois componentes: uma *Política de acesso Compartilhado* e uma *Assinatura de Acesso Compartilhado* (normalmente chamada de *token*).

Você pode encontrar informações mais detalhadas sobre as Assinaturas de Acesso Compartilhado com Barramento de Serviço em [Autenticação SAS com Barramento de Serviço](https://msdn.microsoft.com/library/azure/dn170477.aspx).

## Política de acesso compartilhado

Uma coisa importante para entender sobre as SAS é que tudo começa com uma política. Para cada política, você toma decisões quanto a três informações: **nome**, **escopo** e **permissões**. O **nome** é apenas isso, um nome exclusivo dentro do escopo. O escopo é bastante simples: é o URI do recurso em questão. Para um namespace do Barramento de Serviço, o escopo é o nome de domínio totalmente qualificado (FQDN), como **`https://<yournamespace>.servicebus.windows.net/`**.

As permissões disponíveis para uma política são bastante autoexplicativas:

  + Enviar
  + Escutar
  + Gerenciar

Depois de você criar a política, ela recebe uma *Chave primária* e uma *Chave secundária*. Essas chaves são criptograficamente fortes. Não as perca ou divulgue - elas sempre estarão disponíveis no portal. Você pode usar qualquer uma das chaves geradas e pode gerá-las novamente a qualquer momento. No entanto, se você gera novamente ou altera a chave primária da política, quaisquer Assinaturas de acesso compartilhado criadas por meio dela serão invalidadas.

Quando você cria um namespace do Barramento de Serviço, é criada automaticamente uma política para todo o namespace chamada **RootManageSharedAccessKey**, e essa política tem todas as permissões. Você não faz logon como **raiz**. Portanto, não use esta política a menos que haja um motivo muito bom. Você pode criar políticas adicionais na guia **Configurar** para o namespace no Portal de Gerenciamento do Azure. É importante observar que um único nível de árvore no Barramento de Serviço (namespace, fila, Hub de eventos etc.) só pode ter até 12 políticas anexadas a ele.

## Assinatura de Acesso Compartilhado (token)

A política em si não é o token de acesso para o Barramento de Serviço. Ela é o objeto por meio do qual o token de acesso é gerado - usando a chave primária ou secundária. O token é gerado ao criar cuidadosamente uma cadeia de caracteres no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Em que `signature-string` é o hash SHA-256 do escopo do token (\*\*escopo\*\* conforme descrito na seção anterior) com um CRLF anexado e uma hora de validade (em segundos desde: `00:00:00 UTC` em 1º de janeiro de 1970).

O hash é semelhante ao seguinte pseudocódigo e retorna 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Os valores não hash estão na cadeia de caracteres **SharedAccessSignature** para que o destinatário possa computar o hash com os mesmos parâmetros, para garantir que o mesmo resultado seja retornado. O URI especifica o escopo e o nome da chave identifica a política a ser usada para computar o hash. Isso é importante de um ponto de vista de segurança. Se a assinatura não coincidir com aquela que o destinatário (Barramento de Serviço) calcula, o acesso é negado. Nesse ponto, podemos ter certeza de que o remetente tinha acesso à chave e deve ter os direitos especificados na política.

## Gerando uma assinatura por meio de uma política

Como realmente fazer isso no código? Vamos dar uma olhada em alguns deles.

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## Usando a assinatura de acesso compartilhado
 
Agora que sabe como criar Assinaturas de acesso compartilhado para qualquer entidade no Barramento de Serviço, você está pronto para executar um HTTP POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
Lembre-se de que isso funciona para tudo. Você pode criar uma SAS para uma fila, tópico, assinatura, Hub de eventos ou retransmissão. Se você usar identidades por editor para Hubs de eventos, basta acrescentar `/publishers/< publisherid>`.

Se você fornecer a um remetente ou um cliente um token SAS, eles não têm a chave diretamente e não podem reverter o hash para obtê-la. Dessa forma, você tem controle sobre o que eles podem acessar e por quanto tempo. É importante se lembrar de que se você alterar a chave primária da política, quaisquer Assinaturas de acesso compartilhado criadas por meio dela serão invalidadas.

## Próximas etapas

Consulte a [Referência da API REST do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh780717.aspx) para obter mais informações sobre o que você pode fazer com esses tokens SAS.

Para obter mais informações sobre a SAS, consulte o nó [Autenticação do Barramento de Serviço](https://msdn.microsoft.com/library/azure/dn155925.aspx) no MSDN.

<!---HONumber=July15_HO5-->