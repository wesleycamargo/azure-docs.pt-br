
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Perguntas frequentes - DNS reverso para o endereço IP atribuído pelo Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Qual é o custo dos registros DNS reversos?

Eles são gratuitos!  Não há nenhum custo adicional para registros DNS reversos ou consultas.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Os registros DNS reversos para meu endereço IP público atribuído pelo Azure serão resolvidos na internet?

Sim. Depois de definir a propriedade de DNS reverso para seu Endereço IP Público, o Azure gerenciará todas as delegações de DNS e as zonas de DNS necessárias para garantir que o registro DNS reverso seja resolvido para todos os usuários da Internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Um registro DNS reverso padrão será criado para meus Endereços IP Públicos?

Não. O DNS reverso é um recurso opcional. Nenhum registro DNS reverso padrão é criado se você optar por não configurar um.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Qual é o formato do FQDN (nome de domínio totalmente qualificado)?

Os FQDNs são especificados em ordem crescente e devem terminar com um ponto (por exemplo, “app1.contoso.com.”).

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>O que acontecerá se ocorrer uma falha nas verificações de validação do DNS reverso especificado?

Quando ocorrer uma falha nas verificações de validação do DNS reverso, também ocorrerá uma falha na operação de gerenciamento de serviço. Corrija o valor DNS reverso conforme necessário e tente novamente.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Posso gerenciar o DNS reverso para meu Site do Azure?

Não há suporte para o DNS reverso em Sites do Azure. Há suporte para o DNS reverso em Máquinas Virtuais do Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>Posso configurar vários registros DNS reversos para meus Endereços IP Públicos?

Não. O Azure dá suporte a um único registro DNS reverso por Endereço IP Público. No entanto, cada Endereço IP Público pode ter seu próprio registro DNS reverso.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>Posso configurar registros DNS reversos para meus Endereços IP Públicos IPv6?

Não.  No momento, os registros DNS reversos têm suporte somente para endereços IP públicos IPv4.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>Posso configurar um registro DNS reverso para meu Endereço IP Público sem ter um DomainNameLabel especificado?

Não. Para aproveitar os registros DNS reversos para Endereços IP Públicos, você deve especificar a propriedade DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar emails a domínios externos de meus Serviços de Computação do Azure?

Não. [Os Serviços de Computação do Azure não oferecem suporte ao envio de emails a domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
