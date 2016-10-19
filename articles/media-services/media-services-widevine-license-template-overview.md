<properties 
	pageTitle="Visão geral do modelo de licença do Widevine | Microsoft Azure" 
	description="Este tópico fornece uma visão geral de um modelo de licença do Widevine usado para configurar as licenças do Widevine." 
	authors="juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016"  
	ms.author="juliako"/>

#Visão geral do modelo de licença do Widevine

##Visão geral

Agora, os Serviços de Mídia do Azure permitem que você configure e solicite licenças do Widevine. Quando o player do usuário final tentar reproduzir o conteúdo protegido do Widevine, uma solicitação será enviada ao serviço de entrega de licença para a obtenção de uma licença. Se o serviço de licença aprova a solicitação, ele emite a licença que é enviada ao cliente e pode ser usada para descriptografar e reproduzir o conteúdo especificado.

A solicitação de licença do Widevine é formatada como uma mensagem JSON.

Observe que você pode optar por criar uma mensagem vazia sem valores, apenas "{}", e um modelo de licença será criado com todos os padrões.

	{  
	   “payload”:“<license challenge>”,
	   “content_id”: “<content id>” 
	   “provider”: ”<provider>”
	   “allowed_track_types”:“<types>”,
	   “content_key_specs”:[  
	      {  
	         “track_type”:“<track type 1>”
	      },
	      {  
	         “track_type”:“<track type 2>”
	      },
	      …
	   ],
	   “policy_overrides”:{  
	      “can_play”:<can play>,
	      “can persist”:<can persist>,
	      “can_renew”:<can renew>,
	      “rental_duration_seconds”:<rental duration>,
	      “playback_duration_seconds”:<playback duration>,
	      “license_duration_seconds”:<license duration>,
	      “renewal_recovery_duration_seconds”:<renewal recovery duration>,
	      “renewal_server_url”:”<renewal server url>”,
	      “renewal_delay_seconds”:<renewal delay>,
	      “renewal_retry_interval_seconds”:<renewal retry interval>,
	      “renew_with_usage”:<renew with usage>
	   }
	}

##Mensagem JSON

Nome | Valor | Descrição
---|---|---
payload |Cadeia codificada em Base64 |A solicitação de licença enviada por um cliente. 
content\_id | Cadeia codificada em Base64|Identificador usado para gerar KeyId(s) e chaves de conteúdo para cada content\_key\_specs.track\_type.
provider |string |Usado para pesquisar as políticas e chaves de conteúdo. Obrigatório.
policy\_name | string |Nome de uma política registrada anteriormente. Opcional
allowed\_track\_types | enum | SD\_ONLY ou SD\_HD. Controla quais chaves de conteúdo devem ser incluídas em uma licença
content\_key\_specs | matriz de estruturas JSON, confira **Especificações de chave de conteúdo** abaixo | Um controle mais refinado sobre quais chaves de conteúdo retornar. Confira Especificações de chave de conteúdo a seguir para obter detalhes. Apenas um entre allowed\_track\_types e content\_key\_specs pode ser especificado. 
use\_policy\_overrides\_exclusively | booliano. true ou false | Use os atributos de política especificados por policy\_overrides e omita todas as políticas armazenadas anteriormente.
policy\_overrides | Estrutura JSON, confira **Substituições de política** abaixo | Configurações de política para esta licença. Caso este ativo tenha uma política predefinida, esses valores especificados serão usados. 
session\_init | Estrutura JSON, confira **Inicialização da sessão** abaixo | Dados opcionais passados para a licença.
parse\_only | booliano. true ou false | A solicitação de licença é analisada, mas nenhuma licença é emitida. No entanto, os valores da solicitação de licença retornam na resposta.  

##Especificações de Chave de Conteúdo 

Se já houver uma política, não será necessário especificar qualquer um dos valores nas Especificações de Chave de Conteúdo. A política existente associada a este conteúdo será usada para determinar a proteção da saída, como HDCP e CGMS. Se uma política existente não estiver registrada no Servidor de Licenças do Widevine, o provedor de conteúdo poderá injetar os valores na solicitação de licença.


Cada content\_key\_specs deve ser especificado para todos os controles, independentemente da opção use\_policy\_overrides\_exclusively.


