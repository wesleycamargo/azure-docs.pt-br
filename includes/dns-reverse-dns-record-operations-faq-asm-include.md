
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Perguntas frequentes - DNS reverso para o endereço IP atribuído pelo Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Qual é o custo dos registros DNS reversos?

Eles são gratuitos!  Não há nenhum custo adicional para registros DNS reversos ou consultas.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Meus registros DNS reversos serão resolvidos na Internet?

Sim. Depois de definir a propriedade de DNS reverso para seu Serviço de Nuvem, o Azure gerenciará todas as delegações de DNS e as zonas de DNS necessárias para garantir que o registro DNS reverso seja resolvido para todos os usuários da Internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>Um registro DNS reverso padrão será criado para meus Serviços de Nuvem?

Não. O DNS reverso é um recurso opcional. Nenhum registro DNS reverso padrão é criado se você optar por não configurar um.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Qual é o formato do FQDN (nome de domínio totalmente qualificado)?

Os FQDNs são especificados em ordem crescente e devem terminar com um ponto (por exemplo, “app1.contoso.com.”).

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>O que acontecerá se ocorrer uma falha nas verificações de validação do DNS reverso especificado?

Quando ocorrer uma falha nas verificações de validação do DNS reverso, também ocorrerá uma falha na operação de gerenciamento de serviço. Corrija o valor DNS reverso conforme necessário e tente novamente.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Posso gerenciar o DNS reverso para meu Site do Azure?

Não há suporte para o DNS reverso em Sites do Azure. Há suporte para o DNS reverso em funções de PaaS e em máquinas virtuais IaaS do Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>Posso configurar vários registros DNS reversos para meu Serviço de Nuvem?

Não. O Azure dá suporte a um único registro DNS reverso por Serviço de Nuvem do Azure. No entanto, cada Serviço de Nuvem do Azure pode ter seu próprio registro DNS reverso.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar emails a domínios externos de meus Serviços de Computação do Azure?

Não. [Os Serviços de Computação do Azure não oferecem suporte ao envio de emails a domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
