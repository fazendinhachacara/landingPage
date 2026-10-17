# chacaraFazendinha
Landing page da Assembleia de Deus Chácara Fazendinha.

## Como atualizar
- Textos e seções: edite o `index.html`.
- Horários dos cultos: edite a lista `CULTOS` (e `DOMINGOS`) no script no fim do `index.html`.
- Imagens: ficam na pasta `img/`. Para trocar uma foto, envie um arquivo com o mesmo nome.

O endereço do site não muda com as atualizações, então o QR Code impresso continua funcionando.

## Pedidos de oração e visita
A seção de pedidos no site grava no Firebase (plano gratuito), e a liderança consulta em `pedidos.html`.

**Antes de funcionar, é preciso configurar o Firebase uma vez.** O passo a passo está em [CONFIGURACAO-FIREBASE.md](CONFIGURACAO-FIREBASE.md).

| Arquivo | Para que serve |
| --- | --- |
| `firebase-config.js` | Onde ficam as chaves do projeto Firebase. |
| `firestore.rules` | Regras de segurança. É o que impede que os pedidos fiquem públicos — precisam ser publicadas no console do Firebase. |
| `pedidos.html` | Página da liderança, protegida por login. |
