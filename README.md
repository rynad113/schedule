<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8" />
    <title>منظّم الجدول الدراسي مع مستويات</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            direction: rtl;
            padding: 20px;
            background-color: #f2f2f2;
        }

        form {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            width: 350px;
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-top: 10px;
        }

        input, select {
            width: 100%;
            padding: 8px;
            margin-top: 5px;
            box-sizing: border-box;
        }

        button {
            margin-top: 15px;
            padding: 10px;
            width: 100%;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        #schedule {
            margin-top: 20px;
            max-width: 600px;
        }

        .entry {
            background-color: #ffffff;
            padding: 10px 40px 10px 10px;
            margin-bottom: 5px;
            border-radius: 5px;
            position: relative;
            font-size: 14px;
        }

        .delete-btn {
            position: absolute;
            left: 10px;
            top: 10px;
            background-color: #ff4d4d;
            border: none;
            color: white;
            padding: 5px 8px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
        }
    </style>
</head>
<body>

<h2>منظّم الجدول الدراسي مع مستويات</h2>

<form id="scheduleForm">
    <label>المستوى الدراسي:</label>
    <select id="level" required>
        <option value="">اختر المستوى</option>
        <option>المستوى 1</option>
        <option>المستوى 2</option>
        <option>المستوى 3</option>
        <option>المستوى 4</option>
        <option>المستوى 5</option>
        <option>المستوى 6</option>
    </select>

    <label>اسم المادة:</label>
    <input type="text" id="subject" required />

    <label>اليوم:</label>
    <select id="day" required>
        <option value="">اختر اليوم</option>
        <option>الأحد</option>
        <option>الاثنين</option>
        <option>الثلاثاء</option>
        <option>الأربعاء</option>
        <option>الخميس</option>
    </select>

    <label>الوقت (مثال: 8:00 - 10:00):</label>
    <input type="text" id="time" required />

    <button type="submit">أضف إلى الجدول</button>
</form>

<div id="schedule">
    <h3>الجدول:</h3>
    <div id="entries"></div>
</div>

<script>
    const form = document.getElementById("scheduleForm");
    const entries = document.getElementById("entries");

    // نحاول نقرأ الجدول من localStorage أو نبدأ بمصفوفة فارغة
    let schedule = JSON.parse(localStorage.getItem("schedule")) || [];

    // دالة تعرض الجدول الموجود في الصفحة
    function renderSchedule() {
        entries.innerHTML = ""; // ننظف القائمة

        schedule.forEach(({ level, subject, day, time }, index) => {
            const div = document.createElement("div");
            div.className = "entry";
            div.textContent = `${level} | ${day} - ${time} | ${subject}`;

            const delBtn = document.createElement("button");
            delBtn.className = "delete-btn";
            delBtn.textContent = "حذف";
            delBtn.onclick = function() {
                schedule.splice(index, 1);
                localStorage.setItem("schedule", JSON.stringify(schedule));
                renderSchedule();
            };

            div.appendChild(delBtn);
            entries.appendChild(div);
        });
    }

    // عرض الجدول عند تحميل الصفحة أول مرة
    renderSchedule();

    form.addEventListener("submit", function(event) {
        event.preventDefault();

        const level = document.getElementById("level").value;
        const subject = document.getElementById("subject").value.trim();
        const day = document.getElementById("day").value;
        const time = document.getElementById("time").value.trim();

        // تحقق تعارض: نفس المستوى أو نفس المادة في نفس اليوم والوقت ممنوع
        const conflict = schedule.some(entry => 
            ((entry.level === level || entry.subject === subject) && entry.day === day && entry.time === time)
        );

        if (conflict) {
            alert("⚠️ يوجد تعارض: نفس المستوى أو نفس المادة موجودة في نفس اليوم والوقت. حاول تعديل المادة أو الوقت.");
            return;
        }

        schedule.push({ level, subject, day, time });
        localStorage.setItem("schedule", JSON.stringify(schedule));
        renderSchedule();
        form.reset();
    });
</script>

</body>
</html>
