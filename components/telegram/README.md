# Telegram

Para utilizar o Telegram com o Home Assistant vamos precisar criar um bot. É só seguir [essas instuções](https://core.telegram.org/bots).

## Configuração do Home Assistante

```
notify:
  platform: telegram
  api_key: [SUA-CHAVE]
  chat_id: [ID-DO-SEU-BOT]
```
