# quoteofthedayapp
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Quote of the Day</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <div class="container">
    <h1> Quote of the Day</h1>
    <div id="quote-box">
      <p id="quote-text">Loading quote...</p>
      <p id="quote-author"></p>
    </div>
    <div class="buttons">
      <button id="new-quote">🔁 New Quote</button>
      <button id="favorite">❤️ Favorite</button>
      <button id="share">📤 Share</button>
    </div>
    <div class="favorites-section">
      <h2>Your Favorite Quotes</h2>
      <ul id="favorites-list"></ul>
    </div>
  </div>
  <script src="script.js"></script>
</body>
</html>
style.css
body {
  margin: 0;
  padding: 0;
  font-family: "Segoe UI", sans-serif;
  background: linear-gradient(135deg, #e0c3fc, #8ec5fc);
  color: #333;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  min-height: 100vh;
}

.container {
  background: #fff;
  margin-top: 60px;
  padding: 30px;
  border-radius: 16px;
  width: 90%;
  max-width: 600px;
  box-shadow: 0 10px 20px rgba(0,0,0,0.15);
  text-align: center;
}

h1 {
  font-size: 2rem;
  margin-bottom: 20px;
  color: #6a11cb;
}

#quote-box {
  background: #f0f0ff;
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 20px;
  font-style: italic;
}

#quote-author {
  margin-top: 10px;
  text-align: right;
  font-weight: bold;
  color: #555;
}

.buttons {
  display: flex;
  gap: 10px;
  justify-content: center;
  flex-wrap: wrap;
}

button {
  padding: 10px 15px;
  border: none;
  background: #6a11cb;
  color: white;
  font-size: 1rem;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s ease;
}

button:hover {
  background: #5a01aa;
}

.favorites-section {
  margin-top: 30px;
  text-align: left;
}

.favorites-section h2 {
  color: #444;
}

#favorites-list {
  list-style: none;
  padding: 0;
  max-height: 150px;
  overflow-y: auto;
}

#favorites-list li {
  padding: 10px;
  background: #f9f9ff;
  margin-bottom: 10px;
  border-left: 4px solid #6a11cb;
  border-radius: 4px;
}
script.js
const quotes = [
  { text: "Believe you can and you're halfway there.", author: "Theodore Roosevelt" },
  { text: "Success is not final, failure is not fatal: it is the courage to continue that counts.", author: "Winston Churchill" },
  { text: "You are never too old to set another goal or to dream a new dream.", author: "C.S. Lewis" },
  { text: "Hardships often prepare ordinary people for an extraordinary destiny.", author: "C.S. Lewis" },
  { text: "The only way to do great work is to love what you do.", author: "Steve Jobs" },
  { text: "Act as if what you do makes a difference. It does.", author: "William James" },
  { text: "Start where you are. Use what you have. Do what you can.", author: "Arthur Ashe" }
];

const quoteText = document.getElementById("quote-text");
const quoteAuthor = document.getElementById("quote-author");
const newQuoteBtn = document.getElementById("new-quote");
const favoriteBtn = document.getElementById("favorite");
const shareBtn = document.getElementById("share");
const favoritesList = document.getElementById("favorites-list");

let currentQuote = null;

function getRandomQuote() {
  const index = Math.floor(Math.random() * quotes.length);
  return quotes[index];
}

function displayQuote(quote) {
  quoteText.textContent = `"${quote.text}"`;
  quoteAuthor.textContent = `- ${quote.author}`;
  currentQuote = quote;
}

function loadQuoteOfTheDay() {
  const today = new Date().toISOString().split("T")[0];
  let stored = JSON.parse(localStorage.getItem("quoteOfTheDay")) || {};

  if (stored.date === today) {
    displayQuote(stored.quote);
  } else {
    const newQ = getRandomQuote();
    localStorage.setItem("quoteOfTheDay", JSON.stringify({ date: today, quote: newQ }));
    displayQuote(newQ);
  }
}

function addToFavorites(quote) {
  let favs = JSON.parse(localStorage.getItem("favorites")) || [];
  if (!favs.find(q => q.text === quote.text && q.author === quote.author)) {
    favs.push(quote);
    localStorage.setItem("favorites", JSON.stringify(favs));
    renderFavorites();
  }
}

function renderFavorites() {
  let favs = JSON.parse(localStorage.getItem("favorites")) || [];
  favoritesList.innerHTML = "";
  favs.forEach(q => {
    const li = document.createElement("li");
    li.textContent = `"${q.text}" — ${q.author}`;
    favoritesList.appendChild(li);
  });
}

function shareQuote(quote) {
  const message = `"${quote.text}" — ${quote.author}`;
  if (navigator.share) {
    navigator.share({
      title: "Inspiring Quote",
      text: message
    }).catch(() => alert("Sharing cancelled."));
  } else {
    navigator.clipboard.writeText(message).then(() => {
      alert("Quote copied to clipboard!");
    });
  }
}

newQuoteBtn.addEventListener("click", () => {
  const q = getRandomQuote();
  displayQuote(q);
});

favoriteBtn.addEventListener("click", () => {
  if (currentQuote) addToFavorites(currentQuote);
});

shareBtn.addEventListener("click", () => {
  if (currentQuote) shareQuote(currentQuote);
});

window.onload = () => {
  loadQuoteOfTheDay();
  renderFavorites();
};
