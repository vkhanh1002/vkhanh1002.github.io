<!doctype html>
<html lang="vi">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes" />
    <title>Hệ thống Đặt lịch Khám bệnh & EHR | Phòng khám số</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet" />
    <style>
      :root {
        --primary: #0ea5e9;
        --primary-dark: #0284c7;
        --secondary: #e0f2fe;
        --success: #10b981;
        --warning: #f59e0b;
        --danger: #ef4444;
        --bg-main: #f1f5f9;
        --surface: #ffffff;
        --text-main: #334155;
        --text-muted: #64748b;
        --border: #e2e8f0;
      }

      * { margin: 0; padding: 0; box-sizing: border-box; font-family: "Inter", sans-serif; }
      body { background: var(--bg-main); color: var(--text-main); min-height: 100vh; }

      /* Hiệu ứng chuyển động */
      @keyframes fadeIn {
        from { opacity: 0; transform: translateY(10px); }
        to { opacity: 1; transform: translateY(0); }
      }
      .fade-in { animation: fadeIn 0.4s ease-out forwards; }

      /* --- Login UI --- */
      .login-container {
        max-width: 420px;
        margin: 8vh auto;
        background: var(--surface);
        border-radius: 24px;
        box-shadow: 0 10px 25px rgba(0,0,0,0.05), 0 4px 6px rgba(0,0,0,0.02);
        padding: 2.5rem 2rem;
        text-align: center;
      }
      .logo-area i { font-size: 3.5rem; color: var(--primary); margin-bottom: 1rem; }
      .login-container h2 { color: #0f172a; font-size: 1.5rem; margin-bottom: 1.5rem; line-height: 1.4; }
      
      .input-group { margin-bottom: 1.25rem; text-align: left; }
      .input-group label { display: block; font-weight: 600; font-size: 0.9rem; margin-bottom: 8px; color: var(--text-main); }
      .input-group input, .input-group select {
        width: 100%; padding: 12px 16px; border: 1.5px solid var(--border);
        border-radius: 12px; font-size: 1rem; transition: all 0.3s;
        background: #f8fafc;
      }
      .input-group input:focus, .input-group select:focus {
        outline: none; border-color: var(--primary); background: #fff;
        box-shadow: 0 0 0 4px var(--secondary);
      }

      button {
        background: var(--primary); color: white; border: none; padding: 12px 24px;
        border-radius: 12px; font-weight: 600; cursor: pointer; transition: all 0.2s;
        width: 100%; font-size: 1rem; display: inline-flex; align-items: center; justify-content: center; gap: 8px;
      }
      button:hover { background: var(--primary-dark); transform: translateY(-2px); box-shadow: 0 4px 12px rgba(14, 165, 233, 0.3); }

      .demo-accounts {
        margin-top: 1.5rem; font-size: 0.85rem; background: var(--bg-main);
        padding: 1rem; border-radius: 12px; color: var(--text-muted); text-align: left; line-height: 1.6;
      }

      /* --- App Layout --- */
      .app-container { display: none; max-width: 1440px; margin: 0 auto; min-height: 100vh; }
      .top-bar {
        background: var(--surface); padding: 1rem 2rem; display: flex;
        justify-content: space-between; align-items: center; box-shadow: 0 1px 3px rgba(0,0,0,0.05);
        position: sticky; top: 0; z-index: 100;
      }
      .logo { font-weight: 800; font-size: 1.5rem; color: #0f172a; display: flex; align-items: center; gap: 8px; }
      .logo i { color: var(--primary); }
      .user-info { display: flex; align-items: center; gap: 12px; font-weight: 500; }
      .logout-btn { background: var(--bg-main); color: var(--danger); width: auto; padding: 8px 16px; border-radius: 8px; font-size: 0.9rem; }
      .logout-btn:hover { background: #fee2e2; color: #b91c1c; box-shadow: none; transform: none; }

      .dashboard { display: flex; gap: 24px; padding: 24px; align-items: flex-start; }
      .sidebar {
        flex: 0 0 260px; background: var(--surface); border-radius: 16px;
        padding: 1rem 0; box-shadow: 0 4px 6px rgba(0,0,0,0.02); position: sticky; top: 88px;
      }
      .sidebar-item {
        padding: 14px 24px; display: flex; align-items: center; gap: 12px;
        cursor: pointer; transition: 0.2s; color: var(--text-muted); font-weight: 500;
        border-left: 4px solid transparent; margin-bottom: 4px;
      }
      .sidebar-item i { width: 20px; font-size: 1.1rem; text-align: center; }
      .sidebar-item:hover { color: var(--primary-dark); background: var(--bg-main); }
      .sidebar-item.active { background: var(--secondary); border-left-color: var(--primary); color: var(--primary-dark); font-weight: 600; }

      .content-area {
        flex: 1; background: var(--surface); border-radius: 16px; padding: 2rem;
        box-shadow: 0 4px 6px rgba(0,0,0,0.02); min-width: 0; /* Cho phép table co giãn */
      }
      .card-title { font-size: 1.5rem; font-weight: 700; margin-bottom: 1.5rem; color: #0f172a; display: flex; align-items: center; gap: 10px; }
      .card-title i { color: var(--primary); }

      /* --- Tables & Forms --- */
      .table-responsive { overflow-x: auto; -webkit-overflow-scrolling: touch; border-radius: 12px; border: 1px solid var(--border); }
      table { width: 100%; border-collapse: collapse; white-space: nowrap; }
      th, td { text-align: left; padding: 16px; border-bottom: 1px solid var(--border); }
      th { background: #f8fafc; font-weight: 600; color: var(--text-muted); text-transform: uppercase; font-size: 0.8rem; letter-spacing: 0.5px; }
      tr:last-child td { border-bottom: none; }
      tr:hover td { background: #f8fafc; }

      /* Cải tiến Badge */
      .badge { display: inline-flex; align-items: center; padding: 6px 12px; border-radius: 50px; font-size: 0.8rem; font-weight: 600; }
      .badge-warning { background: #fef3c7; color: #d97706; }
      .badge-success { background: #d1fae5; color: #059669; }
      .badge-danger { background: #fee2e2; color: #dc2626; }
      .badge-info { background: #e0f2fe; color: #0284c7; }

      .btn-sm { padding: 8px 16px; font-size: 0.85rem; border-radius: 8px; width: auto; font-weight: 500; }
      .btn-outline { background: transparent; border: 1.5px solid var(--border); color: var(--text-main); }
      .btn-outline:hover { background: var(--bg-main); border-color: var(--text-muted); color: var(--text-main); box-shadow: none; transform: translateY(-1px); }
      .btn-danger-outline { background: transparent; border: 1.5px solid #fca5a5; color: var(--danger); }
      .btn-danger-outline:hover { background: #fee2e2; }

      .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 20px; }
      .full-width { grid-column: 1 / -1; }
      input[type="date"], input[type="time"], textarea, select, input[type="text"] {
        width: 100%; padding: 12px 16px; border: 1.5px solid var(--border); border-radius: 12px; font-size: 0.95rem; background: #f8fafc; transition: 0.2s;
      }
      input:focus, textarea:focus, select:focus { outline: none; border-color: var(--primary); background: #fff; box-shadow: 0 0 0 3px var(--secondary); }
      label { display: block; font-weight: 600; margin-bottom: 8px; font-size: 0.9rem; color: var(--text-main); }

      /* --- Modals --- */
      .modal {
        display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
        background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(4px);
        align-items: center; justify-content: center; z-index: 1000;
        animation: fadeIn 0.2s ease-out;
      }
      .modal-content {
        background: var(--surface); max-width: 600px; width: 90%; border-radius: 24px;
        padding: 2rem; box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1);
      }
      .modal-content h3 { margin-bottom: 1.5rem; color: #0f172a; padding-bottom: 1rem; border-bottom: 1px solid var(--border); }
      
      .alert { padding: 16px; border-radius: 12px; margin-bottom: 1.5rem; background: var(--secondary); color: var(--primary-dark); border-left: 4px solid var(--primary); }

      /* Tối ưu record card */
      .record-card {
        border: 1px solid var(--border); border-radius: 16px; padding: 1.5rem; margin-bottom: 1rem; background: #fff; transition: 0.2s;
      }
      .record-card:hover { box-shadow: 0 4px 12px rgba(0,0,0,0.03); border-color: #cbd5e1; }
      .record-header { display: flex; justify-content: space-between; margin-bottom: 1rem; padding-bottom: 0.5rem; border-bottom: 1px dashed var(--border); }
      .record-body div { margin-bottom: 8px; }

      @media (max-width: 768px) {
        .dashboard { flex-direction: column; padding: 16px; }
        .sidebar { position: static; width: 100%; flex: auto; }
        .form-grid { grid-template-columns: 1fr; }
        .top-bar { padding: 1rem; }
      }
    </style>
  </head>
  <body>
    <div id="loginContainer" class="login-container">
      <div class="logo-area"><i class="fas fa-heartbeat"></i></div>
      <h2>Phòng khám Đa khoa<br><span style="color: var(--primary); font-size: 1.2rem;">Hệ thống EHR & Đặt lịch</span></h2>
      <div class="input-group">
        <label>Tài khoản</label>
        <input type="text" id="loginUsername" placeholder="Nhập tên đăng nhập" value="benhnhan1" />
      </div>
      <div class="input-group">
        <label>Mật khẩu</label>
        <input type="password" id="loginPassword" placeholder="Nhập mật khẩu" value="123" />
      </div>
      <div class="input-group">
        <label>Vai trò</label>
        <select id="loginRole">
          <option value="patient">Bệnh nhân</option>
          <option value="doctor">Bác sĩ</option>
          <option value="reception">Lễ tân</option>
          <option value="admin">Quản trị viên</option>
        </select>
      </div>
      <button id="doLoginBtn"><i class="fas fa-sign-in-alt"></i> Đăng nhập hệ thống</button>
      
      <div class="demo-accounts">
        <strong><i class="fas fa-info-circle"></i> Tài khoản Demo:</strong><br><br>
        👤 Bệnh nhân: benhnhan1 / 123 <br>
        👨‍⚕️ Bác sĩ: bacsi1 / 123 <br>
        🧑‍💼 Lễ tân: letan1 / 123 <br>
        🔧 Admin: admin1 / 123
      </div>
    </div>

    <div id="appContainer" class="app-container">
      <div class="top-bar">
        <div class="logo"><i class="fas fa-notes-medical"></i> MediCare EHR</div>
        <div class="user-info">
          <div style="text-align: right; display: flex; flex-direction: column;">
            <span id="userFullName" style="color: #0f172a;"></span>
            <span id="userRoleName" style="font-size: 0.8rem; color: var(--text-muted);"></span>
          </div>
          <button id="logoutBtn" class="logout-btn"><i class="fas fa-sign-out-alt"></i> Thoát</button>
        </div>
      </div>
      <div class="dashboard">
        <div class="sidebar" id="sidebarMenu"></div>
        <div class="content-area" id="mainContent"></div>
      </div>
    </div>

    <script>
      // ---------- KHỞI TẠO DỮ LIỆU MẪU (Giữ nguyên logic của bạn) ----------
      let db = { users: [], doctors: [], schedules: [], appointments: [], medicalRecords: [] };

      function initData() {
        if (!localStorage.getItem("clinic_ehr_db")) {
          const users = [
            { id: 1, username: "benhnhan1", password: "123", fullname: "Nguyễn Văn An", role: "patient", phone: "0987654321", email: "an@example.com", dob: "1990-05-12", address: "Hà Nội" },
            { id: 2, username: "benhnhan2", password: "123", fullname: "Trần Thị Bình", role: "patient", phone: "0912345678", email: "binh@example.com", dob: "1985-09-20", address: "Hải Phòng" },
            { id: 3, username: "bacsi1", password: "123", fullname: "BS. Lê Văn Thành", role: "doctor", specialty: "Nội tổng quát", phone: "0903123456" },
            { id: 4, username: "bacsi2", password: "123", fullname: "BS. Phạm Thị Hương", role: "doctor", specialty: "Nhi khoa", phone: "0903123789" },
            { id: 5, username: "letan1", password: "123", fullname: "Trần Văn Nam", role: "reception", phone: "0988777666" },
            { id: 6, username: "admin1", password: "123", fullname: "Quản trị viên", role: "admin", phone: "0999999999" }
          ];
          const schedules = [
            { id: 1, doctorId: 3, workDate: "2025-04-17", startTime: "08:00", endTime: "11:30", interval: 30 },
            { id: 2, doctorId: 3, workDate: "2025-04-18", startTime: "08:00", endTime: "11:30", interval: 30 },
            { id: 3, doctorId: 4, workDate: "2025-04-17", startTime: "13:30", endTime: "16:30", interval: 30 },
            { id: 4, doctorId: 4, workDate: "2025-04-19", startTime: "08:00", endTime: "11:00", interval: 30 }
          ];
          const appointments = [
            { id: 1, patientId: 1, doctorId: 3, scheduleId: 1, appointmentDate: "2025-04-17", timeSlot: "09:00", status: "completed", reason: "Đau đầu", createdAt: "2025-04-10" },
            { id: 2, patientId: 2, doctorId: 4, scheduleId: 3, appointmentDate: "2025-04-17", timeSlot: "14:00", status: "pending", reason: "Sốt", createdAt: "2025-04-11" }
          ];
          const medicalRecords = [
            { id: 1, appointmentId: 1, patientId: 1, doctorId: 3, diagnosis: "Căng thẳng thần kinh", treatment: "Nghỉ ngơi, thuốc giảm đau", prescription: "Paracetamol 500mg x 10 viên, uống khi đau", createdDate: "2025-04-17" }
          ];
          localStorage.setItem("clinic_ehr_db", JSON.stringify({ users, schedules, appointments, medicalRecords }));
        }
        loadFromLocal();
      }

      function loadFromLocal() {
        const data = JSON.parse(localStorage.getItem("clinic_ehr_db"));
        db = data;
        db.doctors = db.users.filter((u) => u.role === "doctor");
      }

      function saveToLocal() {
        localStorage.setItem("clinic_ehr_db", JSON.stringify({ users: db.users, schedules: db.schedules, appointments: db.appointments, medicalRecords: db.medicalRecords }));
        loadFromLocal();
      }

      function getDoctorName(id) { const d = db.doctors.find(u => u.id === id); return d ? d.fullname : "BS. Ẩn"; }
      function getPatientName(id) { const p = db.users.find(u => u.id === id && u.role === "patient"); return p ? p.fullname : "BN Ẩn"; }
      
      // Helper function cho UI Badge
      function getStatusBadge(status) {
        if(status === 'pending') return '<span class="badge badge-warning"><i class="fas fa-clock mr-1"></i> Chờ khám</span>';
        if(status === 'confirmed') return '<span class="badge badge-info"><i class="fas fa-calendar-check mr-1"></i> Đã xác nhận</span>';
        if(status === 'completed') return '<span class="badge badge-success"><i class="fas fa-check-circle mr-1"></i> Đã khám</span>';
        if(status === 'cancelled') return '<span class="badge badge-danger"><i class="fas fa-times-circle mr-1"></i> Đã hủy</span>';
        return `<span class="badge">${status}</span>`;
      }

      let currentUser = null;
      let currentActiveMenu = "dashboard";

      function renderSidebarAndContent() {
        if (!currentUser) return;
        const role = currentUser.role;
        let menuItems = [];
        if (role === "patient") {
          menuItems = [
            { key: "dashboard", icon: "fas fa-home", label: "Trang chủ" },
            { key: "book_appointment", icon: "fas fa-calendar-plus", label: "Đặt lịch khám" },
            { key: "my_appointments", icon: "fas fa-list-alt", label: "Lịch hẹn của tôi" },
            { key: "my_ehr", icon: "fas fa-folder-open", label: "Hồ sơ sức khỏe" }
          ];
        } else if (role === "doctor") {
          menuItems = [
            { key: "dashboard", icon: "fas fa-user-md", label: "Lịch khám hôm nay" },
            { key: "all_appointments", icon: "fas fa-calendar-alt", label: "Tất cả lịch hẹn" }
          ];
        } else if (role === "reception") {
          menuItems = [
            { key: "dashboard", icon: "fas fa-concierge-bell", label: "Quản lý đặt lịch" },
            { key: "new_appointment", icon: "fas fa-plus-circle", label: "Tạo lịch hẹn mới" },
            { key: "manage_patients", icon: "fas fa-users", label: "Danh sách bệnh nhân" }
          ];
        } else if (role === "admin") {
          menuItems = [
            { key: "dashboard", icon: "fas fa-chart-pie", label: "Báo cáo thống kê" },
            { key: "manage_doctors", icon: "fas fa-user-md", label: "Quản lý Bác sĩ" },
            { key: "manage_schedules", icon: "fas fa-clock", label: "Phân ca làm việc" }
          ];
        }
        
        document.getElementById("sidebarMenu").innerHTML = menuItems.map(item => 
          `<div class="sidebar-item ${currentActiveMenu === item.key ? "active" : ""}" data-menu="${item.key}">
            <i class="${item.icon}"></i> <span>${item.label}</span>
          </div>`
        ).join("");

        document.querySelectorAll(".sidebar-item").forEach(el => {
          el.addEventListener("click", () => {
            currentActiveMenu = el.dataset.menu;
            renderSidebarAndContent();
            renderContentByMenu(currentActiveMenu);
          });
        });
      }

      function updateContentWrap(html) {
        const container = document.getElementById("mainContent");
        container.innerHTML = `<div class="fade-in">${html}</div>`;
      }

      function renderContentByMenu(menuKey) {
        const role = currentUser.role;
        if (role === "patient") {
          if (menuKey === "dashboard") renderPatientDashboard();
          else if (menuKey === "book_appointment") renderBookAppointment();
          else if (menuKey === "my_appointments") renderMyAppointments();
          else if (menuKey === "my_ehr") renderMyEHR();
        } else if (role === "doctor") {
          if (menuKey === "dashboard") renderDoctorTodaysAppointments();
          else if (menuKey === "all_appointments") renderAllAppointmentsForDoctor();
        } else if (role === "reception") {
          if (menuKey === "dashboard") renderReceptionAppointments();
          else if (menuKey === "manage_patients") renderManagePatients();
          else if (menuKey === "new_appointment") renderCreateAppointmentByReception();
        } else if (role === "admin") {
          if (menuKey === "dashboard") renderAdminStats();
          else if (menuKey === "manage_doctors") renderManageDoctors();
          else if (menuKey === "manage_schedules") renderManageSchedules();
        }
      }

      /* ================= THÊM CHỨC NĂNG RENDER VỚI UI MỚI ================= */
      
      // --- BỆNH NHÂN ---
      function renderPatientDashboard() {
        let totalApps = db.appointments.filter(a => a.patientId === currentUser.id).length;
        let totalRecords = db.medicalRecords.filter(r => r.patientId === currentUser.id).length;
        let html = `
          <div class="card-title"><i class="fas fa-hand-holding-medical"></i> Chào buổi sáng, ${currentUser.fullname}</div>
          <div class="alert"><i class="fas fa-shield-alt"></i> Dữ liệu sức khỏe của bạn được bảo mật an toàn trên hệ thống.</div>
          <div class="form-grid">
            <div style="background: var(--secondary); padding: 24px; border-radius: 16px; border: 1px solid #bae6fd;">
              <h3 style="color: var(--primary-dark); font-size: 2rem;">${totalApps}</h3>
              <p style="color: var(--text-muted); font-weight: 500; margin-top: 8px;"><i class="fas fa-calendar-check"></i> Tổng lượt đặt khám</p>
            </div>
            <div style="background: #d1fae5; padding: 24px; border-radius: 16px; border: 1px solid #a7f3d0;">
              <h3 style="color: #047857; font-size: 2rem;">${totalRecords}</h3>
              <p style="color: #065f46; font-weight: 500; margin-top: 8px;"><i class="fas fa-file-medical"></i> Hồ sơ y tế lưu trữ</p>
            </div>
          </div>`;
        updateContentWrap(html);
      }

      function renderBookAppointment() {
        let doctorsList = db.doctors.map(d => `<option value="${d.id}">${d.fullname} - Chuyên khoa: ${d.specialty}</option>`).join("");
        let html = `
          <div class="card-title"><i class="fas fa-calendar-plus"></i> Đặt lịch khám mới</div>
          <div class="form-grid">
            <div><label>Bác sĩ điều trị</label><select id="selDoctor"><option value="">-- Chọn Bác sĩ --</option>${doctorsList}</select></div>
            <div><label>Ngày khám</label><input type="date" id="appointmentDate" min="${new Date().toISOString().split("T")[0]}"></div>
            <div class="full-width"><label>Khung giờ trống</label><select id="timeSlotSelect" disabled><option>-- Vui lòng chọn ngày và bác sĩ trước --</option></select></div>
            <div class="full-width"><label>Lý do khám / Triệu chứng</label><textarea id="reason" rows="3" placeholder="Mô tả chi tiết triệu chứng của bạn để bác sĩ nắm bắt..."></textarea></div>
            <div class="full-width"><button id="confirmBookingBtn"><i class="fas fa-paper-plane"></i> Gửi yêu cầu đặt lịch</button></div>
          </div>`;
        updateContentWrap(html);
        
        // Logic load slot giữ nguyên
        const doctorSelect = document.getElementById("selDoctor");
        const dateInput = document.getElementById("appointmentDate");
        const timeSlotSelect = document.getElementById("timeSlotSelect");
        
        function loadSlots() {
          const doctorId = parseInt(doctorSelect.value);
          const date = dateInput.value;
          if (!doctorId || !date) { timeSlotSelect.disabled = true; return; }
          timeSlotSelect.disabled = false;
          const schedule = db.schedules.find(s => s.doctorId === doctorId && s.workDate === date);
          if (!schedule) { timeSlotSelect.innerHTML = "<option>Bác sĩ không có lịch làm việc ngày này</option>"; return; }
          
          let startMin = parseInt(schedule.startTime.split(":")[0])*60 + parseInt(schedule.startTime.split(":")[1]);
          let endMin = parseInt(schedule.endTime.split(":")[0])*60 + parseInt(schedule.endTime.split(":")[1]);
          let slots = [];
          for (let t = startMin; t < endMin; t += schedule.interval) {
            slots.push(`${Math.floor(t/60).toString().padStart(2,"0")}:${(t%60).toString().padStart(2,"0")}`);
          }
          const booked = db.appointments.filter(a => a.doctorId === doctorId && a.appointmentDate === date && a.status !== "cancelled").map(a => a.timeSlot);
          const available = slots.filter(s => !booked.includes(s));
          timeSlotSelect.innerHTML = available.length ? available.map(s => `<option value="${s}">${s}</option>`).join("") : "<option>Đã kín lịch</option>";
        }
        doctorSelect.addEventListener("change", loadSlots);
        dateInput.addEventListener("change", loadSlots);
        
        document.getElementById("confirmBookingBtn").addEventListener("click", () => {
          if(!doctorSelect.value || !dateInput.value || timeSlotSelect.value.includes("kín") || timeSlotSelect.value.includes("Vui lòng")) return alert("Vui lòng nhập đủ thông tin hợp lệ!");
          db.appointments.push({
            id: db.appointments.length + 1, patientId: currentUser.id, doctorId: parseInt(doctorSelect.value),
            appointmentDate: dateInput.value, timeSlot: timeSlotSelect.value, status: "pending",
            reason: document.getElementById("reason").value, createdAt: new Date().toISOString().split("T")[0]
          });
          saveToLocal(); alert("Đặt lịch thành công! Vui lòng chờ lễ tân xác nhận.");
          currentActiveMenu = "my_appointments"; renderSidebarAndContent();
        });
      }

      function renderMyAppointments() {
        let apps = db.appointments.filter(a => a.patientId === currentUser.id).reverse();
        let html = `<div class="card-title"><i class="fas fa-list-ul"></i> Lịch sử đặt khám</div><div class="table-responsive"><table>
          <tr><th>Bác sĩ</th><th>Ngày khám</th><th>Giờ</th><th>Lý do</th><th>Trạng thái</th><th>Thao tác</th></tr>`;
        apps.forEach(app => {
          html += `<tr><td><strong style="color:var(--text-main)">${getDoctorName(app.doctorId)}</strong></td><td>${app.appointmentDate}</td><td><i class="far fa-clock"></i> ${app.timeSlot}</td>
          <td>${app.reason}</td><td>${getStatusBadge(app.status)}</td>
          <td>${app.status === "pending" ? `<button class="btn-sm btn-danger-outline cancel-app" data-id="${app.id}">Hủy lịch</button>` : ""}</td></tr>`;
        });
        html += `</table></div>`;
        updateContentWrap(html);
        document.querySelectorAll(".cancel-app").forEach(btn => btn.addEventListener("click", () => {
          if(confirm("Bạn có chắc chắn muốn hủy lịch khám này?")) {
            db.appointments.find(a => a.id == btn.dataset.id).status = "cancelled"; saveToLocal(); renderMyAppointments();
          }
        }));
      }

      function renderMyEHR() {
        let records = db.medicalRecords.filter(r => r.patientId === currentUser.id).reverse();
        let html = `<div class="card-title"><i class="fas fa-folder-open"></i> Hồ sơ Sức khỏe Điện tử</div>`;
        if(!records.length) html += `<div class="alert" style="background:#f1f5f9; color:#64748b; border:none; text-align:center;">Chưa có dữ liệu bệnh án.</div>`;
        records.forEach(rec => {
          html += `<div class="record-card">
            <div class="record-header">
              <div><strong><i class="fas fa-calendar-day text-primary"></i> ${rec.createdDate}</strong></div>
              <div><span class="badge badge-info"><i class="fas fa-user-md"></i> ${getDoctorName(rec.doctorId)}</span></div>
            </div>
            <div class="record-body">
              <div><strong style="color:var(--text-muted)">Chẩn đoán:</strong> <span style="font-weight:500">${rec.diagnosis}</span></div>
              <div><strong style="color:var(--text-muted)">Chỉ định điều trị:</strong> ${rec.treatment}</div>
              <div style="background:#f8fafc; padding:12px; border-radius:8px; margin-top:12px;"><strong style="color:var(--primary-dark)"><i class="fas fa-pills"></i> Đơn thuốc:</strong><br>${rec.prescription.replace(/\n/g, '<br>')}</div>
            </div>
          </div>`;
        });
        updateContentWrap(html);
      }

      // --- BÁC SĨ ---
      function renderDoctorTodaysAppointments() {
        let today = new Date().toISOString().split("T")[0];
        let apps = db.appointments.filter(a => a.doctorId === currentUser.id && a.appointmentDate === today && a.status !== 'cancelled');
        let html = `<div class="card-title"><i class="fas fa-stethoscope"></i> Ca khám hôm nay (${today})</div><div class="table-responsive"><table>
          <tr><th>Giờ khám</th><th>Bệnh nhân</th><th>Triệu chứng</th><th>Trạng thái</th><th>Thao tác</th></tr>`;
        apps.forEach(app => {
          html += `<tr><td><strong>${app.timeSlot}</strong></td><td>${getPatientName(app.patientId)}</td><td>${app.reason}</td><td>${getStatusBadge(app.status)}</td>
          <td>${app.status !== 'completed' ? `<button class="btn-sm examineBtn" data-id="${app.id}"><i class="fas fa-edit"></i> Khám bệnh</button>` : `<span style="color:var(--success)"><i class="fas fa-check"></i> Đã xử lý</span>`}</td></tr>`;
        });
        html += `</table></div>`;
        updateContentWrap(html);

        document.querySelectorAll(".examineBtn").forEach(btn => btn.addEventListener("click", () => {
          let app = db.appointments.find(a => a.id == btn.dataset.id);
          let modalHtml = `<div id="examineModal" class="modal" style="display:flex;"><div class="modal-content">
            <h3><i class="fas fa-notes-medical" style="color:var(--primary)"></i> Hồ sơ khám bệnh: ${getPatientName(app.patientId)}</h3>
            <div class="form-grid">
              <div class="full-width"><label>Kết luận chẩn đoán:</label><textarea id="diag" rows="2"></textarea></div>
              <div class="full-width"><label>Phương pháp điều trị:</label><textarea id="treat" rows="2"></textarea></div>
              <div class="full-width"><label>Đơn thuốc (Tên thuốc - Liều dùng):</label><textarea id="presc" rows="3" placeholder="VD: Paracetamol 500mg - Sáng 1 viên..."></textarea></div>
              <div class="full-width" style="display:flex; gap:12px;">
                <button id="saveEhrBtn"><i class="fas fa-save"></i> Lưu & Hoàn tất</button>
                <button id="closeModalBtn" class="btn-outline">Hủy bỏ</button>
              </div>
            </div></div></div>`;
          document.body.insertAdjacentHTML("beforeend", modalHtml);
          
          document.getElementById("saveEhrBtn").addEventListener("click", () => {
            if(!document.getElementById("diag").value) return alert("Cần nhập chẩn đoán!");
            db.medicalRecords.push({
              id: db.medicalRecords.length + 1, appointmentId: app.id, patientId: app.patientId, doctorId: currentUser.id,
              diagnosis: document.getElementById("diag").value, treatment: document.getElementById("treat").value,
              prescription: document.getElementById("presc").value, createdDate: today
            });
            app.status = "completed"; saveToLocal();
            document.getElementById("examineModal").remove(); renderDoctorTodaysAppointments();
          });
          document.getElementById("closeModalBtn").addEventListener("click", () => document.getElementById("examineModal").remove());
        }));
      }

      function renderAllAppointmentsForDoctor() {
        let apps = db.appointments.filter(a => a.doctorId === currentUser.id).reverse();
        let html = `<div class="card-title"><i class="fas fa-calendar-alt"></i> Tất cả lịch hẹn</div><div class="table-responsive"><table>
          <tr><th>Ngày</th><th>Giờ</th><th>Bệnh nhân</th><th>Trạng thái</th></tr>`;
        apps.forEach(a => html += `<tr><td>${a.appointmentDate}</td><td>${a.timeSlot}</td><td>${getPatientName(a.patientId)}</td><td>${getStatusBadge(a.status)}</td></tr>`);
        html += `</table></div>`;
        updateContentWrap(html);
      }

      // --- LỄ TÂN & ADMIN (Render cơ bản giữ nguyên layout bảng mới) ---
      function renderReceptionAppointments() {
        let apps = db.appointments.slice().reverse();
        let html = `<div class="card-title"><i class="fas fa-concierge-bell"></i> Tiếp nhận Bệnh nhân</div><div class="table-responsive"><table>
          <tr><th>Ngày/Giờ</th><th>Bệnh nhân</th><th>Bác sĩ</th><th>Trạng thái</th><th>Thao tác</th></tr>`;
        apps.forEach(app => {
          html += `<tr><td>${app.appointmentDate}<br><span style="color:var(--text-muted); font-size:0.85rem">${app.timeSlot}</span></td>
          <td><strong>${getPatientName(app.patientId)}</strong></td><td>${getDoctorName(app.doctorId)}</td><td>${getStatusBadge(app.status)}</td>
          <td>${app.status === "pending" ? `<button class="btn-sm confirmApp" style="background:var(--success); color:white;" data-id="${app.id}"><i class="fas fa-check"></i> Duyệt</button> <button class="btn-sm btn-danger-outline cancelApp" data-id="${app.id}">Hủy</button>` : ""}</td></tr>`;
        });
        html += `</table></div>`;
        updateContentWrap(html);
        
        document.querySelectorAll(".confirmApp").forEach(btn => btn.addEventListener("click", () => {
          db.appointments.find(a => a.id == btn.dataset.id).status = "confirmed"; saveToLocal(); renderReceptionAppointments();
        }));
        document.querySelectorAll(".cancelApp").forEach(btn => btn.addEventListener("click", () => {
          db.appointments.find(a => a.id == btn.dataset.id).status = "cancelled"; saveToLocal(); renderReceptionAppointments();
        }));
      }

      function renderManagePatients() {
        let pts = db.users.filter(u => u.role === "patient");
        let html = `<div class="card-title"><i class="fas fa-users"></i> Quản lý hồ sơ bệnh nhân</div><div class="table-responsive"><table>
          <tr><th>Mã BN</th><th>Họ tên</th><th>Số điện thoại</th><th>Tài khoản hệ thống</th></tr>`;
        pts.forEach(p => html += `<tr><td>#BN${p.id.toString().padStart(4,'0')}</td><td><strong>${p.fullname}</strong></td><td>${p.phone||'N/A'}</td><td><span class="badge badge-info">${p.username}</span></td></tr>`);
        html += `</table></div><div style="margin-top:20px;"><button id="addPatientBtn" style="width:auto;"><i class="fas fa-user-plus"></i> Đăng ký bệnh nhân mới</button></div>`;
        updateContentWrap(html);
        
        document.getElementById("addPatientBtn").addEventListener("click", () => {
          let name = prompt("Nhập họ tên Bệnh nhân:"); if(!name) return;
          let newId = Math.max(...db.users.map(u => u.id)) + 1;
          db.users.push({ id: newId, username: `bn${newId}`, password: "123", fullname: name, role: "patient" });
          saveToLocal(); alert(`Tạo thành công! Tài khoản đăng nhập là: bn${newId} / Mật khẩu: 123`); renderManagePatients();
        });
      }

      function renderCreateAppointmentByReception() {
        let html = `<div class="card-title"><i class="fas fa-plus-circle"></i> Đăng ký khám trực tiếp (Tại quầy)</div>
        <div class="form-grid">
          <div><label>Bệnh nhân</label><select id="patId">${db.users.filter(u=>u.role==="patient").map(p=>`<option value="${p.id}">${p.fullname}</option>`)}</select></div>
          <div><label>Bác sĩ</label><select id="docId">${db.doctors.map(d=>`<option value="${d.id}">${d.fullname}</option>`)}</select></div>
          <div><label>Ngày khám</label><input type="date" id="appDate" value="${new Date().toISOString().split("T")[0]}"></div>
          <div><label>Giờ khám</label><input type="time" id="appTime"></div>
          <div class="full-width"><button id="createRecApp"><i class="fas fa-calendar-check"></i> Tạo lịch khám</button></div>
        </div>`;
        updateContentWrap(html);
        document.getElementById("createRecApp").addEventListener("click", () => {
          if(!document.getElementById("appDate").value || !document.getElementById("appTime").value) return alert("Điền đủ ngày giờ!");
          db.appointments.push({
            id: db.appointments.length + 1, patientId: parseInt(document.getElementById("patId").value), doctorId: parseInt(document.getElementById("docId").value),
            appointmentDate: document.getElementById("appDate").value, timeSlot: document.getElementById("appTime").value,
            status: "confirmed", reason: "Khám trực tiếp", createdAt: new Date().toISOString()
          }); saveToLocal(); alert("Đã xếp lịch thành công!"); renderReceptionAppointments(); currentActiveMenu="dashboard"; renderSidebarAndContent();
        });
      }

      function renderAdminStats() {
        let comp = db.appointments.filter(a => a.status === "completed").length;
        let rev = (comp * 250000).toLocaleString('vi-VN');
        let html = `<div class="card-title"><i class="fas fa-chart-line"></i> Dashboard Quản trị</div>
        <div class="form-grid">
          <div style="background:#fff; border:1px solid var(--border); padding:20px; border-radius:16px; border-left:4px solid var(--primary)">
            <h4 style="color:var(--text-muted); margin-bottom:10px;">Tổng Bệnh nhân</h4><h2 style="font-size:2rem">${db.users.filter(u=>u.role==="patient").length}</h2>
          </div>
          <div style="background:#fff; border:1px solid var(--border); padding:20px; border-radius:16px; border-left:4px solid var(--success)">
            <h4 style="color:var(--text-muted); margin-bottom:10px;">Ca khám hoàn thành</h4><h2 style="font-size:2rem">${comp}</h2>
          </div>
          <div class="full-width" style="background:#fff; border:1px solid var(--border); padding:20px; border-radius:16px; border-left:4px solid var(--warning)">
            <h4 style="color:var(--text-muted); margin-bottom:10px;">Ước tính doanh thu (Demo: 250k/ca)</h4><h2 style="font-size:2rem; color:var(--warning)">${rev} VNĐ</h2>
          </div>
        </div>`;
        updateContentWrap(html);
      }

      function renderManageDoctors() {
        let html = `<div class="card-title"><i class="fas fa-user-md"></i> Danh sách Y Bác sĩ</div><div class="table-responsive"><table>
          <tr><th>ID</th><th>Họ tên</th><th>Chuyên khoa</th><th>Tài khoản</th></tr>`;
        db.doctors.forEach(d => html += `<tr><td>#BS${d.id}</td><td><strong>${d.fullname}</strong></td><td><span class="badge badge-info">${d.specialty}</span></td><td>${d.username}</td></tr>`);
        html += `</table></div>`; updateContentWrap(html);
      }
      function renderManageSchedules() {
        let html = `<div class="card-title"><i class="fas fa-clock"></i> Cấu hình Ca làm việc</div><div class="table-responsive"><table>
          <tr><th>Bác sĩ</th><th>Ngày làm việc</th><th>Ca khám (Bắt đầu - Kết thúc)</th></tr>`;
        db.schedules.forEach(s => html += `<tr><td><strong>${getDoctorName(s.doctorId)}</strong></td><td>${s.workDate}</td><td><span class="badge badge-warning">${s.startTime} - ${s.endTime}</span></td></tr>`);
        html += `</table></div>`; updateContentWrap(html);
      }

      // --- LOGIN EVENT ---
      document.getElementById("doLoginBtn").addEventListener("click", () => {
        let user = db.users.find(u => u.username === document.getElementById("loginUsername").value && u.password === document.getElementById("loginPassword").value && u.role === document.getElementById("loginRole").value);
        if (user) {
          currentUser = user;
          document.getElementById("loginContainer").style.display = "none"; document.getElementById("appContainer").style.display = "block";
          document.getElementById("userFullName").innerText = user.fullname;
          const roleMap = { patient: "Bệnh nhân", doctor: "Bác sĩ chuyên khoa", reception: "Lễ tân tiếp đón", admin: "Ban quản lý" };
          document.getElementById("userRoleName").innerText = roleMap[user.role];
          currentActiveMenu = "dashboard"; renderSidebarAndContent();
        } else alert("Tài khoản, Mật khẩu hoặc Vai trò không hợp lệ!");
      });

      document.getElementById("logoutBtn").addEventListener("click", () => {
        currentUser = null; document.getElementById("appContainer").style.display = "none"; document.getElementById("loginContainer").style.display = "block";
      });

      initData();
    </script>
  </body>
</html>
