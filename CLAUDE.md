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

### Tipos de dia de treino (PLAN_TYPES) — v2.1: fase 6m+, mais braço
- **Dia A** — Core, Pélvico & Braços (8 ex: core diastasia-safe + rosca e elevação lateral leves) · met 3.2
- **Dia B** — Glúteos & Pernas (6 ex, com Stiff/RDL) · met 5.0
- **Dia C** — Cardio & Mobilidade (6 ex — só no plano de 4 dias) · met 6.0
- **Dia D** — Braços & Ombros (8 ex: remada, pulldown, rosca, tríceps, prancha, desenvolvimento, flexão inclinada, elevação lateral) · met 4.0

**Regra de compatibilidade:** exercícios existentes mantêm ordem e nome — novos são APENAS acrescentados no fim. Isto preserva `checks` (por índice) e `pesos` (por nome). `migratePlanData()` atualiza o planData no arranque; `planVer` (localStorage, atual = 3) força rebuild da divisão quando a sequência muda.

### Sequências por dias/semana (v2.1 — braços entram nos 3 dias)
```js
DAY_SEQUENCES = { 2: ["A","B"], 3: ["A","B","D"], 4: ["A","B","D","C"] }
```
Feedback da Andressa (jul/2026): faltava treino de braço → Dia D (Braços & Ombros) substituiu o C no plano de 3 dias; cardio dedicado só com 4 dias (aquecimento cobre o resto).

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
planVer     = 3                              // v2.1: versão da divisão (rebuild automático se menor)
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
- [x] **ytId verificados** — todos os 26 exercícios têm `ytId` validado via oEmbed (jul/2026); 12 IDs antigos estavam mortos (404) e foram substituídos. Se um vídeo "morrer" no futuro, o campo `ytQ` continua suportado como fallback (abre pesquisa do YouTube)

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
