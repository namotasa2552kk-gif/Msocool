# Msocool
เป็น Ai ต้นแบบ
<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<title>MSO AI</title>

<style>
body{
  background:#000;
  color:#0f6;
  font-family:monospace;
  padding:20px;
}
h2{color:#6ff}
#chat{
  border:1px solid #0f6;
  height:420px;
  overflow:auto;
  padding:10px;
  margin-bottom:10px;
}
input{
  width:100%;
  background:#000;
  color:#0f6;
  border:1px solid #0f6;
  padding:10px;
}
.ai{color:#6ff}
.sys{color:#f66}
.search{color:#ff0}
button{
  background:#000;
  color:#0f6;
  border:1px solid #0f6;
  padding:6px 10px;
  margin-bottom:8px;
  cursor:pointer;
}
</style>
</head>

<body>

<h2>☠ MSO AI — MEMORY + VOICE</h2>

<button onclick="toggleVoice()">🔊 เสียง AI: <span id="vstat">ON</span></button>

<div id="chat"></div>
<input id="inp" placeholder="พิมพ์คุย / ค้นหา แล้วกด Enter">

<script>
const chat = document.getElementById("chat");
const inp = document.getElementById("inp");
const vstat = document.getElementById("vstat");

/* ===== MEMORY SYSTEM ===== */
let memory = JSON.parse(localStorage.getItem("mso_ai_memory")) || {
  name:null,
  history:[]
};

function saveMemory(){
  localStorage.setItem("mso_ai_memory", JSON.stringify(memory));
}

/* ===== VOICE SYSTEM ===== */
let voiceEnabled = true;

function speak(text){
  if(!voiceEnabled) return;
  const u = new SpeechSynthesisUtterance(text);
  u.lang = "th-TH";
  u.rate = 1;
  speechSynthesis.speak(u);
}

function toggleVoice(){
  voiceEnabled = !voiceEnabled;
  vstat.textContent = voiceEnabled ? "ON" : "OFF";
}

/* ===== UI ===== */
function add(text, cls=""){
  const d=document.createElement("div");
  d.className=cls;
  d.textContent=text;
  chat.appendChild(d);
  chat.scrollTop=chat.scrollHeight;
}

add("SYSTEM: MSO AI Online", "sys");
if(memory.name){
  add("SYSTEM: ยินดีต้อนรับกลับ "+memory.name, "sys");
}

/* ===== SEARCH BASE ===== */
const knowledgeBase = {
  "html":"HTML คือภาษาสร้างโครงสร้างเว็บไซต์",
  "css":"CSS ใช้ตกแต่งหน้าตาเว็บไซต์",
  "javascript":"JavaScript ทำให้เว็บโต้ตอบได้",
  "ai":"AI คือระบบที่เลียนแบบความคิดมนุษย์",
  "chatgpt":"ChatGPT คือโมเดลภาษา AI",
  "hacker":"Hacker คือผู้เชี่ยวชาญระบบ"
};

function aiSearch(q){
  let r=[];
  for(let k in knowledgeBase){
    if(q.includes(k)) r.push("• "+knowledgeBase[k]);
  }
  return r.length ? r.join("\n") : null;
}

/* ===== AI THINK ===== */
function aiThink(input){
  const t = input.toLowerCase();
  memory.history.push(input);
  saveMemory();

  if(t.includes("ชื่อฉันคือ")){
    memory.name = input.split("คือ")[1].trim();
    saveMemory();
    return "รับทราบ ฉันจะจำชื่อนายว่า "+memory.name;
  }

  if(t.includes("จำอะไรได้บ้าง")){
    return "ฉันจำชื่อ และบทสนทนาได้ "+memory.history.length+" ข้อความ";
  }

  if(
    t.includes("ค้นหา") ||
    t.includes("คืออะไร") ||
    t.includes("หมายถึง")
  ){
    add("[ SEARCH MODE ] กำลังค้นข้อมูล...", "search");
    return aiSearch(t) || "ไม่พบข้อมูลตรง แต่ฉันวิเคราะห์ได้";
  }

  if(t.includes("ปิดเสียง"))
    return "ถ้าต้องการปิดเสียง กดปุ่มด้านบนได้เลย";

  return "ฉันวิเคราะห์แล้ว ประเด็นนี้น่าสนใจ เล่าต่อได้";
}

/* ===== SEND ===== */
function send(){
  const text = inp.value.trim();
  if(!text) return;
  inp.value="";
  add("คุณ: "+text);

  setTimeout(()=>{
    const reply = aiThink(text);
    add("AI: "+reply,"ai");
    speak(reply);
  },500);
}

inp.onkeydown = e=> e.key==="Enter" && send();
</script>

</body>
</html>
