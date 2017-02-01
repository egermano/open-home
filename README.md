# Automação residencial com hardware e software open

[TOC]

## Introdução

Esse é um projeto de automação residência que usa, em sua maioria, apenas hardware e software open-source, isso é chamado por OpenHome. Aqui você vai ver como ter uma central que gerencia todos os seus devices, ou coisas, e também como montar, programar e automatizar algumas coisas para a sua casa.

## Automação

Automação é um sistema que emprega processos automáticos que comandam e controlam os mecanismos para seu próprio funcionamento. Esta palavra tem origem no grego autómatos que significa mover-se por si ou que se move sozinho.

O objetivo aqui é criar alguns projetinhos de internet das coisas (IoT), e automatizar eles, criar comandos remotos, coletar dados e fazer com que as coisas aprendam a se automatizar.

## Instlação do controlador

O projeto inteiro da OpenHome vai ser controlado pelo Home Assistant, uma plataforma de automação residencial open-source feita em Python 3.

### Você vai precisar

* 1 Raspberry Pi ou C.H.I.P
* Roteador WiFi
* Um computador com SSH

### Home Assistant

É uma plataforma open-source de automação residencial feita em Python 3. Ele vai ser como um sistema operacional para a sua casa.

#### Instalação

Se você está usando um Raspberry Pi você pode usar a a instalação completa (All-In-One Installer), as instruções estão [aqui](https://home-assistant.io/getting-started/installation-raspberry-pi-all-in-one/). Recentemente eles também lançaram uma imagem para Rpi, se vc quiser [saber mais](https://home-assistant.io/blog/2016/10/01/we-have-raspberry-image-now/).

Continuando a instalação passo a passo. Antes de mais nada precisamos atualizar o sistema, instalar o Python e todas as dependências.

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install python-pip python3-dev
sudo pip install --upgrade virtualenv
```

Depois criamos um novo usuário, `hass`, ele é quem vai executar o Home Assistant. Essa é uma boa pratica de segurança, para não expor o resto do seu sistema controlando acessos e permissões.


```
sudo adduser --system hass
```

Vamos criar uma pasta para a instalação do Home Assistant e mudamos o dono da pasta para o nosso novo usuário.

```
sudo mkdir /srv/hass
sudo chown hass /srv/hass
```

Vamos logar com o novo usuário e configurar o ambiente do Python (virtualenv) na pasta que acabamos de criar. Essa é uma boa prática para você não zuar o ambiente de outros aplicativos que já estão rodando no seu computador com as dependências.

```
sudo su -s /bin/bash hass
virtualenv -p python3 /srv/hass
source /srv/hass/bin/activate
```

Agora sim vamos a instalação do Home Assistant,

```
pip3 install --upgrade homeassistant
```

Por fim vamos executar o Home Assistant,

```
sudo -u hass -H /srv/hass/bin/hass
```

Para iniciar o Home Assistant no boot, sempre que seu computador ligar, vamos precisar criar um serviço para o `systemd`. Mas alguém já fez isso, é só fazer download e instalar o serviço.

```
sudo wget https://raw.githubusercontent.com/home-assistant/home-assistant/master/script/home-assistant%40.service -O /etc/systemd/system/home-assistant@hass.service
```

Como a gente criar um usuário para executar o Home Assistant Vamos precisar fazer uma pequena modificação no serviço. Precisamos subistituir `/usr/bin/hass` por `/srv/hass/bin/hass`. A linha que você vai precisar alterar se parece com isso: `ExecStart=/srv/hass/bin/hass --runner`.

```
sudo nano /etc/systemd/system/home-assistant@hass.service
```

Precisamos reiniciar o nosso serviço no `systemd` para que ele utilize essas novas configurações.

```
sudo systemctl --system daemon-reload
sudo systemctl enable home-assistant@hass
sudo systemctl start home-assistant@hass
```

No futuro, se você precisar atualizar o Home Assistant é só usar esses comandos:

```
sudo su -s /bin/bash hass
source /srv/hass/bin/activate
pip3 install --upgrade homeassistant
```

Arquivos importantes:


- Configuração: `/home/hass/.homeassistant/configuration.yaml`
- Logs: `/home/hass/.homeassistant/home-assistant.log`

A minha configuração completa está disponível [aqui](configuration/home_assistant).

Referências:

- [Installation in Virtualenv](https://home-assistant.io/getting-started/installation-virtualenv/)
- [Autostart Using Systemd](https://home-assistant.io/getting-started/autostart-systemd/)
