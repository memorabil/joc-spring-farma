================================================================
CONFIGURARE GOOGLE SHEETS PENTRU COLECTAREA RĂSPUNSURILOR
================================================================

Quiz-ul trimite automat fiecare completare către un Google Sheet.
Trebuie să faci o configurare unică (~5 minute), după care merge singur.

────────────────────────────────────────────────────────────────
PAS 1 — Creează Google Sheet-ul
────────────────────────────────────────────────────────────────

1. Mergi pe https://sheets.google.com și creează un sheet nou.
2. Numește-l, de ex: "Zzzumzi Quiz - Răspunsuri"
3. NU adăuga manual capete de coloană — scriptul le creează singur
   prima dată când primește un răspuns.

────────────────────────────────────────────────────────────────
PAS 2 — Atașează scriptul Apps Script
────────────────────────────────────────────────────────────────

1. În sheet, click pe meniul: Extensions → Apps Script
2. Se deschide un editor cu un fișier "Code.gs". Șterge tot conținutul
   și lipește în loc codul de mai jos:

────── ÎNCEPUT COD ──────

function doPost(e) {
  try {
    var data = JSON.parse(e.postData.contents);
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();

    // Adaugă antetul prima dată
    if (sheet.getLastRow() === 0) {
      var header = ['Data', 'Email', 'Gen', 'Vârstă', 'Greutate (kg)',
                    'Înălțime (cm)', 'IMC', 'Scor', 'Scor maxim', 'Diagnoză'];
      // Adaugă perechi de coloane pentru fiecare întrebare (presupunem max 10)
      for (var i = 1; i <= 10; i++) {
        header.push('Î' + i + ' - Întrebare');
        header.push('Î' + i + ' - Răspuns');
        header.push('Î' + i + ' - Puncte');
      }
      sheet.appendRow(header);
      sheet.getRange(1, 1, 1, header.length).setFontWeight('bold');
      sheet.setFrozenRows(1);
    }

    // Construiește rândul cu datele
    var row = [
      new Date(data.timestamp || new Date()),
      data.email || '',
      data.gender === 'male' ? 'Bărbat' : (data.gender === 'female' ? 'Femeie' : ''),
      data.ageRange || '',
      data.weight || '',
      data.height || '',
      data.bmi || '',
      data.score || 0,
      data.maxScore || '',
      data.diagnosisTitle || ''
    ];

    var answers = data.answers || [];
    for (var j = 0; j < 10; j++) {
      if (answers[j]) {
        row.push(answers[j].question || '');
        row.push(answers[j].answer || '');
        row.push(answers[j].points != null ? answers[j].points : '');
      } else {
        row.push(''); row.push(''); row.push('');
      }
    }

    sheet.appendRow(row);

    return ContentService
      .createTextOutput(JSON.stringify({ result: 'success' }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ result: 'error', message: err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

────── SFÂRȘIT COD ──────

3. Click pe iconița de salvare (sau Ctrl+S). Numește proiectul, de ex.
   "Zzzumzi Quiz Receiver".

────────────────────────────────────────────────────────────────
PAS 3 — Publică scriptul ca Web App
────────────────────────────────────────────────────────────────

1. În Apps Script, sus dreapta, click pe butonul albastru "Deploy"
   → "New deployment".
2. Click pe iconița de roată (Select type) și alege "Web app".
3. Completează:
   - Description: Zzzumzi Quiz endpoint
   - Execute as: Me (adresa ta de email)
   - Who has access: ANYONE  ← important! (altfel quiz-ul nu poate trimite)
4. Click "Deploy".
5. Prima dată îți va cere autorizare:
   - Apare "Authorize access" → Click pe el
   - Alege contul tău Google
   - Apare avertisment "Google hasn't verified this app" — e normal,
     scriptul e creat de tine. Click "Advanced" → "Go to [nume] (unsafe)"
   - Acceptă permisiunile (acces la sheet-ul tău).
6. La final primești un "Web app URL" — copiază-l. Arată cam așa:
   https://script.google.com/macros/s/AKfyc.................../exec

────────────────────────────────────────────────────────────────
PAS 4 — Pune URL-ul în quiz
────────────────────────────────────────────────────────────────

1. Deschide fișierul index.html într-un editor de text.
2. Caută linia (Ctrl+F):
      const GOOGLE_SHEETS_ENDPOINT = 'YOUR_GOOGLE_SHEETS_ENDPOINT_HERE';
3. Înlocuiește 'YOUR_GOOGLE_SHEETS_ENDPOINT_HERE' cu URL-ul copiat la pasul 3.6.
   Exemplu:
      const GOOGLE_SHEETS_ENDPOINT = 'https://script.google.com/macros/s/AKfyc.../exec';
4. Salvează fișierul, fă deploy pe Netlify și gata.

────────────────────────────────────────────────────────────────
TESTARE
────────────────────────────────────────────────────────────────

Fă tu primul quiz și verifică dacă apare un rând în Google Sheet.
Dacă nu apare:
- Verifică în consola browserului (F12 → Console) dacă apare vreo eroare.
- Verifică că ai pus "Anyone" la "Who has access" în pasul 3.3.
- Verifică că URL-ul din index.html se termină cu /exec (nu /dev).

────────────────────────────────────────────────────────────────
DACĂ ACTUALIZEZI SCRIPTUL MAI TÂRZIU
────────────────────────────────────────────────────────────────

Dacă schimbi codul Apps Script, trebuie să faci re-deploy:
- Deploy → Manage deployments → click iconița de creion (Edit)
- La "Version" alege "New version" → Deploy
- URL-ul rămâne același, deci nu trebuie să-l mai schimbi în index.html.

────────────────────────────────────────────────────────────────
NOTĂ GDPR
────────────────────────────────────────────────────────────────

Datele colectate sunt date cu caracter personal (email, profil de
sănătate). Asigură-te că:
- Ai o politică de confidențialitate publicată pe site (link-ul
  din quiz duce momentan la "#" — înlocuiește-l cu URL-ul real).
- Ai un temei legal de prelucrare (consent-ul bifat de utilizator
  acoperă acest lucru).
- Permiti utilizatorilor să-și ceară ștergerea datelor (la cerere
  prin email — ștergi rândul corespunzător din Sheet).
