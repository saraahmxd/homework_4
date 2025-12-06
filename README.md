<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Enhanced Form Page</title>
<style>
/* ==== USER CSS (Original With Additions) ==== */
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f9;
  margin: 40px;
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
  position: fixed; /* content protection */
  top: 0;
  left: 0;
  width: 100%;
  background: #f4f4f9;
  padding: 20px;
  z-index: 1000;
}

footer {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  background: #f4f4f9;
  padding: 15px;
  text-align: center;
  font-weight: bold;
  z-index: 1000;
  border-top: 1px solid #ccc;
}

.page-content {
  margin-top: 140px; /* allow room for sticky header */
  margin-bottom: 80px; /* allow room for sticky footer */
}

.block {
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 0 8px rgba(0,0,0,0.1);
  padding: 20px 30px;
  margin-bottom: 25px;
}

.block form {
  display: grid;
  grid-template-columns: 180px 300px 220px;
  align-items: center;
  row-gap: 12px;
  column-gap: 10px;
}

.block label {
  font-weight: bold;
  text-align: right;
}

.block input,
.block select,
.block textarea {
  width: 100%;
  padding: 6px;
  border-radius: 5px;
  border: 1px solid #ccc;
  box-sizing: border-box;
}

.warning {
  color: red;
  font-size: 0.9em;
}

fieldset {
  border: 1px solid #ccc;
  border-radius: 8px;
  margin-top: 15px;
  padding: 10px 15px;
}

fieldset legend {
  font-weight: bold;
}

#salary {
  width: 100%;
}

#salaryValue {
  font-weight: bold;
  margin-left: 10px;
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1em;
  margin-top: 20px;
}

button:hover {
  background-color: #0056b3;
}

@media (max-width: 700px) {
  .block form {
    grid-template-columns: 1fr;
  }
  .block label {
    text-align: left;
  }
}
</style>
</head>
<body>
<h1 id="headerMessage">Loading...</h1>

<div class="page-content">
  <!-- iFrame Example -->
  <iframe src="https://www.example.com" width="100%" height="300" style="border:1px solid #ccc; border-radius:10px;"></iframe>

  <div class="block">
    <form id="mainForm">
      <label for="fname">First Name:</label>
      <input type="text" id="fname" name="fname" />
      <span class="warning" id="fnameWarn"></span>

      <label>Remember Me:</label>
      <input type="checkbox" id="remember" checked />
      <span></span>

      <label for="state">State:</label>
      <select id="state"></select>
      <span></span>

      <label for="salary">Salary Range:</label>
      <input type="range" id="salary" min="30000" max="200000" />
      <span id="salaryValue">30000</span>

      <button type="button" onclick="submitForm()">Submit</button>
    </form>
  </div>
</div>

<footer>Footer content is fixed (content protection example).</footer>

<script>
/* ================= COOKIE HANDLING ================= */
function setCookie(name, value, days) {
  const d = new Date();
  d.setTime(d.getTime() + days * 24 * 60 * 60 * 1000);
  document.cookie = `${name}=${value};expires=${d.toUTCString()};path=/`;
}

function getCookie(name) {
  const decoded = decodeURIComponent(document.cookie).split(';');
  for (let c of decoded) {
    c = c.trim();
    if (c.startsWith(name + '=')) return c.substring(name.length + 1);
  }
  return '';
}

function eraseCookie(name) {
  document.cookie = `${name}=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;`;
}

/* ================= LOAD HEADER BASED ON COOKIE ================= */
window.onload = function () {
  const savedName = getCookie('firstName');

  if (savedName) {
    document.getElementById('headerMessage').innerHTML = `Welcome back, ${savedName}! <br><input type='checkbox' id='newUserBox' onclick='resetUser()' /> Not ${savedName}? Click to start over.`;
    document.getElementById('fname').value = savedName;
    loadLocalStorage();
  } else {
    document.getElementById('headerMessage').textContent = 'Welcome new user!';
  }

  loadStates();
};

/* ================= RESET USER ================= */
function resetUser() {
  eraseCookie('firstName');
  localStorage.clear();
  location.reload();
}

/* ================= FETCH API (state list) ================= */
async function loadStates() {
  try {
    const res = await fetch('states.txt');
    const text = await res.text();
    const stateList = text.split("\n");
    const select = document.getElementById('state');

    stateList.forEach(st => {
      const opt = document.createElement('option');
      opt.value = opt.textContent = st;
      select.appendChild(opt);
    });
  } catch (err) {
    console.error('Error loading states:', err);
  }
}

/* ================= LOCAL STORAGE SAVE ================= */
const fields = ['fname', 'state', 'salary'];

fields.forEach(id => {
  document.addEventListener('change', e => {
    if (e.target.id === id) {
      localStorage.setItem(id, e.target.value);
    }
  });
});

function loadLocalStorage() {
  fields.forEach(id => {
    const val = localStorage.getItem(id);
    if (val !== null) document.getElementById(id).value = val;
  });
}

/* ================= FORM SUBMIT ================= */
function submitForm() {
  const name = document.getElementById('fname').value;
  const remember = document.getElementById('remember').checked;

  if (remember) {
    setCookie('firstName', name, 2);
  } else {
    eraseCookie('firstName');
    localStorage.clear();
  }

  alert('Form submitted!');
}
</script>

</body>
</html>
