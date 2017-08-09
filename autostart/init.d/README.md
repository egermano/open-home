# Auto start com `init/d`

Se o sistema que você instalou o home assistant em um sistema que usa o `init.d` como inicializador de serviços você precisa seguir esse tutorial.

## Copiar o script

Primeiro copie o script de inicialização para esse caminho `/etc/init.d/hass-daemon`. É só executar o comando a seguir:

```
sudo wget https://raw.githubusercontent.com/egermano/open-home/master/autostart/init.d/hass-daemon -O /etc/init.d/hass-daemon
```

Torne esse script executável:

```
sudo chmod +x /etc/init.d/hass-daemon
```

Agora você vai registrar e instalar o serviço:

```
sudo update-rc.d hass-daemon defaults
sudo service hass-daemon install
```

Agora reinicie a máquina, se o Home Assistant não inicializar confira o log de erro nesse arquivo `/var/opt/homeassistant/home-assistant.log`.