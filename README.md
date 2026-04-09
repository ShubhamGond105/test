# Quiz Form App

A React form that saves answers locally when offline and auto-submits when internet reconnects.

## Run

```bash
npm start
```

Opens at **http://localhost:3000**

---

## Files

```
src/
├── App.js
├── App.css
├── index.js
└── components/
    ├── QuizForm.js
    └── QuizForm.css
```

---

## QuizForm.js

### Constants

```js
const STORAGE_KEY = 'quiz_form_data'; // sessionStorage key
const AUTO_SUBMIT_DELAY = 120;        // seconds before auto-submit
```

### Questions

```js
const questions = [
  {
    id: 1,
    question: "What is the capital city of India?",
    placeholder: "Type your answer here...",
  }
];
```

Add more objects to add more questions.

---

### State

| State | Purpose |
|---|---|
| `answers` | Stores `{ id: answerText }` for each question |
| `isOnline` | Current internet status |
| `countdown` | Counts down from 120 to 0 after reconnect |
| `submitted` | True after form is submitted |
| `submitting` | True while submit is in progress |
| `currentQuestion` | Active (highlighted) question index |
| `notification` | Toast message `{ msg, type }` |

---

### How It Works

**1. Save on every keystroke**
```js
useEffect(() => {
  sessionStorage.setItem(STORAGE_KEY, JSON.stringify(answers));
}, [answers]);
```

**2. Restore on page load**
```js
const saved = sessionStorage.getItem(STORAGE_KEY);
return saved ? JSON.parse(saved) : {};
```

**3. Detect offline/online**
```js
window.addEventListener('offline', handleOffline); // shows warning
window.addEventListener('online', handleOnline);   // starts countdown
```

**4. Auto-submit after 2 minutes**
```js
// Countdown ticks from 120 → 0
// When countdown hits 0:
if (countdown === 0 && isOnline && !submitted) {
  handleSubmit(true);
}
```

**5. Submit**
```js
// Simulated API — replace with real fetch:
await new Promise(resolve => setTimeout(resolve, 1500));
sessionStorage.removeItem(STORAGE_KEY); // clear after success
```

---

### Offline Flow

```
User types → saved to sessionStorage
Internet off → submit button disabled
Internet on  → 2-min countdown starts
Countdown 0  → form auto-submits
```

---

## Test Offline

1. Type an answer
2. DevTools → Network → **Offline**
3. Status banner turns yellow
4. Set back to **Online**
5. Watch countdown → auto-submits at `00:00`

> To test faster: change `AUTO_SUBMIT_DELAY` to `10`
