<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تنظيم المحاضرات والمهام اليومية</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            direction: rtl;
            background-color: #f4f4f9;
            padding: 20px;
        }
        .container {
            max-width: 900px;
            margin: auto;
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
            color: #333;
        }
        .form-container {
            margin-bottom: 20px;
        }
        input, select, button {
            padding: 10px;
            margin: 10px 0;
            width: 100%;
            border-radius: 5px;
            border: 1px solid #ddd;
        }
        table {
            width: 100%;
            margin-top: 20px;
            border-collapse: collapse;
        }
        table, th, td {
            border: 1px solid #ddd;
        }
        th, td {
            padding: 10px;
            text-align: center;
        }
        .complete-btn {
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            border: none;
            margin: 5px;
            background-color: #4CAF50;
            color: white;
        }
        .checkmark {
            font-size: 30px;
            color: green;
            text-align: center;
            display: block;
        }
        .complete-day-btn {
            padding: 5px 10px;
            background-color: #f44336; /* لون أحمر للحذف */
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>تنظيم المحاضرات والمهام اليومية</h1>

        <!-- نموذج إضافة المحاضرات -->
        <div class="form-container">
            <h3>إضافة محاضرة</h3>
            <input type="text" id="subjectInput" placeholder="اسم المادة" />
            <input type="time" id="timeInput" />
            <select id="dayInput">
                <option value="">اختار اليوم</option>
                <option value="السبت">السبت</option>
                <option value="الأحد">الأحد</option>
                <option value="الإثنين">الإثنين</option>
                <option value="الثلاثاء">الثلاثاء</option>
                <option value="الأربعاء">الأربعاء</option>
                <option value="الخميس">الخميس</option>
            </select>
            <button id="addLectureBtn">أضف محاضرة</button>
        </div>

        <!-- نموذج إضافة المهام اليومية -->
        <div class="form-container">
            <h3>إضافة مهمة يومية</h3>
            <input type="text" id="taskInput" placeholder="اسم المهمة" />
            <select id="taskDayInput">
                <option value="">اختار اليوم</option>
                <option value="السبت">السبت</option>
                <option value="الأحد">الأحد</option>
                <option value="الإثنين">الإثنين</option>
                <option value="الثلاثاء">الثلاثاء</option>
                <option value="الأربعاء">الأربعاء</option>
                <option value="الخميس">الخميس</option>
            </select>
            <input type="time" id="taskTimeInput" />
            <button id="addTaskBtn">أضف مهمة</button>
        </div>

        <!-- عرض جدول الأيام (السبت - الخميس) -->
        <h3>جدول المحاضرات والمهام اليومية</h3>
        <table id="weeklyTable">
            <thead>
                <tr>
                    <th>اليوم</th>
                    <th>المهام والمحاضرات</th>
                    <th>الإجراءات</th>
                </tr>
            </thead>
            <tbody>
                <!-- سيتم إضافة البيانات هنا -->
            </tbody>
        </table>
    </div>

    <script>
        // عناصر HTML
        const addLectureBtn = document.getElementById('addLectureBtn');
        const subjectInput = document.getElementById('subjectInput');
        const timeInput = document.getElementById('timeInput');
        const dayInput = document.getElementById('dayInput');
        const addTaskBtn = document.getElementById('addTaskBtn');
        const taskInput = document.getElementById('taskInput');
        const taskDayInput = document.getElementById('taskDayInput');
        const taskTimeInput = document.getElementById('taskTimeInput');
        const weeklyTable = document.getElementById('weeklyTable').getElementsByTagName('tbody')[0];

        // استرجاع البيانات المخزنة
        let lectures = JSON.parse(localStorage.getItem('lectures')) || [];
        let tasks = JSON.parse(localStorage.getItem('tasks')) || [];

        // تحميل البيانات
        function loadSchedule() {
            weeklyTable.innerHTML = '';
            const daysOfWeek = ['السبت', 'الأحد', 'الإثنين', 'الثلاثاء', 'الأربعاء', 'الخميس'];

            daysOfWeek.forEach(day => {
                const dayLectures = lectures.filter(lecture => lecture.day === day);
                const dayTasks = tasks.filter(task => task.day === day);

                const row = weeklyTable.insertRow();
                row.innerHTML = `
                    <td>${day}</td>
                    <td>
                        ${generateTaskLectureList(dayLectures, dayTasks)}
                    </td>
                    <td>
                        <button class="complete-day-btn" onclick="deleteAllTasksAndLectures('${day}', this)">حذف كل المهام والمحاضرات</button>
                    </td>
                `;
            });
        }

        function generateTaskLectureList(lectures, tasks) {
            let content = '';
            lectures.forEach(lecture => {
                content += `<div id="lecture-${lecture.subject}" class="lecture">
                    <strong>محاضرة:</strong> ${lecture.subject} - ${lecture.time}
                    ${lecture.completed ? '<span class="checkmark">&#10003;</span>' : `<button class="complete-btn" onclick="completeLecture('${lecture.subject}')">إتمام المحاضرة</button>`}
                </div>`;
            });
            tasks.forEach(task => {
                content += `<div id="task-${task.name}" class="task">
                    <strong>مهمة:</strong> ${task.name} - ${task.time}
                    ${task.completed ? '<span class="checkmark">&#10003;</span>' : `<button class="complete-btn" onclick="completeTask('${task.name}')">إتمام المهمة</button>`}
                </div>`;
            });
            return content || 'لا يوجد مهام أو محاضرات لهذا اليوم';
        }

        // إضافة محاضرة
        addLectureBtn.addEventListener('click', () => {
            const subject = subjectInput.value.trim();
            const time = timeInput.value;
            const day = dayInput.value;

            if (!subject || !time || !day) {
                alert('يرجى ملء جميع الحقول!');
                return;
            }

            const newLecture = { subject, time, day, completed: false };
            lectures.push(newLecture);
            localStorage.setItem('lectures', JSON.stringify(lectures));

            subjectInput.value = '';
            timeInput.value = '';
            dayInput.value = '';

            loadSchedule();
        });

        // إضافة مهمة يومية
        addTaskBtn.addEventListener('click', () => {
            const taskName = taskInput.value.trim();
            const taskDay = taskDayInput.value;
            const taskTime = taskTimeInput.value;

            if (!taskName || !taskDay || !taskTime) {
                alert('يرجى ملء جميع الحقول!');
                return;
            }

            const newTask = { name: taskName, day: taskDay, time: taskTime, completed: false };
            tasks.push(newTask);
            localStorage.setItem('tasks', JSON.stringify(tasks));

            taskInput.value = '';
            taskDayInput.value = '';
            taskTimeInput.value = '';

            loadSchedule();
        });

        // إتمام المهمة
        function completeTask(taskName) {
            tasks = tasks.map(task => {
                if (task.name === taskName) {
                    task.completed = true;
                    document.getElementById(`task-${taskName}`).style.display = 'none'; // إخفاء المهمة من الجدول
                }
                return task;
            });
            localStorage.setItem('tasks', JSON.stringify(tasks));
            loadSchedule();
        }

        // إتمام المحاضرة
        function completeLecture(subject) {
            lectures = lectures.map(lecture => {
                if (lecture.subject === subject) {
                    lecture.completed = true; // تعيين المحاضرة على أنها مكتملة
                    document.getElementById(`lecture-${subject}`).innerHTML += '<span class="checkmark">&#10003;</span>'; // إضافة علامة صح
                }
                return lecture;
            });
            localStorage.setItem('lectures', JSON.stringify(lectures));
            loadSchedule();
        }

        // حذف جميع المهام والمحاضرات لليوم المحدد
        function deleteAllTasksAndLectures(day, button) {
            // حذف المهام والمحاضرات لهذا اليوم من التخزين المحلي
            lectures = lectures.filter(lecture => lecture.day !== day);
            tasks = tasks.filter(task => task.day !== day);
            
            // تحديث التخزين المحلي
            localStorage.setItem('lectures', JSON.stringify(lectures));
            localStorage.setItem('tasks', JSON.stringify(tasks));

            // إعادة تحميل الجدول
            loadSchedule();
        }

        // تحميل البيانات عند تحميل الصفحة
        loadSchedule();
    </script>
</body>
<footer>
    <p>&copy; @2024حقوق النشر محفوظه </p>
    <p>Designed by Eng Mohamed Alaa</p>
  </footer>
  
</html>
