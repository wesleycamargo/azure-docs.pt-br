##Soluções altamente disponíveis com o gerenciador de tráfego do Azure

Você precisa determinar se os requisitos de alta disponibilidade da carga de trabalho podem ser atendidos usando o gerenciador de tráfego do Azure sozinho ou se precisa combinar o gerenciador de tráfego com outras soluções DNS ou processos. Dependendo das suas necessidades, você pode usar:

- **Somente Gerenciador de Tráfego** Se um tempo de atividade de 99,99% é suficiente para sua carga de trabalho, você pode usar o gerenciador de tráfego por si só. No caso de falha no serviço de gerenciador de tráfego, os usuários não poderão acessar sua carga de trabalho até que o serviço de gerenciador de tráfego seja restabelecido.

- **Usar outra solução de gerenciador de tráfego, juntamente com o gerenciador de tráfego do Azure**. No caso de falha no serviço de gerenciador de tráfego, você pode alterar o registro CNAME para apontar para o outro serviço de gerenciador de tráfego. O acesso a sua carga de trabalho ainda está disponível e é distribuído para todos os locais que hospedam sua carga de trabalho. Isso é a solução mais cara, mas pode ser necessário para cargas de trabalho que precisam de um SLA mais alto.