Nome | Valor | Descrição
---|---|---
content\_key\_specs. track\_type | string | Um nome de tipo de controle. Se content\_key\_specs for especificado na solicitação de licença, especifique de forma explícita todos os tipos de controle. Se você não fizer isso, haverá uma falha de reprodução após 10 segundos. 
content\_key\_specs <br/> security\_level | uint32 | Define os requisitos de robustez de reprodução do cliente. <br/> 1 - É necessário aplicar a criptografia whitebox baseada em software. <br/> 2 - É necessário aplicar a criptografia de software e um decodificador ofuscado. <br/> 3 - As principais operações de criptografia e de materiais devem ser executadas em um ambiente de execução confiável com suporte de hardware. <br/> 4 - A criptografia e decodificação do conteúdo devem ser executadas em um ambiente de execução confiável com suporte de hardware. <br/> 5 - A criptografia, decodificação e qualquer manipulação da mídia (compactada e descompactada) devem ser tratadas em um ambiente de execução confiável com suporte de hardware.  
content\_key\_specs <br/> required\_output\_protection.hdc | cadeia de caracteres - uma das seguintes: HDCP\_NONE, HDCP\_V1, HDCP\_V2 | Indica se HDCP é necessário
chave <br/>content\_key\_specs | Cadeia codificada em <br/>Base64|Chave de conteúdo a ser usada para este controle. Se for especificado, o track\_type ou a key\_id será obrigatório. Essa opção permite que o provedor de conteúdo insira a chave de conteúdo para este controle em vez de deixar o servidor de licença do Widevine gerar ou procurar uma chave.
content\_key\_specs.key\_id| Binário de cadeia de caracteres codificada em Base64, 16 bytes | Identificador exclusivo para a chave. 


##Substituições de Política 

Nome | Valor | Descrição
---|---|---
policy\_overrides. can\_play | booliano. true ou false | Indica que a reprodução do conteúdo é permitida. O padrão é falso.
policy\_overrides. can\_persist | booliano. true ou false |Indica que a licença pode ser persistente para o armazenamento não volátil para uso offline. O padrão é falso.
policy\_overrides. can\_renew | booliano true ou false |Indica que a renovação dessa licença é permitida. Se for true, a duração da licença poderá ser estendida por pulsação. O padrão é falso. 
policy\_overrides. license\_duration\_seconds | int64 | Indica o período para esta licença específica. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. 
policy\_overrides. rental\_duration\_seconds | int64 | Indica o período em que a reprodução é permitida. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. 
policy\_overrides. playback\_duration\_seconds | int64 | O período de exibição após o início da reprodução dentro da duração da licença. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. 
policy\_overrides. renewal\_server\_url |string | Todas as solicitações de pulsação (renovação) para esta licença devem ser direcionadas à URL especificada. Este campo só será usado se can\_renew for true.
policy\_overrides. renewal\_delay\_seconds |int64 |O número de segundos após license\_start\_time, antes da primeira tentativa de renovação. Este campo só será usado se can\_renew for true. O padrão é 0 
policy\_overrides. renewal\_retry\_interval\_seconds | int64 | Especifica o atraso em segundos entre as solicitações de renovação da licença subsequentes, em caso de falha. Este campo só será usado se can\_renew for true. 
policy\_overrides. renewal\_recovery\_duration\_seconds | int64 | O período durante o qual a reprodução é permitida enquanto ocorre a tentativa de renovação, porém sem êxito devido a problemas de back-end com o servidor de licença. Um valor 0 indica que não há qualquer limite para a duração. Este campo só será usado se can\_renew for true.
policy\_overrides. renew\_with\_usage | booliano true ou false |Indica que a licença deve ser enviada para renovação quando o uso for iniciado. Este campo só será usado se can\_renew for true. 

##Inicialização da sessão

Nome | Valor | Descrição
---|---|---
provider\_session\_token | Cadeia codificada em Base64 |Este token de sessão é repassado na licença e continuará a existir em renovações subsequentes. O token de sessão não persistirá além das sessões. 
provider\_client\_token | Cadeia codificada em Base64 | Token de cliente para envio na resposta da licença. Se a solicitação de licença contiver um token de cliente, esse valor será ignorado. O token do cliente persistirá além das sessões da licença.
override\_provider\_client\_token | booliano. true ou false |Se for false e a solicitação de licença contiver um token de cliente, use o token da solicitação mesmo se houver um token do cliente especificado nessa estrutura. Se for true, sempre use o token especificado nessa estrutura.

##Configurar suas licenças do Widevine usando tipos .NET

Os Serviços de Mídia fornecem APIs .NET que permitem a configuração de suas licenças do Widevine.

###Classes, como definido no SDK do .NET dos Serviços de Mídia

Veja a seguir as definições desses tipos.

	public class WidevineMessage
	{
	    public WidevineMessage();
	
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public AllowedTrackTypes? allowed_track_types { get; set; }
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public ContentKeySpecs[] content_key_specs { get; set; }
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public object policy_overrides { get; set; }
	}

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###Exemplo

O exemplo a seguir mostra como usar as APIs do .NET para configurar uma licença simples do Widevine.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Consulte também

[Usando a PlayReady e/ou a Criptografia Comum Dinâmica Widevine](media-services-protect-with-drm.md)

<!---HONumber=AcomDC_0928_2016-->