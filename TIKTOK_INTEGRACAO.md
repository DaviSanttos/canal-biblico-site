# Integração TikTok — guia para o projeto channel-server

App no TikTok for Developers: **Canal Dark Bíblico** (id `7710967041901168660`), produtos **Login Kit** +
**Content Posting API**, escopo **`video.upload`** (rascunho na caixa de entrada; NÃO usar `video.publish`).
Credenciais (Client Key / Client Secret) vão no `.env` do channel-server, nunca no git.

## URLs já publicadas e verificadas (este repositório, GitHub Pages)
- Site / plataforma Web: `https://davisanttos.github.io/canal-biblico-site/`
- Terms: `https://davisanttos.github.io/canal-biblico-site/terms.html`
- Privacy: `https://davisanttos.github.io/canal-biblico-site/privacy.html`
- **Redirect URI do Login Kit:** `https://davisanttos.github.io/canal-biblico-site/callback.html`
  - Essa página só repassa a query (`?code=...&state=...`) para **`http://127.0.0.1:17563/tiktok/callback`**.
  - Ou seja: o app local precisa subir um servidor HTTP em `127.0.0.1:17563` durante o login, receber
    `code` + `state`, validar o `state` e trocar o `code` por token.

## Fluxo a implementar (Node/TS)
1. **Login (uma vez):** abrir no navegador
   `https://www.tiktok.com/v2/auth/authorize/?client_key=...&scope=user.info.basic,video.upload&response_type=code&redirect_uri=<callback.html>&state=<aleatorio>`
   (conferir parâmetros atuais — incl. PKCE se exigido — na doc oficial do Login Kit para Web/Desktop).
   Trocar o code em `POST https://open.tiktokapis.com/v2/oauth/token/`; guardar access + refresh token
   **localmente** (arquivo fora do git); renovar com refresh_token.
2. **Envio como rascunho:** `POST https://open.tiktokapis.com/v2/post/publish/inbox/video/init/` com
   `source_info.source = FILE_UPLOAD` (+ tamanho/chunks), depois `PUT` do arquivo na `upload_url`
   retornada; acompanhar com `/v2/post/publish/status/fetch/`.
3. **Discord:** botão "Aprovar → enviar ao TikTok" no fluxo de aprovação; registrar o `publish_id` e o
   resultado no canal da categoria "📖 canal dark bíblico".

## Sandbox e revisão
- Antes da aprovação, usar o **Sandbox** do app (até 10 contas próprias em "Target users").
- Revisão exige **vídeo de demonstração (mp4/mov, ≤ 50 MB)** mostrando o fluxo completo: aprovação no
  Discord → tela de autorização do TikTok (domínio do site visível) → envio → rascunho aparecendo no
  app do TikTok. O texto de explicação já foi preenchido no formulário.

Docs oficiais: https://developers.tiktok.com/doc/content-posting-api-get-started ·
https://developers.tiktok.com/doc/content-posting-api-get-started-upload-content ·
https://developers.tiktok.com/doc/add-a-sandbox
