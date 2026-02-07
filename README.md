<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <title>AI Study Planner</title>
  <script src="https://cdn.tailwindcss.com"></script>

  <style>
    .bg-hero {
      background-image:
        linear-gradient(rgba(79, 70, 229, 0.7), rgba(168, 85, 247, 0.7)),
        url("https://images.unsplash.com/photo-1523240795612-9a054b0db644?q=80&w=1600&auto=format&fit=crop");
      background-size: cover;
      background-position: center;
    }

    .bg-planner {
      background-image:
        linear-gradient(rgba(14, 165, 233, 0.75), rgba(99, 102, 241, 0.75)),
        url("https://images.unsplash.com/photo-1503676260728-1c00da094a0b?q=80&w=1600&auto=format&fit=crop");
      background-size: cover;
      background-position: center;
    }

    .tab-btn {
      padding: 10px 18px;
      border-radius: 999px;
      background: #e0f2fe;
      font-weight: 600;
      transition: 0.3s;
    }

    .tab-btn:hover {
      background: #bae6fd;
      transform: scale(1.05);
    }

    .badge-high {
      background: #ef4444;
      color: white;
      padding: 4px 10px;
      border-radius: 999px;
    }

    .badge-medium {
      background: #facc15;
      color: #333;
      padding: 4px 10px;
      border-radius: 999px;
    }

    .badge-low {
      background: #22c55e;
      color: white;
      padding: 4px 10px;
      border-radius: 999px;
    }

    .row-high {
      background: #fee2e2;
    }

    .row-medium {
      background: #fef9c3;
    }

    .row-low {
      background: #dcfce7;
    }
  </style>
</head>

