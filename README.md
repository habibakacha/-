<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>متابعة مناشير التفتيش</title>
  <script src="https://cdn.tailwindcss.com"></script> 
  <style>
    body.dark {
      background-color: #111827;
      color: #f9fafb;
    }
    body.dark .bg-light {
      background-color: #1f2937;
    }
    body.dark .hover\:bg-light:hover {
      background-color: #374151;
    }
  </style>
</head>
<body class="min-h-screen font-sans bg-gray-50 text-gray-900">

<!-- Login Screen -->
<div id="loginScreen" class="flex items-center justify-center min-h-screen bg-gray-100 dark:bg-gray-900">
  <div class="w-full max-w-md p-6 bg-white rounded-lg shadow-md dark:bg-gray-800">
    <h2 class="text-2xl font-bold mb-4 text-center">تسجيل الدخول</h2>
    <form id="loginForm" class="space-y-4">
      <div>
        <label class="block text-sm font-medium mb-1">اسم المستخدم</label>
        <input type="text" id="username" required class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">كلمة المرور</label>
        <input type="password" id="password" required class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <button type="submit" class="w-full px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-md transition-colors">
        دخول
      </button>
    </form>
  </div>
</div>

<!-- Main App -->
<div id="app" class="hidden container mx-auto px-4 py-6">

  <header class="bg-gradient-to-r from-blue-600 to-indigo-700 text-white shadow-lg p-6 rounded-lg mb-6">
    <div class="flex justify-between items-center">
      <h1 class="text-3xl font-bold">متابعة مناشير التفتيش</h1>
      <div class="flex items-center gap-4">
        <span id="welcomeMessage" class="text-sm"></span>
        <button id="toggleDarkMode" class="p-2 rounded-full hover:bg-white/20 transition-all">
          🌙
        </button>
      </div>
    </div>
  </header>

  <!-- Statistics -->
  <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6" id="stats">
    <div class="p-4 rounded-lg shadow-md bg-white dark:bg-gray-800">
      <h3 class="font-semibold text-sm opacity-70">إجمالي الإدخالات</h3>
      <p class="text-2xl font-bold" id="total">0</p>
    </div>
    <div class="p-4 rounded-lg shadow-md bg-white dark:bg-gray-800">
      <h3 class="font-semibold text-sm opacity-70">قيد المتابعة</h3>
      <p class="text-2xl font-bold text-green-500" id="inProgress">0</p>
    </div>
    <div class="p-4 rounded-lg shadow-md bg-white dark:bg-gray-800">
      <h3 class="font-semibold text-sm opacity-70">تم الحفظ</h3>
      <p class="text-2xl font-bold text-red-500" id="saved">0</p>
    </div>
  </div>

  <!-- Search & Add Button -->
  <div class="flex flex-col md:flex-row gap-4 mb-6">
    <input
      type="text"
      placeholder="بحث بالاسم أو عدد القضية..."
      id="searchInput"
      class="px-4 py-2 rounded-md border border-gray-300 focus:outline-none focus:ring-2 focus:ring-blue-400 w-full"
    />
    <button
      onclick="showAddModal()"
      id="addBtn"
      class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-md transition-colors hidden"
    >
      إضافة شخص جديد
    </button>
  </div>

  <!-- Table -->
  <div class="overflow-x-auto rounded-lg shadow-md">
    <table class="w-full table-auto border-collapse text-right">
      <thead class="bg-gray-100 dark:bg-gray-800">
        <tr>
          <th class="px-6 py-3 text-sm font-semibold">الاسم و اللقب</th>
          <th class="px-6 py-3 text-sm font-semibold">تاريخ الجلسة</th>
          <th class="px-6 py-3 text-sm font-semibold">عدد القضية</th>
          <th class="px-6 py-3 text-sm font-semibold">تاريخ المنشور</th>
          <th class="px-6 py-3 text-sm font-semibold">عدد المنشور</th>
          <th class="px-6 py-3 text-sm font-semibold">عدد التخزين</th>
          <th class="px-6 py-3 text-sm font-semibold">تاريخ رجوع الاعلام</th>
          <th class="px-6 py-3 text-sm font-semibold">تاريخ كف التفتيش</th>
          <th class="px-6 py-3 text-sm font-semibold">سبب كف التفتيش</th>
          <th class="px-6 py-3 text-sm font-semibold">مآل الملف</th>
          <th class="px-6 py-3 text-sm font-semibold">الإجراءات</th>
        </tr>
      </thead>
      <tbody id="dataTable" class="divide-y divide-gray-200 dark:divide-gray-700">
        <!-- Data will be inserted here -->
      </tbody>
    </table>
  </div>
