# Configuração TLS no Mosquitto

Para deixar o seu MQTT broker mais seguro eu sugiro usar o protocolo TLS de segurança no serviço do Mosquitto, nesse mini tutorail você vai aprender a fazer essa configuração.

NOTE: Todos esses procedimentos tem que ser executado no mesmo computador que está rodando o serviço do Mosquitto.

## Criando os Certificados

Crie uma pasta segura, onde ficarão os seus certificados.

```
mkdir myCA
chmod 700 myCA
cd myCA
```

Baixe e execute o script generate-CA.sh, que foi criado pelo projeto [Owntracks](https://github.com/owntracks/owntracks/wiki). Esse script cria os arquivos de autoridade do certificado (CA, certificate authority), cria também os certificados do servidor, e usa o CA para assinar o certificado.

```
wget https://raw.githubusercontent.com/egermano/open-home/master/mosquitto/tls/generate-CA.sh .
bash ./generate-CA.sh
```

Depois de executar esse script 6 arquivos serão criados: `ca.crt`, `ca.key`, `ca.srl`, `host.crt`,  `host.csr` e `host.key`. São eles certificados (.crt), chaves (.key), um requisitor (.csr) e um arquivo de registro de serial number (.slr) usado para assinar o processo.

Os arquivos `host` podem ter nomes diferentes no seu computador!

## Configurando o Mosquitto

Três desses arquivos precisam ser copiados para a pasta do Mosquito `/etc/mosquitto/`:

```
sudo cp ca.crt /etc/mosquitto/ca_certificates/
sudo cp host.crt host.key /etc/mosquitto/certs/
```

Atualize agora a configuração do mosquitto para usar os novos certificados `/etc/mosquitto/mosquitto.conf`, seu arquivo vai ficar mais ou mesno assim:

```
# mosquitto.conf
pid_file /var/run/mosquitto.pid
persistence true
persistence_location /var/lib/mosquitto/
log_dest file /var/log/mosquitto/mosquitto.log
cafile /etc/mosquitto/ca_certificates/ca.crt
certfile /etc/mosquitto/certs/host.crt
keyfile /etc/mosquitto/certs/host.key
```

Reinicie o serviço do Mosquitto para aplicar a nova configuração:

```
sudo service mosquitto restart
```

### Teste as novas configurações

Você pode testar as novas configurações usando o cliente `mosquitto_sub`. O registro do envio e recebimento de tópico signiica que a sua configuração está correta.

```
$ mosquitto_sub -t \$SYS/broker/bytes/\# -v --cafile ca.crt
$SYS/broker/bytes/received 65
$SYS/broker/bytes/sent 67
$SYS/broker/bytes/received 130
$SYS/broker/bytes/sent 196
```

## Configurando o Home Assistant

Na configuração do Home Assitant `/home/hass/.homeassistant/configuration.yaml` você precisa atualizar com o novo certificado.

Se o Mosquitto está funcionando em um computador diferente do Home Assistant você vai precisar copiar o arquivo do certificado `ca.crt`.

```yaml
mqtt:
  broker: 'localhost' #127.0.0.1
  port: 8883 #1883
  client_id: 'ha'
  username: 'ha'
  password: '[SUA-SENHA]'
  certificate: '/etc/mosquitto/certs/ca.crt' # aqui você coloca o caminho do certificado
```
