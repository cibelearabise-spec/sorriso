# Smile Analysis

App de leitura estética do sorriso (foto → IA → score, preocupações e rotina de higiene bucal).
Front em HTML puro + função serverless na Vercel chamando o Gemini. Mesma arquitetura do app de pele original.

## Estrutura

```
smile-analysis/
├── index.html          # front-end (uma página só)
└── api/
    └── analyze.js       # função serverless que chama o Gemini
```

## O que muda em relação ao app de pele

- **Métricas (0–100, quanto maior melhor):** alinhamento, tom dos dentes, simetria/linha do sorriso, saúde aparente da gengiva.
- **"Ingredientes" virou "Higiene":** produtos e hábitos de higiene bucal (creme dental, fio dental, enxaguante, limpador de língua).
- **Rotina:** passos de higiene bucal de manhã e à noite, em vez de skincare.
- **Prompt do backend:** leitura só estética, sem diagnóstico, sem citar doenças, sem indicar tratamento, aparelho, clareamento ou medicamento. Se notar algo suspeito (mancha isolada, ferida, sangramento), o app marca `see_professional` e sugere procurar um dentista, sem nomear causas.
- **Textos de privacidade e avisos** adaptados para foto do sorriso/boca em vez de rosto/pele.
- **Paleta visual** ajustada (tom pérola/champanhe em vez de lilás), mantendo o mesmo layout "glass" sofisticado.

## Passo a passo para publicar num repositório novo

### 1. Crie o repositório
1. No GitHub, crie um repositório novo (ex.: `smile-analysis`), vazio, sem README.
2. No seu computador, dentro de uma pasta vazia, coloque os arquivos `index.html` e `api/analyze.js` (mantendo a pasta `api/`).
3. Rode:
   ```bash
   git init
   git add .
   git commit -m "Smile Analysis - versão inicial"
   git branch -M main
   git remote add origin https://github.com/SEU-USUARIO/smile-analysis.git
   git push -u origin main
   ```

### 2. Consiga uma chave da API Gemini
1. Acesse https://aistudio.google.com/apikey e gere uma chave (Google AI Studio).
2. Guarde essa chave — ela vai virar uma variável de ambiente, nunca vai para o código.

### 3. Publique na Vercel
1. Acesse https://vercel.com e faça login (pode usar sua conta do GitHub).
2. Clique em **Add New → Project** e importe o repositório `smile-analysis`.
3. Em **Environment Variables**, adicione:
   - `GEMINI_API_KEY` → a chave que você gerou no passo 2.
   - (opcional) `GEMINI_MODEL` → só se quiser fixar um modelo específico; o padrão já é `gemini-flash-latest`.
   - (opcional) `ALLOWED_ORIGIN` → só se o `index.html` for servido de outro domínio (ex.: app empacotado). Coloque a(s) origem(ns) separadas por vírgula, ex.: `https://meusite.com`.
4. Clique em **Deploy**. A Vercel detecta `api/analyze.js` automaticamente como uma função serverless e serve `index.html` como página estática.
5. Ao terminar, você recebe uma URL do tipo `https://smile-analysis-xxxx.vercel.app`. Abra e teste:
   - Toque em "Analisar meu sorriso" → autorize a câmera → tire uma foto sorrindo com os dentes à mostra.
   - Em alguns segundos deve aparecer o score, as preocupações e os produtos de higiene sugeridos.

### 4. Testar sem gastar chamadas de API
- No app, o botão **"Ver exemplo"** (aparece na tela de erro, ou acessando a URL com `?demo` no final, ex. `https://seu-projeto.vercel.app/?demo`) mostra um resultado de demonstração sem chamar o backend.

### 5. Domínio próprio (opcional)
- Em **Project → Settings → Domains**, na Vercel, você pode apontar um domínio próprio para o projeto.

## Notas de segurança e privacidade já embutidas no código
- A chave da API fica só no servidor (variável de ambiente); nunca é exposta no HTML.
- A foto é redimensionada no navegador antes de enviar (máx. ~1024px, JPEG) e não é salva em disco no servidor nem em nenhum banco — só passa pela função serverless até o Gemini e volta.
- No aparelho do usuário, só ficam salvos (via `localStorage`): o último resultado em texto, os passos da rotina marcados e o consentimento dado. Tudo pode ser apagado na tela "Privacidade" → "Apagar meus dados".
- O texto de qualquer imagem é explicitamente ignorado como instrução no prompt, para reduzir risco de prompt injection via foto.
