1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger. Execute os seguintes comandos em um terminal:
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Para instalar o Serviço de Mobilidade, execute o seguinte comando:

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de linha de comando do instalador do Serviço de Mobilidade

|Parâmetro|Tipo|Descrição|Valores possíveis|
|-|-|-|-|
|-t |Obrigatório|Tipo de agente<br>(a ser substituído em versão futura)|*both*|
|-a |Obrigatório|Configuração do agente<br>(a ser substituído em versão futura) |*host*|
|-R |Opcional|Função do agente|Agente<br>MasterTarget|
|-d |Opcional|Local onde o Serviço de Mobilidade será instalado|/usr/local/ASR|
|-i |Obrigatório|Endereço IP do servidor de configuração|Qualquer endereço IP válido|
|-p |Obrigatório|Porta na qual o servidor de configuração escuta as conexões de entrada|443|
|-s |Obrigatório|Inicia o serviço após a instalação bem-sucedida<br>(a ser substituído em versão futura)|*y*|
|-c |Obrigatório|Modo de comunicação entre o agente e o servidor de processo<br>(a ser substituído em versão futura) |*https*|
|-P |Obrigatório|Frase secreta do servidor de configuração|Qualquer caminho de arquivo UNC ou local válido|


#### <a name="example"></a>Exemplo
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```
