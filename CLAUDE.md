# Andressa Fitness App — Cowork Project

## Localização
- **Pasta do projeto:** `C:\Users\vinic\Downloads\Coding\andressa-treino\`
- **Ficheiro principal:** `index.html` (single-file app — HTML + CSS + JS tudo junto)
- **GitHub:** `viniciusros7/andressa-treino`
- **URL live:** `https://viniciusros7.github.io/andressa-treino`

## Sobre a Andressa
- Mãe do Miguel (nascido ~Jan 2026 — app agora na fase 6+ meses pós-parto)
- Treina no **Basic Fit Terneuzen**, Países Baixos
- Nível: iniciante/intermédio
- Idioma do app: **Português (PT-BR)**
- Contexto especial: possível diastasia abdominal — exercícios de core são todos de ativação profunda (nunca pressão/crunch)

---

## Arquitetura atual do app

### Stack
- **HTML/CSS/JS puro** — sem frameworks, sem dependências externas
- Google Fonts: `Nunito` + `Playfair Display`
- Estado persistido via `localStorage` (sem backend)
- PWA básico: `manifest.json` + `icon.png` (ficheiros separados no repo)

### Tabs de navegação (5)
| Tab | ID | Função |
|---|---|---|
| 🏠 Início | `sec-home` | Streak, stats, week tracker, próximo treino |
| ⚙️ Configurar | `sec-config` | Dias/semana (2-3-4), duração (30-75min), nível de bem-estar, diastasia |
| 📅 Plano | `sec-plano` | Cards gerados dinamicamente por dia da semana |
| 📊 Histórico | `sec-historico` | Últimas 4 semanas, pesos usados, exercícios feitos |
| 💡 Dicas | `sec-dicas` | Guia pós-parto, nutrição, alertas de segurança |

### Tipos de dia de treino (PLAN_TYPES) — v2: fase 6m+, 6 exercícios/dia
- **Dia A** — Core & Assoalho Pélvico (+ Pallof press; progressões com pausa/carga leve) · met 3.0
- **Dia B** — Glúteos & Pernas (+ Stiff/RDL; progressão de carga explícita nos details) · met 5.0
- **Dia C** — Cardio & Mobilidade (+ gato-camelo; intervalos leves na esteira) · met 6.0
- **Dia D** — Parte Superior Leve (+ desenvolvimento de ombros sentada) · met 4.0

**Regra de compatibilidade:** os 5 exercícios originais de cada dia mantêm ordem e (quase todos) o nome — o 6º foi APENAS acrescentado no fim. Isto preserva `checks` (por índice) e `pesos` (por nome). `migratePlanData()` atualiza o planData guardado no arranque.

### Sequências por dias/semana
```js
DAY_SEQUENCES = { 2: ["A","B"], 3: ["A","B","C"], 4: ["A","B","C","D"] }
```

### Estado guardado em localStorage
```js
cfg         = { days, dur, feel, diastasia, bodyW }   // bodyW = peso corporal (v2, para kcal)
planData    = array de dias com exercícios
checks      = { "YYYY-MM-DD_exIdx": true }   // exercícios marcados
pesos       = { "exName": "5kg" }            // último peso por exercício
dayDone     = { "YYYY-MM-DD": true }         // dias concluídos
weightsHist = { "exName": [{date, weight}] } // histórico de pesos (alimenta o gráfico)
kcalLog     = { "YYYY-MM-DD": 262 }          // v2: kcal estimadas por treino concluído
badges      = { "badgeId": "YYYY-MM-DD" }    // v2: conquistas + data
```

### Cada exercício tem
- `name`, `zone`, `detail`, `tip`
- `ytId` (vídeo YouTube embutido click-to-play) **ou** `ytQ` (query de pesquisa YouTube, para exercícios novos sem ID verificado)
- `anim` — chave da animação SVG em `ANIMS` (bonequinho SMIL do movimento)

### Features v2
- **Vídeo embutido**: `playVideo()` troca a animação por iframe `youtube-nocookie` com autoplay; `closeVideo()` restaura
- **Animações SVG**: objeto `ANIMS` com ~24 padrões de movimento (SMIL, sem dependências)
- **Kcal**: `estimateKcal(dayType)` = MET × 3.5 × bodyW / 200 × duração; registado em `kcalLog` ao concluir o dia; total na Home, chip no Histórico
- **Cronómetro de descanso**: FAB visível na tab Plano (45/60/90s), beep WebAudio + vibração
- **Conquistas**: `BADGES` (8), `checkBadges(announce)` — silencioso no arranque, toast em ações
- **Gráfico de cargas**: canvas puro no Histórico, select por exercício, dados de `weightsHist`
- **Streak v2**: conta dias DE TREINO consecutivos — dias de descanso não quebram a sequência

---

## Regras para novas features

### ✅ SEMPRE fazer
- Manter **single-file HTML** — todo o código vai dentro de `index.html`
- Preservar o `localStorage` como mecanismo de estado (sem backend)
- Manter o design system existente — usar variáveis CSS (`--primary`, `--text`, `--card`, etc.)
- Usar fontes já carregadas: `Nunito` e `Playfair Display`
- Comentar código novo em **português**
- Testar que funciona em mobile (max-width: 480px)
- **Segurança pós-parto em primeiro lugar** — nunca sugerir exercícios com pressão abdominal alta

### ❌ NUNCA fazer
- Adicionar frameworks (React, Vue, etc.) ou npm
- Criar ficheiros separados de JS ou CSS (exceto `manifest.json` e `icon.png` que já existem)
- Adicionar exercícios de crunch, sit-up, ou qualquer movimento que crie pressão abdominal
- Remover ou alterar as dicas de segurança na tab "Dicas"
- Quebrar compatibilidade com dados já guardados no localStorage (não renomear keys existentes)

---

## Features futuras planeadas (backlog)
- [x] **Tracker de progressão de pesos** — feito na v2 (gráfico canvas no Histórico)
- [ ] **Modo recuperação** — semanas de baixa intensidade (deload) automáticas a cada 4 semanas
- [ ] **Plano nutricional expandido** — substituir grid estático por calculadora com peso/amamentação
- [ ] **Notificações de lembrete** — Web Notifications API para lembrar dias de treino
- [ ] **Partilha de conquistas** — imagem de streak partilhável (canvas API)
- [ ] **Modo escuro** — toggle entre light/dark usando as variáveis CSS existentes
- [ ] **Sons de motivação** — áudio curto ao marcar treino como concluído (beep do timer já existe)
- [ ] **ytId verificados** — os 4 exercícios novos usam `ytQ` (pesquisa); substituir por `ytId` reais após confirmar bons vídeos

---

## Deploy / Git workflow
1. Editar `index.html` localmente
2. Testar no browser (abrir o ficheiro direto ou com Live Server)
3. `git add index.html && git commit -m "descrição" && git push`
4. GitHub Pages atualiza automaticamente em ~1 min

## Notas do Vinicius
- O app foi construído para a Andressa usar no telemóvel no ginásio
- Miguel é o bebé (filho do Vinicius e Andressa, nascido ~Jan 2026)
- Basic Fit Terneuzen tem: Free Weight Zone, Functional Zone, Strength Zone, Cardio Zone, Stretch Zone
- Vinicius prefere soluções simples e single-file — evitar complexidade desnecessária
