# chat.co.jp
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GitHub Realtime Chat</title>
    <style>
        body { font-family: sans-serif; background: #e6eaea; margin: 0; display: flex; flex-direction: column; height: 100vh; }
        #messages { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 10px; }
        .msg { background: white; padding: 8px 12px; border-radius: 10px; max-width: 80%; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .input-area { background: white; padding: 15px; display: flex; gap: 10px; border-top: 1px solid #ddd; }
        input { flex: 1; padding: 10px; border: 1px solid #ccc; border-radius: 5px; }
        button { padding: 10px 20px; background: #28a745; color: white; border: none; border-radius: 5px; cursor: pointer; }
    </style>
</head>
<body>

<div id="messages"></div>

<form class="input-area" id="chat-form">
    <input type="text" id="user-input" placeholder="名前" style="width: 20%;">
    <input type="text" id="msg-input" placeholder="メッセージ..." required>
    <button type="submit">送信</button>
</form>

<script type="module">
    // Firebase SDKのインポート
    import { initializeApp } from "https://gstatic.com";
    import { getDatabase, ref, push, onChildAdded, serverTimestamp } 
    from "https://gstatic.com";

    // --- ここに自分のFirebase設定を貼り付ける ---
    const firebaseConfig = {
        apiKey: "YOUR_API_KEY",
        authDomain: "YOUR_PROJECT_://firebaseapp.com",
        databaseURL: "https://YOUR_PROJECT_://firebaseio.com",
        projectId: "YOUR_PROJECT_ID",
        storageBucket: "YOUR_PROJECT_://appspot.com",
        messagingSenderId: "YOUR_ID",
        appId: "YOUR_APP_ID"
    };

    // Firebaseの初期化
    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const dbRef = ref(db, 'chat');

    const form = document.getElementById('chat-form');
    const msgInput = document.getElementById('msg-input');
    const userInput = document.getElementById('user-input');
    const msgContainer = document.getElementById('messages');

    // 送信処理
    form.addEventListener('submit', (e) => {
        e.preventDefault();
        const name = userInput.value || "名無し";
        const text = msgInput.value;
        push(dbRef, { name, text, time: serverTimestamp() });
        msgInput.value = "";
    });

    // 受信処理（データが追加されるたびに実行）
    onChildAdded(dbRef, (data) => {
        const v = data.val();
        const div = document.createElement('div');
        div.className = 'msg';
        div.innerHTML = `<strong>${v.name}</strong>: ${v.text}`;
        msgContainer.appendChild(div);
        msgContainer.scrollTop = msgContainer.scrollHeight;
    });
</script>
</body>
</html>
