---
name: multiplayer-jeopardy-game
description: >
  Build a Hebrew multiplayer Jeopardy-style game with Fortune wheel, Jeopardy board,
  host screen and player screen, Firebase Realtime Database, and GitHub Pages hosting.
  Use when asked to build a training game, quiz game, or multiplayer learning activity
  with a host/player architecture. Triggers: "בנה משחק", "משחק מולטיפלייר", "jeopardy",
  "גלגל מזל", "host player firebase", "משחק הדרכה".
version: "1.0"
language: he
stack:
  - HTML + CSS + JS (ES Modules)
  - Firebase Realtime Database 10.12.0
  - GitHub Pages
  - QRCode.js
---

# SKILL: משחק מולטיפלייר — Jeopardy Style
**גרסה:** 1.0  
**שפה:** עברית (RTL)  
**Stack:** HTML + Firebase Realtime DB + GitHub Pages

---

## תיאור
Skill לבניית משחק מולטיפלייר אינטראקטיבי בסגנון Jeopardy, עם גלגל מזל, לוח שאלות, ומסך שחקן נפרד. נבנה ונבדק בפרויקטים: "הבית הבטוח" ו-"מקשיבים לבטיחות" — משחקי בטיחות ל-HB Learning.

---

## חשבונות קבועים — HB Consulting (לא לשאול שוב!)

```
GitHub:  hbconsulting00-sketch   ← חשבון העבודה של HB
         gh CLI מוגדר ומחובר במחשב (hbconsulting00@gmail.com)
         כל repo חדש נוצר תחת: https://github.com/hbconsulting00-sketch/

Firebase: hbconsulting00@gmail.com
          צור פרויקט חדש לכל משחק ב-firebase.google.com
          
ארגון:   HB Consulting
```

### זרימת GitHub לפרויקט חדש
```bash
# 1. צור repo ב-GitHub UI (Public + README) תחת hbconsulting00-sketch
# 2. אתחל git בתיקיית הפרויקט:
git init
git config user.name "hbconsulting00-sketch"
git config user.email "hbconsulting00@gmail.com"
git remote add origin https://github.com/hbconsulting00-sketch/REPO-NAME.git
git pull origin main --allow-unrelated-histories
git add index.html player.html
git commit -m "Add game files"
git push -u origin main
```

### הוספת תמונות (אחרי שהמשתמשת מכינה אותן)
```bash
# המשתמשת שומרת תמונות לתיקיית הפרויקט — ואז:
cd "PATH_TO_PROJECT"
git add *.png
git commit -m "Add question images"
git push
```

### GitHub Pages — הפעלה ידנית (פעם אחת)
Settings → Pages → Branch: main → Save
URL: https://hbconsulting00-sketch.github.io/REPO-NAME/

---

## שלב 0 — מה עדיין צריך לבקש

Claude Code **חייב לבקש** רק את הפרטים הבאים (GitHub וחשבון — כבר ידועים):

```
1. Firebase config חדש לפרויקט (העתק מ-Project Settings > Your apps > SDK setup):
   - apiKey
   - authDomain
   - databaseURL  ← חשוב במיוחד! (כולל region אם europe-west1)
   - projectId
   - storageBucket
   - messagingSenderId
   - appId

2. שם ה-repo ב-GitHub (נוצר ידנית ע"י המשתמשת לפני הבנייה)

3. שם המשחק בעברית

4. שם הארגון/חברה (ברירת מחדל: HB Consulting)
```

### בדיקת Firebase לפני המשך
```python
import urllib.request, json
db_url = "https://[PROJECT-ID]-default-rtdb.firebaseio.com/"
try:
    req = urllib.request.Request(db_url + "test.json")
    with urllib.request.urlopen(req, timeout=5) as r:
        print("✅ Firebase פתוח")
except:
    print("❌ Firebase חסום — בדוק Rules")
```

**אם Firebase חסום:** הנחה את המשתמש:
1. firebase.google.com > הפרויקט > Realtime Database > Rules
2. שנה ל: `{ "rules": { ".read": true, ".write": true } }`
3. לחץ Publish
4. **חשוב:** זו הגדרה קבועה — לא test mode שפג תוקף!

---

## שלב 1 — קלט תוכן

בקש מהמשתמש:
- מצגת (PDF / PPTX / Word) עם תוכן הקורס
- 4 קטגוריות לשאלות (ניתן לחלץ מהמצגת)

---

## שלב 2 — חילוץ וגיבוש שאלות

### מבנה נדרש
**16 שאלות בדיוק: 4 קטגוריות × 4 ערכים (100/200/300/400)**