</div>

<!-- Modal for Add/Edit -->
<div id="modal" class="fixed inset-0 bg-black/50 backdrop-blur-sm z-50 hidden flex items-center justify-center">
  <div class="bg-white dark:bg-gray-800 p-6 rounded-lg shadow-xl max-w-md w-full">
    <h2 id="modalTitle" class="text-xl font-bold mb-4">إضافة شخص جديد</h2>
    <form id="personForm" class="space-y-4">
      <input type="hidden" id="editIndex" value="" />

      <div>
        <label class="block text-sm font-medium mb-1">الاسم و اللقب</label>
        <input type="text" id="name" required class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">تاريخ الجلسة</label>
        <input type="text" id="sessionDate" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">عدد القضية</label>
        <input type="text" id="issueNumber" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">تاريخ المنشور</label>
        <input type="text" id="publishDate" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">عدد المنشور</label>
        <input type="text" id="publishNumber" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">عدد التخزين</label>
        <input type="text" id="storageNumber" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">تاريخ رجوع الاعلام</label>
        <input type="text" id="returnDate" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">تاريخ كف التفتيش</label>
        <input type="text" id="inspectionStopDate" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">سبب كف التفتيش</label>
        <input type="text" id="stopReason" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600" />
      </div>
      <div>
        <label class="block text-sm font-medium mb-1">مآل الملف</label>
        <select id="fileStatus" class="w-full px-3 py-2 border rounded-md dark:bg-gray-700 dark:border-gray-600">
          <option value="قيد المتابعة">قيد المتابعة</option>
          <option value="حفظ">حفظ</option>
        </select>
      </div>

      <div class="flex justify-end gap-2 mt-4">
        <button type="button" onclick="hideModal()" class="px-4 py-2 bg-gray-500 text-white rounded-md hover:bg-gray-600">
          إلغاء
        </button>
        <button type="submit" class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">
          حفظ
        </button>
      </div>
    </form>
  </div>
</div>

