#### <a name="create-an-a-record-set-with-single-record"></a>Criar um conjunto de registros A com registro único

Para criar um conjunto de registros, use `azure network dns record-set create`. Especifique o grupo de recursos, o nome da zona, o nome relativo do conjunto de registros, o tipo de registro e a TTL (vida útil).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Depois de criar o conjunto de registros A, adicione o endereço IPv4 ao conjunto de registro com `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registros AAAA com um registro único

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registros CNAME com um registro único

Os registros CNAME permitem apenas um valor de cadeia de caracteres único.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registros MX com um registro único

Neste exemplo, usamos o nome do conjunto de registros "@" para criar o registro MX no vértice da zona (nesse caso, "contoso.com"). Isso é comum para os registros MX.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registros NS com um registro único

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registros PTR com um único registro  
Nesse caso, “my-arpa-zone.com” representa a zona ARPA que representa o intervalo de IP.  Cada registro PTR definido nesta zona corresponde a um endereço IP nesse intervalo de IP.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registros SRV com um registro único

Se você estiver criando um registro SRV na raiz da zona, poderá especificar “_service” e “_protocol” no nome do registro. Não é necessário incluir "@" no nome do registro.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>Criar um conjunto de registros TXT com um registro único

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"


<!--HONumber=Oct16_HO2-->