לכל שאלה:
```js
{
  cat: 'שם קטגוריה',   // עם אמוג'י: '🔥 אש וחשמל'
  pts: 100,             // 100/200/300/400
  type: 'mc',          // 'mc' = רב ברירה | 'tf' = אמת/שקר
  text: 'טקסט השאלה',
  opts: ['א','ב','ג','ד'],  // tf: ['✅ אמת','❌ שקר']
  correct: 0,           // index של התשובה הנכונה
  img: 'https://raw.githubusercontent.com/USER/REPO/main/q01-name.png',
  expl: 'הסבר קצר לתשובה הנכונה'
}
```

### כללים לניסוח שאלות
- התשובה הנכונה **לא תופיע** בגוף השאלה
- הסבר: משפט אחד, ברור, מנוסח נכון בעברית
- tf: תמיד `opts: ['✅ אמת','❌ שקר']`
- mc: 4 אפשרויות, אחת נכונה, שלוש מסיחות סבירות
- שאלות 100: קלות (tf מומלץ)
- שאלות 400: מאתגרות

---

## שלב 3 — טבלת QA לאישור ← **עצור וחכה**

הצג למשתמש:

```
| # | קטגוריה | נק' | סוג | שאלה | אפשרויות (✅=נכון) | הסבר |
|---|---------|-----|-----|------|-------------------|------|
| 1 | ...     | 100 | tf  | ...  | ✅ שקר / אמת      | ...  |
...
```

**המתן לאישור מפורש לפני המשך.**  
המשתמש עשוי לתקן ניסוחים, לשנות תשובות, להחליף שאלות.

---

## שלב 4 — פרומפטים לתמונות ← **עצור וחכה**

לכל שאלה, הכן פרומפט:

```
שאלה 1 — [קטגוריה] [נק']
שם קובץ: q01-[תיאור-קצר].png
פרומפט:
Realistic photo of [תיאור מדויק של הסיטואציה].
No people. Natural lighting. Wide angle.
Image size: 1400x400 pixels (3.5:1 landscape) ← חשוב! לא 16:9
```

**⚠️ גודל תמונות חובה: 1400×400px (יחס 3.5:1)**  
אזור התצוגה במשחק הוא רחב ונמוך (max-height: 320px, width: ~900px).  
תמונות 16:9 (1400×800) ייחתכו חזק — תמיד לבקש 1400×400 או דומה.

