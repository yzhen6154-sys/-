# -
甘肃省考备考助手

<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>甘肃省考备考助手</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#f4f6fa;color:#243047;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif}
header{background:#244c86;color:white;padding:25px 18px}
header h1{margin:0 0 8px;font-size:24px}
header p{margin:0;color:#dce8f8;font-size:14px}
main{max-width:850px;margin:20px auto;padding:0 14px}
.card{background:white;padding:18px;border-radius:14px;margin-bottom:15px;box-shadow:0 3px 12px #152b4910}
h2{font-size:18px;margin:0 0 14px}
.stats{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
.stat{background:#edf4ff;padding:14px 8px;border-radius:10px;text-align:center}
.stat strong{display:block;font-size:23px;color:#24569a}
.stat span{font-size:12px;color:#59677c}
input,select,textarea,button{font:inherit}
input,select,textarea{width:100%;padding:11px;border:1px solid #d7deea;border-radius:8px;margin:5px 0 10px;background:white}
textarea{min-height:75px;resize:vertical}
button{border:0;border-radius:8px;padding:11px 15px;background:#285a9f;color:white;cursor:pointer}
button.secondary{background:#e9eef6;color:#29415f}
button.danger{background:#fff0f0;color:#a42e2e}
.task{display:flex;gap:10px;align-items:flex-start;padding:12px 0;border-bottom:1px solid #edf0f5}
.task input{width:auto;margin-top:5px}
.task label{flex:1;line-height:1.6}
.task.done label{text-decoration:line-through;color:#8b95a5}
.small{font-size:13px;color:#778399}
.item{padding:12px 0;border-bottom:1px solid #edf0f5;line-height:1.7;overflow-wrap:anywhere}
.item button{margin:6px 5px 0 0}
footer{text-align:center;padding:15px;color:#8a94a5;font-size:12px}
</style>
</head>
<body>
<header>
<h1>甘肃省考备考助手</h1>
<p>从今天开始，一点一点积累。坚持比完美更重要。</p>
</header>
<main>
<section class="card">
<h2>📊 今日备考概况</h2>
<div class="stats">
<div class="stat"><strong id="doneCount">0/4</strong><span>今日任务</span></div>
<div class="stat"><strong id="minutes">0</strong><span>学习分钟</span></div>
<div class="stat"><strong id="wrongCount">0</strong><span>错题数量</span></div>
</div>
<p class="small" id="today"></p>
</section>

<section class="card">
<h2>📅 今日学习计划</h2>
<div id="tasks"></div>
<p class="small">可以勾选已完成的任务。你也可以在下方添加自己的任务。</p>
<input id="taskInput" placeholder="例如：完成资料分析20道题">
<button onclick="addTask()">添加学习任务</button>
</section>

<section class="card">
<h2>⏱️ 学习时长记录</h2>
<label for="subject">学习科目</label>
<select id="subject">
<option>行测—言语理解</option>
<option>行测—判断推理</option>
<option>行测—资料分析</option>
<option>行测—数量关系</option>
<option>行测—常识判断</option>
<option>申论</option>
<option>复习错题</option>
</select>
<label for="duration">本次学习分钟数</label>
<input id="duration" type="number" min="1" placeholder="例如：45">
<button onclick="addStudy()">记录本次学习</button>
<div id="studyList"></div>
</section>

<section class="card">
<h2>📝 我的错题本</h2>
<label for="question">题目或知识点</label>
<textarea id="question" placeholder="写下做错的题目或知识点"></textarea>
<label for="reason">错误原因</label>
<select id="reason">
<option>知识点不会</option>
<option>审题不仔细</option>
<option>计算错误</option>
<option>解题方法不熟</option>
<option>时间不够</option>
<option>其他原因</option>
</select>
<label for="solution">正确思路 / 复习提醒</label>
<textarea id="solution" placeholder="下次遇到类似题目，我应该怎么做？"></textarea>
<button onclick="addWrong()">保存错题</button>
<div id="wrongList"></div>
</section>

<section class="card">
<h2>📈 备考小目标</h2>
<p>每天完成一点，比偶尔学很久更容易坚持。</p>
<ul>
<li>行测：先打好基础，再逐步提高刷题速度。</li>
<li>申论：练习概括、归纳和规范表达。</li>
<li>每周：回顾错题，调整下一周计划。</li>
</ul>
<button class="secondary" onclick="exportData()">导出备考记录备份</button>
<button class="danger" onclick="clearData()">清空本机记录</button>
<p class="small">记录默认保存在当前浏览器中。清除浏览器数据可能导致记录丢失，请定期备份。</p>
</section>
</main>
<footer>甘肃省考备考助手 · 个人学习工具</footer>
<script>
const KEY="gansuExamAssistantV1";
const defaultTasks=[
{id:1,text:"行测基础学习30分钟",done:false},
{id:2,text:"行测专项练习20道",done:false},
{id:3,text:"申论学习或练习30分钟",done:false},
{id:4,text:"复盘今天的错题",done:false}
];
let data;
try{data=JSON.parse(localStorage.getItem(KEY))||{};}catch(e){data={};}
if(!Array.isArray(data.tasks))data.tasks=defaultTasks;
if(!Array.isArray(data.studies))data.studies=[];
if(!Array.isArray(data.wrongs))data.wrongs=[];
if(!data.days)data.days={};
function todayKey(){const d=new Date();return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");}
function save(){localStorage.setItem(KEY,JSON.stringify(data));render();}
function render(){
document.getElementById("today").textContent="今天是 "+todayKey()+"。完成适合自己的学习节奏即可。";
document.getElementById("tasks").innerHTML=data.tasks.map(t=>`<div class="task ${t.done?'done':''}"><input type="checkbox" ${t.done?'checked':''} onchange="toggleTask(${t.id},this.checked)"><label>${esc(t.text)}</label><button class="danger" onclick="deleteTask(${t.id})">删除</button></div>`).join("");
const studies=data.studies.filter(s=>s.date===todayKey());
document.getElementById("doneCount").textContent=data.tasks.filter(t=>t.done).length+"/"+data.tasks.length;
document.getElementById("minutes").textContent=studies.reduce((n,s)=>n+s.minutes,0);
document.getElementById("wrongCount").textContent=data.wrongs.length;
document.getElementById("studyList").innerHTML=studies.length?studies.slice().reverse().map(s=>`<div class="item">${esc(s.subject)}：${s.minutes} 分钟 <span class="small">${s.date}</span></div>`).join(""):'<p class="small">今天还没有学习时长记录。</p>';
document.getElementById("wrongList").innerHTML=data.wrongs.length?data.wrongs.slice().reverse().map(w=>`<div class="item"><strong>${esc(w.question)}</strong><br>原因：${esc(w.reason)}<br>思路：${esc(w.solution||"暂未填写")}<br><span class="small">${w.date}</span><br><button class="secondary" onclick="reviewWrong(${w.id})">标记已复习</button><button class="danger" onclick="deleteWrong(${w.id})">删除</button>${w.reviewed?'<span class="small"> · 已复习</span>':''}</div>`).join(""):'<p class="small">还没有错题，做题时可以随时添加。</p>';
}
function esc(s){return String(s??"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));}
function toggleTask(id,done){data.tasks=data.tasks.map(t=>t.id===id?{...t,done}:t);save();}
function addTask(){const el=document.getElementById("taskInput");const text=el.value.trim();if(!text)return alert("请先输入任务内容");data.tasks.push({id:Date.now(),text,done:false});el.value="";save();}
function deleteTask(id){data.tasks=data.tasks.filter(t=>t.id!==id);save();}
function addStudy(){const minutes=Number(document.getElementById("duration").value);if(!Number.isFinite(minutes)||minutes<1)return alert("请输入有效的学习分钟数");data.studies.push({subject:document.getElementById("subject").value,minutes:Math.floor(minutes),date:todayKey()});document.getElementById("duration").value="";save();}
function addWrong(){const question=document.getElementById("question").value.trim();if(!question)return alert("请先填写题目或知识点");data.wrongs.push({id:Date.now(),question,reason:document.getElementById("reason").value,solution:document.getElementById("solution").value.trim(),date:todayKey(),reviewed:false});document.getElementById("question").value="";document.getElementById("solution").value="";save();}
function reviewWrong(id){data.wrongs=data.wrongs.map(w=>w.id===id?{...w,reviewed:true}:w);save();}
function deleteWrong(id){if(confirm("确定删除这条错题吗？")){data.wrongs=data.wrongs.filter(w=>w.id!==id);save();}}
function exportData(){const blob=new Blob(["\uFEFF"+JSON.stringify(data,null,2)],{type:"application/json;charset=utf-8"});const url=URL.createObjectURL(blob);const a=document.createElement("a");a.href=url;a.download="甘肃省考备考记录-"+todayKey()+".json";a.click();URL.revokeObjectURL(url);}
function clearData(){if(confirm("确定清空当前浏览器保存的全部备考记录吗？建议先导出备份。")){localStorage.removeItem(KEY);location.reload();}}
render();
</script>
</body>
</html>
