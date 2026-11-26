# Feito em Casa

App para anotar, dia a dia, tudo o que você faz em casa. Funciona como atalho na tela inicial do iPhone, abre sem internet e guarda os dados no próprio aparelho. Tem agente com IA e um painel para os pais acompanharem.

## O que tem aqui

- Arquivos soltos na raiz (`index.html`, `sw.js`, `manifest.webmanifest`, `favicon.svg`, os quatro `.png`, a fonte `.woff2`, `LICENSES.txt`, `.nojekyll`): **o app**. É isso que o GitHub Pages publica.
- `worker/worker.js`: o Worker da Cloudflare. Ele dá a IA (lembrete e agente) e guarda os resultados do **painel dos pais**. A chave da Groq fica segura nele.
- `LEIA-ME.md`: este guia.

## 1. Colocar no ar com o GitHub Pages

1. No GitHub, abra o repositório (público).
2. Para enviar ou atualizar: **Add file → Upload files** e envie os arquivos do app. Os arquivos com o mesmo nome são substituídos. Depois toque em **Commit changes**.
3. Na primeira vez: **Settings → Pages**. Em **Source** escolha **Deploy from a branch**, em **Branch** escolha `main` e `/ (root)`, e toque em **Save**.
4. Espere 1 a 2 minutos. O endereço do app aparece no topo da página de Pages, algo como `https://mgmf9.github.io/Tarefa-casa/`.

Se o app no celular não mostrar a versão nova, abra **Personalizar → Atualizar o app agora**. Isso não apaga os seus dados.

## 2. Instalar no iPhone

1. Abra o endereço no **Safari** (precisa ser o Safari).
2. Toque em **Compartilhar** (quadrado com seta) e depois em **Adicionar à Tela de Início**.
3. Use sempre pelo ícone novo.

## 3. Seus dados e o backup

Os dados ficam só neste aparelho. Importante: no iPhone, o app da tela inicial e o Safari **não dividem os dados**. O que você anotar pelo ícone só aparece pelo ícone.

De vez em quando: **Personalizar → Backup dos dados → Copiar backup** e cole em um lugar seguro (Notas, por exemplo). Para voltar: cole na caixa e toque em **Restaurar** (toque duas vezes para confirmar). Trocar de celular ou limpar os dados do Safari apaga tudo, então o backup é o seguro.

## 4. O Worker (IA + painel dos pais)

Sem o Worker o app funciona normalmente: o lembrete e o agente usam respostas simples calculadas no aparelho, e o painel dos pais só aparece no seu próprio celular. Com o Worker, a Groq escreve o lembrete e responde o agente, e seus pais conseguem ver os resultados no celular deles.

A chave da Groq **nunca** pode ficar no site nem no GitHub, porque qualquer pessoa conseguiria ver. Ela fica guardada só na Cloudflare.

### 4.1 Criar o Worker (se ainda não tem)

1. Na Cloudflare: **Workers & Pages → Create → Hello World**. Dê o nome `feito-em-casa-ia` e toque em **Deploy**.

### 4.2 Colocar o código

1. Abra o Worker e toque em **Edit code**.
2. Apague tudo, cole o conteúdo inteiro de `worker/worker.js` e toque em **Deploy**.

Trocar o código **não** apaga as variáveis que você já tinha configurado.

### 4.3 Variáveis (Settings → Variables and Secrets)

- `GROQ_API_KEY` como **Secret**: a sua chave da Groq (começa com `gsk_`)
- `ALLOWED_ORIGIN` como **Text**: `https://mgmf9.github.io` (só o endereço base, sem barra no fim e sem o nome do repositório)
- `GROQ_MODEL` como **Text** (opcional): outro modelo da Groq, se um dia o padrão `openai/gpt-oss-20b` for desativado

### 4.4 Espaço para o painel dos pais (KV)

1. Na Cloudflare: **Storage & Databases → KV → Create** (ou **Create namespace**). Nome: `feito-em-casa`. Toque em **Add**/**Create**.
2. Volte ao Worker: **Settings → Bindings → Add → KV namespace**.
   - **Variable name**: `DADOS` (exatamente assim, em maiúsculas)
   - **KV namespace**: `feito-em-casa`
3. Toque em **Save**/**Deploy**.

### 4.5 Ligar no app

1. Copie o endereço do Worker (termina em `.workers.dev`).
2. No app: **☰ → Personalizar → Worker (IA e painel dos pais)**. Cole, toque em **Salvar** e depois em **Testar**. Deve aparecer "Funcionou: …".

## 5. Criar o link dos pais

1. No app (perfil **Filho**): **☰ → Personalizar → Painel dos pais → Criar link dos pais**.
2. Quando aparecer "Pronto!", toque em **Enviar link** e mande pelo WhatsApp.
3. Seus pais abrem o link no celular deles. O app abre direto no painel, só para ver: o que foi feito hoje e a que horas, o plano do dia, os últimos 7 dias e o que mais foi feito no mês. Atualiza sozinho a cada minuto (e no botão de atualizar).
4. Cada vez que você registra algo, o app envia os resultados para o painel em poucos segundos (precisa de internet nessa hora).
5. Para cortar o acesso: **Desligar** (toque duas vezes). O link antigo para de funcionar. Se quiser, crie um novo depois.

Em **☰ → O que os pais veem** você confere como fica a tela deles.

O Worker guarda só o que o painel mostra (serviços, rotina e os últimos 3 meses de registros). Só o seu aparelho consegue mudar esses dados; o link dos pais só lê.

## Problemas comuns

- **Abri o app e os dados sumiram:** provavelmente você abriu pelo Safari em vez do ícone (ou o contrário). Cada um tem seus dados. Use o backup.
- **O app não atualiza:** **Personalizar → Atualizar o app agora**.
- **O Testar falha com "Origem não permitida":** `ALLOWED_ORIGIN` precisa ser exatamente `https://mgmf9.github.io`.
- **"A Groq recusou a chave":** confira a chave em `GROQ_API_KEY` (sem espaços antes ou depois).
- **Criar link dos pais diz que falta o KV "DADOS":** faça o passo 4.4 e confira se o nome da variável é `DADOS`.
- **Os pais veem "Este link foi desligado ou ainda não recebeu dados":** o link foi desligado, ou o seu celular ainda não conseguiu enviar. Abra o app com internet e registre algo, ou crie um link novo.
- **Não consegui copiar o backup:** o texto aparece na caixa; selecione tudo e copie. Também dá para usar **Salvar arquivo** (e **Escolher arquivo** para restaurar).

## Licenças

Ícones: Lucide (ISC) e Tabler Icons (MIT). Fonte: Plus Jakarta Sans (SIL OFL 1.1). Os textos completos estão em `LICENSES.txt`.