**הנחיות למשתמש:**
1. צור תמונות ב-Gemini/Midjourney לפי הפרומפטים
2. שמור בשמות המוצעים (q01-..., q02-..., וכו')
3. העלה ל-GitHub repository (Add file > Upload files)
4. GitHub Pages יטען אוטומטית מ: `https://raw.githubusercontent.com/USER/REPO/main/[name].png`

**המתן לאישור "העלאתי את התמונות" לפני המשך.**

---

## שלב 5 — בניית הקבצים

### קבצים לייצר
```
index.html   ← מסך מנחה
player.html  ← מסך שחקן
```

---

## מפרט טכני מלא

### Firebase — ES Modules (חובה!)
```js
// ייבוא נכון — תמיד כך, לא compat
import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js';
import { getDatabase, ref, set, remove, update, onValue, onDisconnect, get }
  from 'https://www.gstatic.com/firebasejs/10.12.0/firebase-database.js';

const app = initializeApp({ /* config */ });
const db = getDatabase(app);
const SESSION = 'game_' + Math.random().toString(36).slice(2,7);
```

**אסור לחלוטין:**
- `db.ref()` — זה compat ישן, לא עובד עם ES Modules
- `firebase.database()` — אותה בעיה
- `initializeApp` פעמיים

### Firebase Paths
```
SESSION/players/PLAYER_ID     ← {name, emoji, score}
SESSION/answers/PLAYER_ID     ← {answer, elapsed}
SESSION/gameState             ← 'wheel' | 'question' | 'reveal' | 'end'
SESSION/currentQ              ← אובייקט השאלה המלא (כולל qId)
SESSION/correct               ← index התשובה הנכונה
```

### חשיפת פונקציות ל-onclick (קריטי!)
```js
// כל פונקציה שמופעלת מ-onclick ב-HTML חייבת:
window.functionName = functionName;

// בסוף הסקריפט, לפני INIT:
window.startGame = startGame;
window.resetGame = resetGame;
window.spinWheel = spinWheel;
window.revealAnswer = revealAnswer;
window.nextQuestion = nextQuestion;
window.showScores = showScores;
window.continueGame = continueGame;
window.pickQuestion = pickQuestion;
window.endEarly = endEarly;
window.backToBoard = backToBoard;

// ב-HTML: onclick="window.functionName()"
```

---

## מסך מנחה (index.html) — מפרט מלא

### מסכים
1. **lobby** — QR + רשימת שחקנים + כפתור "התחל משחק" (פעיל גם ללא שחקנים)
2. **wheel-screen** — גלגל מזל + לוח ג'פרדי
3. **q-screen** — שאלה פעילה
4. **scores-screen** — ניקוד ביניים
5. **end-screen** — מסך סיום + מנצח

### סטטוס בר (תמיד נראה בזמן משחק)
```
שחקנים: N | שאלה X/16 | 🥇 [מוביל + ניקוד]
[📋 חזור ללוח] [📊 ניקוד] [🏁 סיים פעילות] [🗑️]
```

- **"חזור ללוח"** — נראה רק בזמן שאלה פעילה, מאפשר לחזור בכל שלב
- **"סיים פעילות"** — confirm dialog + עובר לסיום עם ניקוד סופי
- **"🗑️"** — איפוס מלא עם confirm

### גלגל מזל
- גודל: 420px
- 4 קטגוריות בצבעים שונים
- אנימציה חלקה עם ease-out
- פונט ברור בתוך הגלגל (16px bold)
- אחרי עצירה: מציג קטגוריה ועובר לשאלה אחרי 1 שנייה

### לוח ג'פרדי
- 4×4 grid (4 קטגוריות × 4 ערכים)
- כרטיסים זמינים: **לחיצים** — לחיצה מריצה את השאלה ישירות (בלי גלגל)
- כרטיסים שעברו: מוצגים ✓ מעומעמים
- כרטיס נוכחי: מסומן עם border מהבהב
- ריחוף: הדגשה בצהוב

### מסך שאלה
```
[קטגוריה badge] [נק' badge]        [טיימר גדול]
[progress bar + "שאלה X מתוך 16"]
[טקסט שאלה גדול]
[תמונה (אם יש)]
[4 כפתורי תשובה]
[מונה תשובות: "N מתוך M ענו"]
[כפתור "✅ חשוף תשובה" — גדול, מהבהב כשעונים]
```

**אחרי חשיפה:**
```
[תשובות: נכון=ירוק, שגוי=אדום]
[גרף התפלגות תשובות]
[הסבר מסומן]
[כפתור גדול "← הבא | המשך למשחק"]
```

### ניקוד
```js
function scoreRound(q){
  const updates = {};
  Object.entries(playerAnswers).forEach(([id, data]) => {
    if(data.answer === q.correct){
      const elapsed = data.elapsed || 20000;
      const bonus = Math.floor(Math.max(0, (1 - elapsed/30000)) * q.pts * 0.5);
      const pts = q.pts + bonus;
      updates[`${SESSION}/players/${id}/score`] = (players[id]?.score || 0) + pts;
    }
  });
  if(Object.keys(updates).length) update(ref(db, '/'), updates);
}
```

---

## מסך שחקן (player.html) — מפרט מלא

### מסכים
1. **join-screen** — אמוג'י (10 כפתורים ב-HTML, לא JS) + שם + "הצטרפות למשחק"
2. **wait-screen** — המתנה + טיפים + ניקוד
3. **q-screen** — שאלה
4. **end-screen** — ניקוד סופי

### אמוג'ים — חובה ב-HTML
```html
<!-- לא ב-JS! -->
<div class="emoji-grid" id="emoji-grid">
  <button class="emoji-btn sel" data-em="😊">😊</button>
  <button class="emoji-btn" data-em="🎯">🎯</button>
  <!-- ... 10 אמוג'ים -->
</div>
```

### מסך המתנה
```
[אנימציית טעינה]
[אמוג'י + שם השחקן]
"⏳ ממתינים שהמנחה יתחיל...
על המסך תופיע שאלה ברגע שהמשחק מתחיל"
[טיפים: השאירו מסך דולק / ענו מהר / בונוס מהירות]
[ניקוד נוכחי]
```

### זרימת שאלה
1. `currentQ` מגיע מ-Firebase → `showQuestion(q)`
2. שחקן בוחר → "תשובתך נרשמה! המתינו שהמנחה יחשוף את התשובה הנכונה"
3. `correct` מגיע מ-Firebase → `doReveal(correct)`
4. הצג ✅/❌ + "התשובה הנכונה: X" + הסבר כ-**popup מלא מסך**
5. popup: "ממתין לשאלה הבאה..."

### Timeout
```js
if(timeLeft <= 0){
  clearInterval(timerInt);
  if(!answered) timeOut();
}
function timeOut(){
  answered = true;
  // הצג ⏰ "הזמן נגמר!"
}
```

### onDisconnect
```js
const pRef = ref(db, `${SESSION}/players/${myId}`);
set(pRef, {name, emoji, score: 0});
onDisconnect(pRef).remove(); // מחיקה אוטומטית בניתוק
```

### Dedup שאלות (קריטי!)
```js
let lastQId = null;
onValue(ref(db, `${SESSION}/currentQ`), snap => {
  const q = snap.val();
  if(!q) return;
  if(lastQId === q.qId) return; // מונע כפילות
  lastQId = q.qId;
  showQuestion(q);
});
```

---

## עיצוב — CSS Variables

```css
:root {
  --or: #FF6B00;      /* כתום — צבע ראשי */
  --orl: #FF9A3C;     /* כתום בהיר */
  --og: rgba(255,107,0,.4);
  --pm: #9333EA;      /* סגול */
  --pl: #C084FC;      /* סגול בהיר */
  --pg: rgba(147,51,234,.4);
  --dark: #0F0A1A;    /* רקע כהה */
  --pan: #1A0F2E;     /* כרטיסים */
  --pan2: #231540;    /* כרטיסים משניים */
  --gold: #FFD700;    /* ניקוד */
  --ok: #00CC6A;      /* נכון */
  --err: #FF4444;     /* שגוי */
}
```

**פונטים:**
```html
<link href="https://fonts.googleapis.com/css2?family=Heebo:wght@400;700;800;900&family=Secular+One&display=swap" rel="stylesheet">
```
- גוף: `Heebo`
- כותרות/מספרים: `Secular One`

---

## תיקונים ידועים — חובה לכלול מהתחלה

```css
/* חובה! בלי זה המסך לא ממלא את הרוחב (תוכן נדחס לצד) */
.screen { width: 100%; }
#game-screens { width: 100%; }
.screen.active { align-items: stretch; }  /* align-items:center שבור ב-RTL */
#lobby { align-items: center; }           /* חזרה ל-center ללובי בלבד */
#q-screen, #scores-screen, #end-screen { margin-left: auto; margin-right: auto; }

/* חובה! בלי זה תאי הלוח נמתחים לגובה לא אחיד */
#board { align-self: start; }
.board-cat, .board-cell { height: 64px/72px; display: flex; align-items: center; justify-content: center; }

/* תמונות — object-fit:contain כדי לא לחתוך */
#q-img { object-fit: contain; background: #111; max-height: 320px; }
```

---

## QA Checklist לפני שחרור

```
[ ] Firebase Rules = true/true (לא test mode)
[ ] אין db.ref() — רק ref(db, path)
[ ] כל onclick מופעל עם window.X()
[ ] window.X = X בסוף הסקריפט
[ ] אמוג'ים ב-HTML (לא ב-JS)
[ ] אין hotspot questions
[ ] 16 שאלות בדיוק
[ ] כל תמונה נטענת (GitHub raw URL)
[ ] player.html — אין hintEl reference
[ ] SESSION prefix שונה בין host לפרויקטים אחרים
[ ] Firebase Rules לא יפגו (true ולא timestamp)
[ ] GitHub Pages מופעל ב-Settings > Pages > main branch
```

---

## Firebase Rules — אזהרה חשובה

Firebase Realtime Database נוצר ב-"test mode" עם Rules שפוגים אחרי **30 יום**.

**פתרון:**
```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

זה מספיק למשחקי הדרכה — אין מידע רגיש, הנתונים זמניים.

**בדיקה:** פתח בדפדפן:
`https://[PROJECT-ID]-default-rtdb.firebaseio.com/test.json`
- רואה `null` → ✅ פתוח
- רואה `Permission denied` → ❌ חסום

---

## GitHub Pages — הגדרה

1. צור repository חדש (Public)
2. העלה `index.html`, `player.html`, וכל תמונות ה-PNG
3. Settings > Pages > Branch: main > Save
4. המשחק יהיה ב: `https://USERNAME.github.io/REPO-NAME/`
5. הסריקה מובילה ל: `https://USERNAME.github.io/REPO-NAME/player.html?s=SESSION_ID`

---

## פרויקטים לדוגמה

### הבית הבטוח
- **Repository:** `hbconsulting00-sketch/safehouse-game`
- **Firebase:** `hb-ergonomics-default-rtdb.firebaseio.com`
- **קטגוריות:** 🔥 אש וחשמל | 💧 נפילה והחלקה | ☠️ סכנות נסתרות | 🚨 שעת חירום
- **תמונות:** GitHub raw URLs + תמונות AI מ-Gemini

### מקשיבים לבטיחות (נזקי רעש)
- **Repository:** `hbconsulting00-sketch/noise-game`
- **Firebase:** `noise-game-75903-default-rtdb.europe-west1.firebasedatabase.app`
- **קטגוריות:** 🔊 הרעש והנזק | 📏 מספרים ומדידות | 🛡️ הגנה ומניעה | 👂 שימוש נכון
- **תמונות:** q01–q16 (PNG, 1400×800) — פרומפטים ב-`noise-game-prep.html`
- **תיקייה מקומית:** `g:\האחסון שלי\עבודה HB\תוצרי AI\multiplayer games\`
- **הערה:** Firebase region = europe-west1 (לא us-central1 ברירת מחדל)
