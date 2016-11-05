<BR>

## Perguntas frequentes
### Qual é o custo dos registros DNS reversos?
Eles são gratuitos! Não há nenhum custo adicional para registros DNS reversos ou consultas.

### Meus registros DNS reversos serão resolvidos na Internet?
Sim. Depois de definir a propriedade de DNS reverso para seu Endereço IP Público, o Azure gerenciará todas as delegações de DNS e as zonas de DNS necessárias para garantir que o registro DNS reverso seja resolvido para todos os usuários da Internet.

### Um registro DNS reverso padrão será criado para meus Endereços IP Públicos?
Não. O DNS reverso é um recurso opcional. Nenhum registro DNS reverso padrão é criado se você optar por não configurar um.

### Qual é o formato do FQDN (nome de domínio totalmente qualificado)?
Os FQDNs são especificados em ordem crescente e devem terminar com um ponto (por exemplo, “app1.contoso.com.”).

### O que acontecerá se ocorrer uma falha nas verificações de validação do DNS reverso especificado?
Quando ocorrer uma falha nas verificações de validação do DNS reverso, também ocorrerá uma falha na operação de gerenciamento de serviço. Corrija o valor DNS reverso conforme necessário e tente novamente.

### Posso gerenciar o DNS reverso para meu Site do Azure?
Não há suporte para o DNS reverso em Sites do Azure. Há suporte para o DNS reverso em Máquinas Virtuais do Azure.

### Posso configurar vários registros DNS reversos para meus Endereços IP Públicos?
Não. O Azure dá suporte a um único registro DNS reverso por Endereço IP Público. No entanto, cada Endereço IP Público pode ter seu próprio registro DNS reverso.

### Posso configurar um registro DNS reverso para meu Endereço IP Público sem ter um DomainNameLabel especificado?
Não. Para aproveitar os registros DNS reversos para Endereços IP Públicos, você deve especificar a propriedade DomainNameLabel.

### Posso hospedar as zonas ARPA para meu IPs atribuídos pelo Azure no DNS do Azure em minha própria assinatura ou em meus próprios servidores DNS autoritativos?
Não. O Azure não dá suporte à delegação progressiva de zonas ARPA. O Azure hospeda as zonas ARPA para todos os IPs disponíveis e permite aos clientes criar registros DNS reversos nessas zonas ARPA.

### Posso hospedar zonas ARPA para meus blocos IP atribuídos pelo ISP no DNS do Azure?
Não. Atualmente, o DNS do Azure não dá suporte a registros DNS reversos em zonas DNS de clientes.

### Posso enviar emails a domínios externos de meus Serviços de Computação do Azure?
Não. De acordo com [estas informações](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/), os Serviços de Computação do Azure não oferecem suporte ao envio de emails a domínios externos.

<!---HONumber=AcomDC_0907_2016-->