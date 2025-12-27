# Newyear_html
newyear2026
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>New Year 2026 – One Number One Wish</title>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore-compat.js"></script>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: radial-gradient(circle, #000428, #004e92);
  color: white;
  text-align: center;
}

h1 {
  color: gold;
  margin-top: 20px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(10, 1fr);
  gap: 8px;
  max-width: 500px;
  margin: 20px auto;
}

.number {
  padding: 12px;
  background: rgba(255,255,255,0.2);
  border-radius: 6px;
  cursor: pointer;
  transition: 0.3s;
}

.number:hover {
  background: gold;
  color: black;
}

.number.selected {
  background: crimson;
  cursor: not-allowed;
}

form {
  margin: 30px auto;
  max-width: 400px;
}

input, textarea {
  width: 90%;
  padding: 10px;
  margin: 8px 0;
  border-radius: 5px;
  border: none;
}

button {
  background: gold;
  color: black;
  padding: 10px 25px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
}

button:hover {
  background: orange;
}

.admin {
  display: none;
  background: #111;
  margin-top: 40px;
  padding: 20px;
}

table {
  width: 90%;
  margin: auto;
  border-collapse: collapse;
}

td, th {
  border: 1px solid white;
  padding: 8px;
}
</style>
</head>

<body>

<h1>🎆 Welcome 2026 🎆</h1>
<p>Choose ONE number and make a wish ✨</p>

<div class="grid" id="numbers"></div>

<h2>Write Your Wish</h2>
<form id="wishForm">
  <input type="text" id="name" placeholder="Your Name" required>
  <input type="text" id="chosenNumber" placeholder="Chosen Number" readonly required>
  <textarea id="wish" placeholder="Your Wish / Resolution for 2026" required></textarea>
  <br>
  <button type="submit">Submit</button>
</form>

<!-- ADMIN PANEL (Hidden) -->
<div class="admin" id="adminPanel">
  <h2>Admin Panel</h2>
  <table>
    <tr>
      <th>Name</th>
      <th>Number</th>
      <th>Wish</th>
    </tr>
    <tbody id="adminData"></tbody>
  </table>
</div>

<script>
/* 🔥 FIREBASE CONFIG (REPLACE WITH YOURS) */
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

/* CREATE NUMBERS 1–100 */
const numbersDiv = document.getElementById("numbers");
const chosenNumber = document.getElementById("chosenNumber");

for (let i = 1; i <= 100; i++) {
  const div = document.createElement("div");
  div.className = "number";
  div.innerText = i;

  div.onclick = async () => {
    if (chosenNumber.value) {
      alert("You already selected a number!");
      return;
    }

    const ref = db.collection("numbers").doc(i.toString());
    const snap = await ref.get();

    if (snap.exists && snap.data().taken) {
      alert("Number already taken!");
      return;
    }

    await ref.set({ taken: true });
    div.classList.add("selected");
    chosenNumber.value = i;
  };

  numbersDiv.appendChild(div);
}

/* SUBMIT WISH */
document.getElementById("wishForm").addEventListener("submit", async (e) => {
  e.preventDefault();

  await db.collection("wishes").add({
    name: name.value,
    number: chosenNumber.value,
    wish: wish.value,
    time: new Date()
  });

  alert("✨ Your wish is saved for 2026 ✨");
  e.target.reset();
});

/* 🔐 ADMIN MODE (SECRET) */
/* Add ?admin=2026 to URL */
if (window.location.search === "?admin=2026") {
  document.getElementById("adminPanel").style.display = "block";

  db.collection("wishes").onSnapshot(snapshot => {
    adminData.innerHTML = "";
    snapshot.forEach(doc => {
      const d = doc.data();
      adminData.innerHTML += `
        <tr>
          <td>${d.name}</td>
          <td>${d.number}</td>
          <td>${d.wish}</td>
        </tr>`;
    });
  });
}
</script>

</body>
</html>