<script>
  // مستخدمو النظام
  const users = {
    admin: "admin123",
    user: "user123"
  };

  let currentUser = null;

  // Sample Data
  let data = [
    {
      id: 1,
      name: "عبد الرزاق خضر",
      sessionDate: "2/25/20",
      issueNumber: "1123/19",
      publishDate: "3/23/21",
      publishNumber: "265",
      storageNumber: "265",
      returnDate: "6/9/20",
      inspectionStopDate: "",
      stopReason: "",
      fileStatus: "قيد المتابعة"
    },
    {
      id: 2,
      name: "نصر الدين بنعمارة",
      sessionDate: "2/25/20",
      issueNumber: "1126/19",
      publishDate: "3/23/21",
      publishNumber: "268",
      storageNumber: "268",
      returnDate: "",
      inspectionStopDate: "",
      stopReason: "",
      fileStatus: "حفظ"
    }
  ];

  const loginForm = document.getElementById("loginForm");
  const app = document.getElementById("app");
  const loginScreen = document.getElementById("loginScreen");
  const welcomeMessage = document.getElementById("welcomeMessage");
  const addBtn = document.getElementById("addBtn");

  // Handle Login
  loginForm.addEventListener("submit", function (e) {
    e.preventDefault();
    const username = document.getElementById("username").value.trim();
    const password = document.getElementById("password").value.trim();

    if (users[username] && users[username] === password) {
      currentUser = username;
      loginScreen.classList.add("hidden");
      app.classList.remove("hidden");
      welcomeMessage.textContent = `مرحباً ${username}`;
      if (username === "admin") {
        addBtn.classList.remove("hidden");
      } else {
        addBtn.classList.add("hidden");
      }
      renderTable();
    } else {
      alert("اسم المستخدم أو كلمة المرور غير صحيحة");
    }
  });

  function renderTable() {
    const tbody = document.getElementById("dataTable");
    const searchTerm = document.getElementById("searchInput").value.toLowerCase();
    const filteredData = data.filter(p =>
      p.name.toLowerCase().includes(searchTerm) ||
      p.issueNumber.includes(searchTerm)
    );
    tbody.innerHTML = "";
    filteredData.forEach((person, index) => {
      const tr = document.createElement("tr");
      tr.className = "transition-colors hover:bg-gray-50 dark:hover:bg-gray-800";
      tr.innerHTML = `
        <td class="px-6 py-4">${person.name}</td>
        <td class="px-6 py-4">${person.sessionDate || "-"}</td>
        <td class="px-6 py-4">${person.issueNumber || "-"}</td>
        <td class="px-6 py-4">${person.publishDate || "-"}</td>
        <td class="px-6 py-4">${person.publishNumber || "-"}</td>
        <td class="px-6 py-4">${person.storageNumber || "-"}</td>
        <td class="px-6 py-4">${person.returnDate || "-"}</td>
        <td class="px-6 py-4">${person.inspectionStopDate || "-"}</td>
        <td class="px-6 py-4">${person.stopReason || "-"}</td>
        <td class="px-6 py-4">${person.fileStatus}</td>
        <td class="px-6 py-4 space-x-2 space-x-reverse">
          ${currentUser === 'admin' ? `<button onclick="editPerson(${index})" class="text-blue-500 hover:text-blue-700">تعديل</button>` : ""}
          ${currentUser === 'admin' ? `<button onclick="deletePerson(${index})" class="text-red-500 hover:text-red-700">حذف</button>` : ""}
        </td>
      `;
      tbody.appendChild(tr);
    });
    updateStats();
  }

  function updateStats() {
    const total = data.length;
    const inProgress = data.filter(d => d.fileStatus === "قيد المتابعة").length;
    const saved = data.filter(d => d.fileStatus === "حفظ").length;

    document.getElementById("total").textContent = total;
    document.getElementById("inProgress").textContent = inProgress;
    document.getElementById("saved").textContent = saved;
  }

  const modal = document.getElementById("modal");
  const personForm = document.getElementById("personForm");
  const editIndexInput = document.getElementById("editIndex");

  function showAddModal() {
    document.getElementById("modalTitle").textContent = "إضافة شخص جديد";
    editIndexInput.value = "";
    resetForm();
    modal.classList.remove("hidden");
  }

  function hideModal() {
    modal.classList.add("hidden");
    resetForm();
  }

  function resetForm() {
    document.getElementById("name").value = "";
    document.getElementById("sessionDate").value = "";
    document.getElementById("issueNumber").value = "";
    document.getElementById("publishDate").value = "";
    document.getElementById("publishNumber").value = "";
    document.getElementById("storageNumber").value = "";
    document.getElementById("returnDate").value = "";
    document.getElementById("inspectionStopDate").value = "";
    document.getElementById("stopReason").value = "";
    document.getElementById("fileStatus").value = "قيد المتابعة";
  }

  function editPerson(index) {
    const person = data[index];
    document.getElementById("modalTitle").textContent = "تعديل بيانات";
    editIndexInput.value = index;
    document.getElementById("name").value = person.name;
    document.getElementById("sessionDate").value = person.sessionDate;
    document.getElementById("issueNumber").value = person.issueNumber;
    document.getElementById("publishDate").value = person.publishDate;
    document.getElementById("publishNumber").value = person.publishNumber;
    document.getElementById("storageNumber").value = person.storageNumber;
    document.getElementById("returnDate").value = person.returnDate;
    document.getElementById("inspectionStopDate").value = person.inspectionStopDate;
    document.getElementById("stopReason").value = person.stopReason;
    document.getElementById("fileStatus").value = person.fileStatus;
    modal.classList.remove("hidden");
  }

  function deletePerson(index) {
    if (confirm("هل أنت متأكد من الحذف؟")) {
      data.splice(index, 1);
      renderTable();
    }
  }

  personForm.addEventListener("submit", function (e) {
    e.preventDefault();
    const index = editIndexInput.value;
    const person = {
      name: document.getElementById("name").value,
      sessionDate: document.getElementById("sessionDate").value,
      issueNumber: document.getElementById("issueNumber").value,
      publishDate: document.getElementById("publishDate").value,
      publishNumber: document.getElementById("publishNumber").value,
      storageNumber: document.getElementById("storageNumber").value,
      returnDate: document.getElementById("returnDate").value,
      inspectionStopDate: document.getElementById("inspectionStopDate").value,
      stopReason: document.getElementById("stopReason").value,
      fileStatus: document.getElementById("fileStatus").value
    };

    if (index === "") {
      // Add new
      person.id = Date.now();
      data.push(person);
    } else {
      // Update existing
      person.id = data[index].id;
      data[index] = person;
    }

    renderTable();
    hideModal();
  });

  document.getElementById("searchInput").addEventListener("input", renderTable);

  document.getElementById("toggleDarkMode").addEventListener("click", () => {
    document.documentElement.classList.toggle("dark");
    document.body.classList.toggle("dark");
  });

  // Initial Render
  function initApp() {
    updateStats();
    renderTable();
  }
</script>

</body>
</html>
