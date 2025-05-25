# JS-online-community
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Local Community Event Portal</title>
</head>
<body>
  <h1>Community Events</h1>
  <div>
    <label for="categoryFilter">Filter by Category:</label>
    <select id="categoryFilter">
      <option value="">All</option>
      <option value="music">Music</option>
      <option value="workshop">Workshop</option>
    </select>
  </div>

  <input type="text" id="searchBox" placeholder="Search by name..." />

  <div id="eventsContainer"></div>

  <form id="registrationForm">
    <h2>Register for Event</h2>
    <input type="text" name="name" placeholder="Your Name" required />
    <input type="email" name="email" placeholder="Your Email" required />
    <select name="event" required></select>
    <button type="submit" id="registerBtn">Register</button>
  </form>

  <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
  <script src="main.js"></script>
</body>
</html>

/*js code*/
// 1. Setup and Welcome
console.log("Welcome to the Community Portal");
window.addEventListener("load", () => alert("Page fully loaded!"));

// 2. Event Data Example
const events = [
  { name: "Music Fest", date: "2025-06-15", category: "music", seats: 30 },
  { name: "Workshop on Baking", date: "2025-06-10", category: "workshop", seats: 0 },
  { name: "Local Band Night", date: "2025-07-05", category: "music", seats: 25 },
];

let registrations = 0;

// 3. Display Valid Events
function isUpcoming(event) {
  const today = new Date();
  return new Date(event.date) > today && event.seats > 0;
}

function displayEvents(filteredEvents) {
  const container = document.querySelector("#eventsContainer");
  container.innerHTML = "";
  filteredEvents.forEach(event => {
    const card = document.createElement("div");
    card.innerHTML = `<h3>${event.name}</h3><p>${event.date}</p><p>Seats: ${event.seats}</p><button onclick="registerUser('${event.name}')">Register</button>`;
    container.appendChild(card);
  });
}

// 4. Functions & Closures
function createCategoryCounter() {
  const counts = {};
  return function (category) {
    counts[category] = (counts[category] || 0) + 1;
    return counts[category];
  };
}
const countByCategory = createCategoryCounter();

function filterEventsByCategory(events, callback) {
  return events.filter(callback);
}

function registerUser(eventName) {
  try {
    const event = events.find(e => e.name === eventName);
    if (event && event.seats > 0) {
      event.seats--;
      registrations++;
      countByCategory(event.category);
      alert(`Registered for ${event.name}`);
      displayEvents(events.filter(isUpcoming));
    } else {
      throw new Error("Event full or not found");
    }
  } catch (err) {
    console.error("Registration error:", err.message);
  }
}

// 5. Objects & Prototypes
function Event(name, date, category, seats) {
  this.name = name;
  this.date = date;
  this.category = category;
  this.seats = seats;
}

Event.prototype.checkAvailability = function () {
  return this.seats > 0 && new Date(this.date) > new Date();
};

console.log(Object.entries(events[0]));

// 6. Array Methods
const musicEvents = events.filter(e => e.category === "music");
const eventTitles = events.map(e => `${e.category.toUpperCase()}: ${e.name}`);
console.log(eventTitles);

// 7. DOM Manipulation
document.querySelector("#categoryFilter").onchange = function () {
  const val = this.value;
  const filtered = val ? events.filter(e => e.category === val && isUpcoming(e)) : events.filter(isUpcoming);
  displayEvents(filtered);
};

// 8. Event Handling
document.querySelector("#searchBox").addEventListener("keydown", function (e) {
  const query = e.target.value.toLowerCase();
  const filtered = events.filter(e => e.name.toLowerCase().includes(query));
  displayEvents(filtered.filter(isUpcoming));
});

// 9. Async / Await Mock Fetch
async function fetchEvents() {
  document.querySelector("#eventsContainer").innerHTML = "Loading...";
  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=3");
    const data = await res.json();
    console.log("Fetched mock events", data);
  } catch (err) {
    console.error("Fetch error:", err);
  }
}
fetchEvents();

// 10. ES6+ Features
function formatEvent({ name, date, seats }) {
  return `${name} - ${date} (${seats} seats left)`;
}

const clonedEvents = [...events]; // spread operator

// 11. Forms
document.querySelector("#registrationForm").addEventListener("submit", function (e) {
  e.preventDefault();
  const { name, email, event } = e.target.elements;
  if (!name.value || !email.value) {
    alert("All fields required");
    return;
  }
  console.log("Form Submitted:", name.value, email.value, event.value);
});

// 12. AJAX POST
async function sendRegistration(data) {
  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts", {
      method: "POST",
      body: JSON.stringify(data),
      headers: { "Content-Type": "application/json" }
    });
    const result = await res.json();
    console.log("Registration success", result);
    setTimeout(() => alert("Registration Complete!"), 1000);
  } catch (err) {
    alert("Error during registration");
  }
}

// 13. Debugging Tips
// Use console.log, DevTools breakpoints and network tab to inspect submission

// 14. jQuery Usage
$("#registerBtn").click(() => {
  console.log("jQuery Click!");
  $("#eventsContainer div").fadeOut(500).fadeIn(500);
});

// 15. Render on Load
window.onload = () => {
  displayEvents(events.filter(isUpcoming));

  // Fill form select options
  const select = document.querySelector('form select[name="event"]');
  events.forEach(ev => {
    const option = document.createElement("option");
    option.value = ev.name;
    option.text = ev.name;
    select.appendChild(option);
  });
};
