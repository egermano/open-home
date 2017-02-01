# Configuração TLS no Mosquitto

Para deixar o seu MQTT broker mais seguro eu sugiro usar o protocolo TLS de segurança no serviço do Mosquitto, nesse mini tutorail você vai aprender a fazer essa configuração.

NOTE: Todos esses procedimentos tem que ser executado no mesmo computador que está rodando o serviço do Mosquitto.

Crie uma pasta segura, onde ficarão os seus certificados.

```
mkdir myCA
chmod 700 myCA
cd myCA
```

Baixe e execute o script generate-CA.sh, que foi criado pelo projeto [Owntracks](https://github.com/owntracks/owntracks/wiki). Esse script cria os arquivos de autoridade do certificado (CA, certificate authority), cria também os certificados do servidor, e usa o CA para assinar o certificado.

```
wget https://github.com/egermano/tools/raw/master/TLS/generate-CA.sh .
bash ./generate-CA.sh
```