<body class="min-h-screen">

  <!-- SCREEN 1 -->
  <div id="screen1" class="min-h-screen flex items-center justify-center p-4 bg-hero">
    <div class="bg-white/90 backdrop-blur-md p-8 rounded-2xl shadow-2xl w-full max-w-3xl">
      <h2 class="text-3xl font-bold text-center text-indigo-700 mb-4">
        🎓 AI Study Planner for Engineering Students
      </h2>

      <form id="plannerForm" class="space-y-5">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <input type="text" placeholder="Name (Optional)" class="p-3 border rounded-lg" />
          <input type="email" placeholder="Email (Optional)" class="p-3 border rounded-lg" />
          <input type="text" placeholder="College (Optional)" class="p-3 border rounded-lg" />
          <input type="text" placeholder="Branch / Department" class="p-3 border rounded-lg" />
          <input type="number" id="weekdayHours" placeholder="Weekday Study Hours" class="p-3 border rounded-lg"
            required />
          <input type="number" id="weekendHours" placeholder="Weekend Study Hours" class="p-3 border rounded-lg"
            required />
        </div>

        <div>
          <h3 class="font-bold text-lg mb-2">📚 Subjects</h3>
          <div id="subjectsContainer" class="space-y-3"></div>
          <button type="button" onclick="addSubject()" class="mt-3 text-indigo-600 font-semibold">
            ➕ Add Another Subject
          </button>
        </div>

        <button type="submit"
          class="w-full bg-indigo-600 text-white py-3 rounded-xl text-lg font-semibold hover:bg-indigo-700 transition">
          🚀 Generate My AI Study Plan
        </button>
      </form>

      <p id="appreciation" class="hidden text-center text-green-600 font-semibold mt-4 text-lg">
        🌟 Awesome! Generating your personalized study plan...
      </p>
    </div>
  </div>

  <!-- SCREEN 2 -->
  <div id="screen2" class="hidden min-h-screen p-6 bg-planner">
    <div class="flex justify-between items-center mb-6">
      <h1 class="text-3xl md:text-4xl font-bold text-white">📘 Your AI Study Planner</h1>
      <button onclick="goBack()" class="bg-white/80 px-4 py-2 rounded-lg font-semibold">⬅ Back & Edit</button>
    </div>

    <div class="bg-white/90 backdrop-blur-md rounded-2xl p-6 shadow-2xl max-w-5xl mx-auto">
      <div class="flex flex-wrap gap-4 mb-6 justify-center">
        <button onclick="showTab('weekly')" class="tab-btn">📅 Weekly Plan</button>
        <button onclick="showTab('subjectsTab')" class="tab-btn">📚 Subject Allocation</button>
        <button onclick="showTab('progress')" class="tab-btn">📈 Progress</button>
      </div>

      <div id="weekly" class="tab-content">
        <table class="w-full border rounded-lg overflow-hidden">
          <thead class="bg-blue-100">
            <tr>
              <th class="p-2">Done</th>
              <th class="p-2">Day</th>
              <th class="p-2">Subject</th>
              <th class="p-2">Priority</th>
              <th class="p-2">Duration</th>
            </tr>
          </thead>
          <tbody id="weeklyTable"></tbody>
        </table>
      </div>

      <div id="subjectsTab" class="tab-content hidden">
        <ul id="subjectSummary" class="space-y-3 text-lg"></ul>
      </div>

      <div id="progress" class="tab-content hidden">
        <p class="text-lg mb-2">📊 Completion: <b><span id="progressPercent">0</span>%</b></p>
        <div class="w-full bg-gray-200 rounded-full h-4">
          <div id="progressBar" class="bg-green-500 h-4 rounded-full w-[0%]"></div>
        </div>
      </div>
    </div>
  </div>

  <script>
    function addSubject() {
      const div = document.createElement("div");
      div.className = "grid grid-cols-1 md:grid-cols-4 gap-2";
      div.innerHTML = `
      <input type="text" placeholder="Subject Name" class="p-2 border rounded" required />
      <input type="number" placeholder="Credits" class="p-2 border rounded" required />
      <select class="p-2 border rounded">
        <option value="1">Confidence 1 (Low)</option>
        <option value="2">Confidence 2</option>
        <option value="3" selected>Confidence 3</option>
        <option value="4">Confidence 4</option>
        <option value="5">Confidence 5 (High)</option>
      </select>
      <button type="button" onclick="removeSubject(this)" class="bg-red-500 text-white rounded px-2">✖</button>
    `;
      document.getElementById("subjectsContainer").appendChild(div);
    }

    function removeSubject(btn) {
      btn.parentElement.remove();
    }

    function getPriority(confidence, credits) {
      if (confidence <= 2 || credits >= 4) return { label: "High", badge: "badge-high", row: "row-high", hours: "2 hrs" };
      if (confidence == 3) return { label: "Medium", badge: "badge-medium", row: "row-medium", hours: "1.5 hrs" };
      return { label: "Low", badge: "badge-low", row: "row-low", hours: "1 hr" };
    }

    document.getElementById("plannerForm").addEventListener("submit", function (e) {
      e.preventDefault();
      document.getElementById("appreciation").classList.remove("hidden");

      const rows = document.querySelectorAll("#subjectsContainer > div");
      const weeklyTable = document.getElementById("weeklyTable");
      const summary = document.getElementById("subjectSummary");

      weeklyTable.innerHTML = "";
      summary.innerHTML = "";

      const subjects = [];

      rows.forEach(row => {
        subjects.push({
          name: row.children[0].value,
          credits: parseInt(row.children[1].value),
          confidence: parseInt(row.children[2].value)
        });
      });

      const days = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];

      days.forEach((day, i) => {
        const subj = subjects[i % subjects.length];
        const p = getPriority(subj.confidence, subj.credits);

        weeklyTable.innerHTML += `
        <tr class="${p.row}">
          <td class="p-2 text-center"><input type="checkbox" onchange="updateProgress()" /></td>
          <td class="p-2">${day}</td>
          <td class="p-2 font-semibold">${subj.name}</td>
          <td class="p-2"><span class="${p.badge}">${p.label} Priority</span></td>
          <td class="p-2">${p.hours}</td>
        </tr>
      `;
      });

      subjects.forEach(subj => {
        const p = getPriority(subj.confidence, subj.credits);
        summary.innerHTML += `
        <li class="flex items-center gap-2">
          📘 <b>${subj.name}</b>
          <span class="${p.badge}">${p.label} Priority</span>
        </li>
      `;
      });

      setTimeout(() => {
        document.getElementById("screen1").classList.add("hidden");
        document.getElementById("screen2").classList.remove("hidden");
      }, 800);
    });

    function updateProgress() {
      const boxes = document.querySelectorAll("#weeklyTable input[type='checkbox']");
      const checked = [...boxes].filter(b => b.checked).length;
      const percent = boxes.length ? Math.round((checked / boxes.length) * 100) : 0;
      document.getElementById("progressPercent").innerText = percent;
      document.getElementById("progressBar").style.width = percent + "%";
    }

    function showTab(id) {
      document.querySelectorAll(".tab-content").forEach(el => el.classList.add("hidden"));
      document.getElementById(id).classList.remove("hidden");
    }

    function goBack() {
      document.getElementById("screen2").classList.add("hidden");
      document.getElementById("screen1").classList.remove("hidden");
      document.getElementById("appreciation").classList.add("hidden");
    }

    addSubject();
  </script>
</body>

</html>
