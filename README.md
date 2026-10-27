<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>دردشة</title>
<style>
  :root{--bg:#eef2f1;--panel:#fff;--ink:#1c2b2a;--me:#1f7a6d;--me-ink:#fff;--them:#e3e9e8;--line:#d3dcdb}
  @media (prefers-color-scheme:dark){:root{--bg:#121a19;--panel:#1a2423;--ink:#e6efee;--me:#2a9d8c;--me-ink:#06201c;--them:#263230;--line:#2f3d3b}}
  *{box-sizing:border-box}
  html,body{height:100%;margin:0}
  body{font-family:system-ui,-apple-system,"Segoe UI",Tahoma,sans-serif;background:var(--bg);color:var(--ink);display:flex;justify-content:center}
  .app{width:100%;max-width:560px;height:100%;display:flex;flex-direction:column;background:var(--panel)}
  header{padding:14px 16px;border-bottom:1px solid var(--line);font-weight:600}
  #login{margin:auto;padding:24px;display:flex;flex-direction:column;gap:12px;width:100%}
  #login h1{font-size:1.3rem;margin:0 0 4px}
  input{font:inherit;color:inherit;background:var(--bg);border:1px solid var(--line);border-radius:10px;padding:12px}
  button{font:inherit;border:0;border-radius:10px;padding:12px 18px;background:var(--me);color:var(--me-ink);cursor:pointer;font-weight:600}
  #chat{display:none;flex:1;min-height:0;flex-direction:column}
  #msgs{flex:1;overflow-y:auto;padding:14px;display:flex;flex-direction:column;gap:8px}
  .m{max-width:80%;padding:9px 12px;border-radius:14px;background:var(--them);word-wrap:break-word;white-space:pre-wrap}
  .m.me{align-self:flex-start;background:var(--me);color:var(--me-ink)}
  .m small{display:block;font-size:.72rem;opacity:.7;margin-bottom:2px}
  form{display:flex;gap:8px;padding:10px;border-top:1px solid var(--line)}
  form input{flex:1}
  #err{color:#c0392b;font-size:.9rem;min-height:1.2em}
</style>
</head>
<body>
<div class="app">
  <div id="login">
    <h1>دردشة خاصة</h1>
    <input id="name" placeholder="اسمك" autocomplete="off">
    <input id="code" placeholder="الكود السري" autocomplete="off">
    <button id="enter">دخول</button>
    <div id="err"></div>
  </div>

  <div id="chat">
    <header id="title"></header>
    <div id="msgs"></div>
    <form id="form">
      <input id="text" placeholder="اكتب رسالة..." autocomplete="off">
      <button type="submit">إرسال</button>
    </form>
  </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getDatabase, ref, push, onChildAdded, query, limitToLast, serverTimestamp }
  from "https://www.gstatic.com/firebasejs/10.12.2/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyBM1JtcKNnwABsesVPZWtTi51OpJtQgG28",
  authDomain: "chat-ana-ou-hbiba-b0e80.firebaseapp.com",
  databaseURL: "https://chat-ana-ou-hbiba-b0e80-default-rtdb.firebaseio.com",
  projectId: "chat-ana-ou-hbiba-b0e80",
  appId: "1:720695295313:web:b86a66a806c7b0093f6770"
};

const SECRET = "AeTuOpp/48";
const $ = id => document.getElementById(id);
let db, name;

$("enter").onclick = () => {
  name = $("name").value.trim();
  const code = $("code").value.trim();
  if (!name) { $("err").textContent = "اكتب اسمك."; return; }
  if (code !== SECRET) { $("err").textContent = "الكود غير صحيح."; return; }
  try {
    db = getDatabase(initializeApp(firebaseConfig));
  } catch (e) { $("err").textContent = "خطأ في إعدادات Firebase."; return; }
  $("login").style.display = "none";
  $("chat").style.display = "flex";
  $("title").textContent = name;
  start();
};

function start(){
  const path = "rooms/main";
  const q = query(ref(db, path), limitToLast(200));
  onChildAdded(q, snap => {
    const d = snap.val();
    const div = document.createElement("div");
    div.className = "m" + (d.name === name ? " me" : "");
    const s = document.createElement("small");
    s.textContent = d.name;
    const t = document.createElement("span");
    t.text
