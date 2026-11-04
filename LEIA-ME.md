# Feito em Casa

App para anotar, dia a dia, tudo o que você faz em casa. Funciona como atalho na tela inicial do iPhone, abre sem internet e guarda os dados no próprio aparelho.

## O que tem aqui

- `site/` — os arquivos que vão para o GitHub (arquivos soltos, sem pastas dentro)
- `worker/worker.js` — opcional: o lembrete com IA da Groq (a chave fica segura na Cloudflare)
- `LEIA-ME.md` — este guia

## 1. Colocar no ar com o GitHub Pages

1. No GitHub, crie um **repositório novo** (por exemplo `tarefas`), marcado como **Public**. Não use o repositório do site de horários.
2. Na página do repositório vazio, toque em **uploading an existing file**.
3. Envie **todos os arquivos de dentro da pasta `site`** (não a pasta em si), de uma vez só: `index.html`, `sw.js`, `manifest.webmanifest`, `favicon.svg`, os quatro `.png`, a fonte `.woff2` e `LICENSES.txt` (o `.nojekyll` é um arquivo vazio e invisível em alguns aparelhos; se não aparecer, pode ficar de fora). Depois toque em **Commit changes**.
4. Vá em **Settings → Pages**. Em **Source** escolha **Deploy from a branch**, em **Branch** escolha `main` e `/ (root)`, e toque em **Save**.
5. Espere 1 a 2 minutos. O endereço do app aparece no topo da página de Pages, algo como `https://mgmf9.github.io/tarefas/`.

Para atualizar no futuro: **Add file → Upload files** e envie de novo os arquivos de `site` (os de mesmo nome são substituídos). Se o app no celular não mostrar a versão nova, abra **Personalizar → Atualizar o app agora**.

## 2. Instalar no iPhone

1. Abra o endereço no **Safari** (precisa ser o Safari).
2. Toque em **Compartilhar** (quadrado com seta) e depois em **Adicionar à Tela de Início**.
3. Use sempre pelo ícone novo.

## 3. Seus dados e o backup

Os dados ficam só neste aparelho. Importante: no iPhone, o app da tela inicial e o Safari **não dividem os dados**. O que você anotar pelo ícone só aparece pelo ícone.

Por isso, de vez em quando: **Personalizar → Backup dos dados → Copiar backup** e cole em um lugar seguro (Notas, por exemplo). Para voltar: cole na caixa e toque em **Restaurar** (toque duas vezes para confirmar). Trocar de celular ou limpar os dados do Safari apaga tudo, então o backup é o seguro.

Se você já usou a versão de teste dentro do Claude, abra lá **Personalizar → Backup dos dados → Copiar backup** e restaure no app novo.

## 4. Lembrete com IA (opcional)

Sem isso o app mostra um lembrete em texto simples, calculado no próprio aparelho (também é útil). Com o Worker, a Groq escreve o lembrete.

A chave **nunca** pode ficar no site, porque qualquer pessoa conseguiria ver. Por isso ela fica guardada num Worker da Cloudflare, e o app só conversa com o Worker.

1. **Crie uma chave nova** na Groq (console.groq.com → API Keys) e apague a antiga: a que foi colada na conversa deve ser considerada vazada.
2. Na Cloudflare: **Workers & Pages → Create → Hello World**. Dê o nome `feito-em-casa-ia` e toque em **Deploy**.
3. Toque em **Edit code**, apague tudo, cole o conteúdo de `worker/worker.js` e toque em **Deploy**.
4. Em **Settings → Variables and Secrets** adicione:
   - `GROQ_API_KEY` como **Secret**: a chave nova
   - `ALLOWED_ORIGIN` como **Text**: `https://mgmf9.github.io` (só o endereço base, sem barra no fim e sem `/tarefas`)
   - `GROQ_MODEL` como **Text** (opcional): outro modelo da Groq, se um dia o padrão `openai/gpt-oss-20b` for desativado
5. Copie o endereço do Worker (termina em `.workers.dev`), abra o app em **Personalizar → Lembrete com IA**, cole, toque em **Salvar** e depois em **Testar**.

O Worker só aceita pedidos vindos do seu site, só repassa um resumo do dia (nomes das tarefas e contagens) e usa um texto de pedido fixo guardado nele. O plano grátis da Cloudflare e da Groq costuma sobrar para uso pessoal.

## Problemas comuns

- **Abri o app e os dados sumiram:** provavelmente você abriu pelo Safari em vez do ícone (ou o contrário). Cada um tem seus dados. Use o backup.
- **O app não atualiza:** **Personalizar → Atualizar o app agora**.
- **O Testar do lembrete falha:** confira se `ALLOWED_ORIGIN` é exatamente o endereço base do site e se a chave nova foi colada em `GROQ_API_KEY`.
- **Não consegui copiar o backup:** o texto aparece na caixa; selecione tudo e copie. Também dá para usar **Salvar arquivo** (e **Escolher arquivo** para restaurar).

## Licenças

Ícones: Lucide (ISC) e Tabler Icons (MIT). Fonte: Plus Jakarta Sans (SIL OFL 1.1). Os textos completos estão em `site/LICENSES.txt`.
