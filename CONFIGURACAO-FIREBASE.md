# Como ligar os pedidos de oração e visita

Os formulários do site já estão prontos, mas ainda não têm onde guardar os pedidos. Este guia liga o site ao Firebase, que é o serviço do Google onde os pedidos vão ficar.

**Custo:** nenhum. O plano gratuito (chamado **Spark**) permite 20.000 gravações e 50.000 leituras **por dia**. Uma igreja que receba 30 pedidos por mês usa 30 das cerca de 600.000 gravações disponíveis no mês. Não é preciso cadastrar cartão de crédito, e se um dia o limite fosse atingido o Firebase apenas bloquearia até o mês virar — nunca gera cobrança.

**Tempo:** cerca de 20 minutos, uma vez só.

---

## 1. Criar o projeto no Firebase

1. Entre em <https://console.firebase.google.com> com a conta Google da igreja.
2. Clique em **Criar projeto**.
3. Nome do projeto: `chacara-fazendinha` (ou outro de sua preferência).
4. Quando perguntar sobre o **Google Analytics**, pode **desativar**. Não usamos.
5. Espere o projeto ser criado e clique em **Continuar**.

## 2. Criar o banco de dados

1. No menu da esquerda, abra **Criar** (ou *Build*) → **Firestore Database**.
2. Clique em **Criar banco de dados**.
3. Escolha o local **`southamerica-east1` (São Paulo)** — é o mais perto, então o site responde mais rápido.
4. Quando perguntar o modo, escolha **Iniciar no modo de produção**. Isso deixa tudo fechado, e no próximo passo nós abrimos só o necessário.

## 3. Publicar as regras de segurança ⚠️

**Este é o passo mais importante do guia.** São as regras que impedem que os pedidos de oração — que têm informação sensível sobre saúde e família das pessoas — fiquem abertos para qualquer um na internet.

1. Ainda no Firestore Database, abra a aba **Regras** (*Rules*).
2. Apague todo o conteúdo que está lá.
3. Abra o arquivo `firestore.rules` deste repositório, copie **todo** o conteúdo e cole no lugar.
4. Clique em **Publicar**.

O que essas regras fazem, em resumo: qualquer visitante do site **pode enviar** um pedido, mas **só quem está logado consegue ler**. Ninguém consegue apagar nada pelo site, e a liderança só pode alterar o acompanhamento (situação e anotação) — o texto que a pessoa escreveu fica intocável.

## 4. Ligar o login da liderança

1. No menu da esquerda, abra **Criar** → **Authentication** e clique em **Primeiros passos**.
2. Na aba **Sign-in method** (Método de login), escolha **E-mail/senha**, **ative** e salve.
3. Vá para a aba **Users** (Usuários) e clique em **Adicionar usuário**.
4. Cadastre o e-mail e uma senha para cada pessoa que vai ver os pedidos (os pastores, a liderança do grupo de oração).

> Não existe "criar conta" na página de pedidos, e isso é de propósito: só entra quem foi cadastrado aqui. Para dar acesso a mais alguém, volte nesta tela.

## 5. Pegar as chaves e colar no site

1. Clique na **engrenagem** no topo do menu da esquerda → **Configurações do projeto**.
2. Role até **Seus aplicativos** e clique no ícone **`</>`** (Web).
3. Apelido do app: `site` . **Não** marque "Firebase Hosting" — o site já está no GitHub Pages.
4. Clique em **Registrar app**. O Firebase vai mostrar um bloco de código com `apiKey`, `authDomain`, `projectId` e os demais.
5. Abra o arquivo `firebase-config.js` deste repositório e substitua cada `COLE_AQUI` pelo valor correspondente, mantendo as aspas.

Exemplo de como fica depois de preenchido:

```js
export const firebaseConfig = {
  apiKey: 'AIzaSyB...',
  authDomain: 'chacara-fazendinha.firebaseapp.com',
  projectId: 'chacara-fazendinha',
  storageBucket: 'chacara-fazendinha.firebasestorage.app',
  messagingSenderId: '123456789012',
  appId: '1:123456789012:web:abc123...'
};
```

> Estas chaves ficam visíveis no código do site, e **isso é normal e esperado** — todo site estático funciona assim. Elas apenas dizem *qual* é o projeto; quem decide *o que cada pessoa pode fazer* são as regras do passo 3. Por isso aquele passo não pode ser pulado.

## 6. Liberar o endereço do site

1. Em **Authentication** → aba **Settings** (Configurações) → **Domínios autorizados**.
2. Confira se o endereço do site está na lista. Se não estiver, clique em **Adicionar domínio** e informe o domínio do GitHub Pages (algo como `jhownantes.github.io`).

## 7. Publicar e testar

1. Envie as alterações para o GitHub (`git push`). O GitHub Pages atualiza sozinho em um ou dois minutos.
2. Abra o site e envie um pedido de teste em cada um dos dois formulários.
3. Abra `SEU-SITE/pedidos.html`, entre com um dos e-mails cadastrados no passo 4 e confira se os dois pedidos apareceram.
4. Apague os pedidos de teste pelo console do Firebase (Firestore Database → coleção `pedidos`). De propósito, não dá para apagar pela página.

---

## Como a liderança usa no dia a dia

Entre em `SEU-SITE/pedidos.html` pelo celular ou computador e faça login. Dá para:

- Filtrar por **tipo** (oração ou visita) e por **situação** (pendentes, atendidos, todos).
- Clicar no telefone para abrir direto a conversa no WhatsApp.
- Marcar como **atendido** — fica registrado qual e-mail marcou e quando.
- **Anotar** uma observação no pedido (ex.: "liguei, vamos sábado").

Vale combinar com a liderança de salvar o endereço nos favoritos do celular.

### Duas etiquetas que pedem atenção

- **Anônimo** — a pessoa preferiu não se identificar. Não há como responder a ela; o pedido é só para oração.
- **Não compartilhar** — a pessoa **não** autorizou levar o pedido ao grupo de oração. Nesse caso, ele deve ficar só entre os pastores.

---

## Coisas que podem ser feitas depois

Nada disso é necessário agora, mas fica registrado:

- **Aviso de pedido novo.** Hoje é preciso abrir a página para ver se chegou algo. Dá para configurar um e-mail ou mensagem automática a cada pedido novo.
- **Proteção contra robô.** Os formulários já têm um campo-armadilha que descarta envio automático simples, mas não há captcha. Se começar a chegar spam, o caminho é ativar o **Firebase App Check** (também gratuito).
- **Limpeza periódica.** Pedidos antigos podem ser apagados de tempos em tempos pelo console, para não acumular dado pessoal sem necessidade.

## Em caso de problema

| O que acontece | Causa mais provável |
| --- | --- |
| "Não conseguimos enviar agora" ao enviar um pedido | As regras do passo 3 não foram publicadas, ou o `firebase-config.js` ainda está com `COLE_AQUI`. |
| A página de pedidos diz que não conseguiu carregar | As regras foram publicadas pela metade. Refaça o passo 3 colando o arquivo inteiro. |
| "E-mail ou senha incorretos" com a senha certa | O usuário não foi cadastrado em Authentication → Users (passo 4). |
| O projeto sumiu / nada funciona depois de semanas parado | Não deve acontecer: o plano Spark do Firebase **não** pausa por inatividade. Confira se o projeto não foi excluído na conta Google. |
