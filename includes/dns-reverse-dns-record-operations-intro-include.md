## <a name="what-is-reverse-dns"></a>O que é DNS reverso?

Os registros convencionais de DNS permitem um mapeamento de um nome DNS (por exemplo, 'www.contoso.com’) para um endereço IP (como 64.4.6.100).  O DNS reverso permite a conversão de um endereço IP (64.4.6.100) novamente para um nome ('www.contoso.com').

Registros DNS reversos são usados em uma variedade de situações. Por exemplo, registros DNS reversos são amplamente usados no combate ao spam de email ao verificarem o remetente de uma mensagem de email.  O servidor de recebimento de email irá recuperar o registro de DNS reverso do endereço IP do servidor de envio e verificar se o host está autorizado a enviar email do domínio de origem. (Observe que, entretanto, os [Serviços de Computação do Azure não oferecem suporte ao envio de emails a domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)).

## <a name="how-reverse-dns-works"></a>Como funciona o DNS reverso

Os registros do DNS reverso são hospedados em zonas DNS especiais, conhecidas como zonas 'ARPA'.  Essas zonas formam uma hierarquia DNS separada em paralelo com a hierarquia normal de hospedagem de domínios como ‘contoso.com’.

Por exemplo, o registro DNS 'www.contoso.com' é implementado usando um registro DNS 'A' com o nome ‘www’ na zona ‘contoso.com’.  Este registro A aponta para o endereço IP correspondente, neste caso, 64.4.6.100.  A pesquisa inversa é implementada separadamente, usando um registro 'PTR' chamado '100' na zona '6.4.64.in-addr.arpa' (observe que os endereços IP são revertidos em zonas ARPA).  Esse registro PTR, caso tenha sido configurado corretamente, aponta para o nome ‘www.contoso.com’.

Quando uma organização recebe um bloco de endereços IP, também adquirem o direito de gerenciar a zona ARPA correspondente. As zonas ARPA correspondentes aos blocos de endereços IP usados pelo Azure são hospedadas e gerenciadas pela Microsoft. Seu ISP pode hospedar a zona ARPA para seus próprios endereços IP para você, ou pode permitir que você hospede a zona ARPA em um serviço DNS à sua escolha, como o Azure DNS.

> [!NOTE]
> As pesquisas DNS diretas e as pesquisas DNS inversas são implementadas em hierarquias de DNS separadas e paralelas. A pesquisa inversa para 'www.contoso.com' **não** é hospedada na zona ‘contoso.com’, mas na região ARPA para o bloco de endereços IP correspondente.

Para saber mais sobre DNS reverso, confira [Pesquisa de DNS reverso](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Suporte do Azure para DNS reverso

O Azure dá suporte a dois cenários separados relacionados ao DNS reverso:

1. Hospedagem da zona ARPA correspondente ao seu bloco de endereços IP.
2. Permitindo que você configure o registro DNS reverso para o endereço IP atribuído ao seu serviço do Azure.

Para oferecer suporte ao anterior, o Azure DNS pode ser usado para hospedar suas zonas ARPA e gerenciar os registros PTR para cada pesquisa de DNS reverso.  O processo de criação da zona ARPA, de configuração da delegação e de configuração de registros PTR é o mesmo para as zonas DNS regulares.  As únicas diferenças são que a delegação deve ser configurada por meio de seu ISP em vez de um registrador de DNS, e somente o tipo de registro PTR deve ser usado.

Para dar suporte à última opção, o Azure permite que você configure a pesquisa inversa para os endereços IP alocados para o serviço.  Essa pesquisa inversa é configurada pelo Azure como um registro PTR na zona ARPA correspondente.  Essas zonas ARPA, correspondentes a todos os intervalos IP usados pelo Azure, são hospedadas pela Microsoft. **O restante deste artigo descreve esse cenário detalhadamente.**
