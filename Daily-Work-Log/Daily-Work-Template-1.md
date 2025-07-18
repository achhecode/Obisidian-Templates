<%*
// Folder structure
const baseFolder = "Daily-Logs";
const moment = window.moment;
const today = moment();

const weekStart = today.clone().startOf('week').add(1, 'day');
const weekEnd = today.clone().endOf('week').add(1, 'day');

const currentDate = today.format("YYYY-MM-DD");
const currentMonth = today.format("YYYY-MM");
const currentWeekStart = weekStart.format("YYYY-MM-DD");
const currentWeekEnd = weekEnd.format("YYYY-MM-DD");
const weekFolder = `${currentWeekStart}_to_${currentWeekEnd}`;

const targetFolder = `${baseFolder}/${currentMonth}/${weekFolder}`;
const fileName = `${currentDate}.md`;
const filePath = `${targetFolder}/${fileName}`;

// Create folder structure
await app.vault.createFolder(baseFolder).catch(() => {});
await app.vault.createFolder(`${baseFolder}/${currentMonth}`).catch(() => {});
await app.vault.createFolder(`${baseFolder}/${currentMonth}/${weekFolder}`).catch(() => {});

// Check for existing file
if (await app.vault.adapter.exists(filePath)) {
    // Open existing file instead of creating a new one
    const existingFile = app.vault.getAbstractFileByPath(filePath);
    await app.workspace.getLeaf().openFile(existingFile);
    tR = `✅ Daily note for ${currentDate} already exists. Opened existing file.`;
} else {
    const createdDate = today.format("YYYY-MM-DD HH:mm:ss");
    const content = `---
title: "Daily-Log-${currentDate}"
date: ${currentDate}
created: ${createdDate}
status: "In Progress"
tags: [daily-log, software-development]
---

# Daily Work Log: ${today.format("dddd, MMMM Do YYYY")}

## 1️⃣ Focus Tasks for Today
- [ ] Task 1
- [ ] Task 2
- [ ] Task 3

## 2️⃣ Pending Tasks from Previous Days
\`\`\`dataview
task
from "${baseFolder}"
where !completed and date < date("${currentDate}")
sort date desc
\`\`\`

## 3️⃣ Meetings & Discussions
| Time       | Meeting Title              | Notes/Action Items                  |
|------------|----------------------------|------------------------------------|
| 10:15 AM   | Daily Standup              |                                    |

## 4️⃣ Git Commits Log
- [ ] Commit 1
- [ ] Commit 2
- [ ] PRs Created Today

## 5️⃣ Notes & Observations
-  

---
\`\`\`dataview
table date, status
from "${baseFolder}"
where contains(tags, "daily-log")
sort date desc
limit 10
\`\`\`
`;

    const newFile = await app.vault.create(filePath, content);
    await app.workspace.getLeaf().openFile(newFile);
    tR = `✅ Created and opened new Daily Log for ${currentDate}.`;
}
%>