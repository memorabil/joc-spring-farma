# 🐝 Zzzumzi Quiz - Cât de sănătos ești?

Quiz interactiv pentru **Spring Farma** cu mascota Zzzumzi. Un joc distractiv care promovează farmacia online și mesajul "Sănătatea la un click distanță".

## 📁 Structura fișierelor

```
zzzumzi-quiz/
├── index.html              ← fișierul principal
├── images/
│   ├── zzzumzi.png         ← mascota
│   └── spring-farma-logo.png ← logo
└── README.md               ← acest fișier
```

## 🚀 Cum să pui quiz-ul online (3 variante)

### Varianta 1: GitHub Pages (recomandat, gratis)

1. Creează un repository nou pe GitHub (ex: `zzzumzi-quiz`)
2. Urcă toate fișierele din acest folder (inclusiv folderul `images/`)
3. Mergi la **Settings → Pages** din repository
4. La "Source" alege branch-ul `main` și folder `/ (root)`
5. Apasă **Save**
6. După ~1 minut, quiz-ul va fi live la: `https://USERNAME.github.io/zzzumzi-quiz/`

### Varianta 2: Netlify Drop (cel mai rapid, gratis)

1. Mergi pe https://app.netlify.com/drop
2. Trage tot folderul `zzzumzi-quiz` în pagină
3. Gata — primești imediat un link public

### Varianta 3: Direct pe springfarma.com

1. Urcă fișierele într-un subfolder pe server (ex: `/quiz/`)
2. Accesează la: `https://springfarma.com/quiz/`
3. Poți pune și un link direct din meniu pe site

## 🔗 Cum integrezi pe site-ul existent

Poți adăuga quiz-ul pe orice pagină prin iframe:

```html
<iframe
  src="https://username.github.io/zzzumzi-quiz/"
  width="100%"
  height="900"
  frameborder="0"
  style="border-radius: 20px;">
</iframe>
```

Sau simplu, un buton care trimite către quiz:

```html
<a href="https://username.github.io/zzzumzi-quiz/"
   target="_blank"
   style="background: #FFD93D; padding: 16px 32px; border-radius: 100px; font-weight: bold; text-decoration: none; color: #2D1F33;">
  🐝 Joacă Zzzumzi Quiz
</a>
```

## ✏️ Cum modifici quiz-ul

Toate întrebările și răspunsurile sunt în fișierul `index.html`, în secțiunea `<script>`. Caută array-ul `questions` și modifică:

- **question** — întrebarea
- **options** — cele 4 răspunsuri (fiecare cu `text`, `points` între 0-3, și `comment` de la Zzzumzi)
- **category** — categoria afișată sus

Diagnosticele finale sunt în array-ul `diagnoses` (5 niveluri de la 0-9 puncte la 25-30 puncte).

## 🎨 Culori folosite (brand Spring Farma)

- Mov principal: `#7B2D8E`
- Verde: `#5FB947`
- Galben: `#FFD93D`
- Crem fundal: `#FFF9EC`

## 📊 Share pe social media (opțional)

Dacă vrei să adaugi butoane de share la final, spune-mi și adaug:
- Facebook Share
- WhatsApp
- Provoacă prietenii

---

Cu drag, **Zzzumzi** 🐝 de la **Spring Farma**
