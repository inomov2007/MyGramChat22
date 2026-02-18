<html lang="tg">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Instagram Ultimate</title>
    
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    
    <style>
        :root { --blue: #0095f6; --bg: #fafafa; --border: #dbdbdb; --red: #ed4956; --gray: #8e8e8e; }
        
        /* Reset CSS: Барои он ки дар ҳама браузерҳо якхела намояд */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, system-ui, sans-serif; }
        
        body { background: var(--bg); display: flex; justify-content: center; height: 100vh; overflow: hidden; }
        .hidden { display: none !important; }

        .app-container { 
            width: 100%; 
            max-width: 450px; 
            height: 100vh; 
            background: white; 
            border-left: 1px solid var(--border); 
            border-right: 1px solid var(--border); 
            position: relative; 
            display: flex; 
            flex-direction: column; 
            overflow: hidden; 
        }
        
        header { 
            height: 60px; 
            border-bottom: 1px solid var(--border); 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            padding: 0 20px; 
            background: white; 
            flex-shrink: 0; 
            z-index: 10; 
        }
        .logo { font-size: 28px; font-weight: bold; font-style: italic; font-family: 'Arial', sans-serif; }

        .page { display: none; flex-direction: column; height: calc(100% - 115px); overflow-y: auto; background: white; flex: 1; }
        .active-page { display: flex; }

        /* POSTS */
        .post { border-bottom: 1px solid var(--border); padding-bottom: 10px; }
        .post-header { padding: 12px; display: flex; align-items: center; gap: 10px; font-weight: 600; cursor: pointer; }
        .avatar-sm { width: 32px; height: 32px; border-radius: 50%; object-fit: cover; background: #eee; border: 1px solid #eee; }
        .post-img { width: 100%; aspect-ratio: 1/1; object-fit: cover; cursor: pointer; }
        .post-btns { padding: 12px; display: flex; gap: 18px; font-size: 24px; }
        .liked { color: var(--red) !important; animation: scaleUp 0.3s ease; }
        @keyframes scaleUp { 0% { transform: scale(1.3); } 100% { transform: scale(1); } }
        
        /* NAVIGATION */
        .nav-bottom { height: 55px; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; background: white; }
        .nav-bottom i { font-size: 24px; cursor: pointer; color: #262626; }

        /* PROFILE */
        .profile-stats { display: flex; justify-content: space-around; flex: 1; text-align: center; }
        .profile-stats b { display: block; font-size: 16px; }
        .profile-stats span { font-size: 12px; color: var(--gray); }
        .grid-container { display: grid; grid-template-columns: repeat(3, 1fr); gap: 2px; margin-top: 20px; }
        .grid-item img { width: 100%; aspect-ratio: 1/1; object-fit: cover; }

        /* AUTH & INPUTS */
        .auth-input { width: 100%; padding: 12px; margin-bottom: 12px; border: 1px solid var(--border); border-radius: 8px; background: #fafafa; outline: none; }
        .btn-blue { width: 100%; padding: 10px; background: var(--blue); color: white; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; }
        
        /* CHAT */
        .chat-msg { margin: 5px 15px; padding: 8px 12px; border-radius: 18px; max-width: 75%; font-size: 14px; }
        .my-msg { align-self: flex-end; background: var(--blue); color: white; }
        .their-msg { align-self: flex-start; background: #efefef; color: black; }

        /* MODALS */
        .overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 1000; }
        .modal { display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: white; width: 90%; max-width: 400px; border-radius: 12px; padding: 20px; z-index: 1001; }
    </style>
</head>
<body>

<div id="authPage" style="height:100vh; display:flex; align-items:center; justify-content:center; width:100%;">
    <div style="width:320px; text-align:center; padding:20px; border:1px solid #dbdbdb; background:white;">
        <h1 class="logo">Instagram</h1><br>
        <input type="text" id="email" class="auth-input" placeholder="Email">
        <input type="password" id="password" class="auth-input" placeholder="Парол">
        <button class="btn-blue" onclick="handleAuth()">Ворид шудан</button>
    </div>
</div>

<div id="appPage" class="app-container hidden">
    <header>
        <div class="logo">Instagram</div>
        <i class="fa-regular fa-paper-plane" onclick="showPage('chatList')" style="font-size: 22px; cursor:pointer;"></i>
    </header>

    <div id="page-feed" class="page active-page"><div id="feed-list"></div></div>

    <div id="page-search" class="page" style="padding:15px;">
        <input type="text" id="searchInput" class="auth-input" placeholder="Ҷустуҷӯ..." oninput="searchUsers()">
        <div id="search-results"></div>
    </div>

    <div id="page-create" class="page" style="padding:20px;">
        <h3>Пости нав</h3><br>
        <input type="file" onchange="previewImg(this)"><br>
        <img id="pre" style="width:100%; display:none; margin:10px 0; border-radius:8px;">
        <textarea id="cap" class="auth-input" placeholder="Тавсифи пост..."></textarea>
        <button class="btn-blue" onclick="uploadPost()">Пост кардан</button>
    </div>

    <div id="page-chatList" class="page" style="padding:15px;">
        <b>Паёмҳо</b><hr style="margin:10px 0;">
        <div id="chat-users-list"></div>
    </div>

    <div id="page-chatRoom" class="page" style="padding:15px;">
        <div style="display:flex; gap:10px; align-items:center; border-bottom:1px solid #eee; padding-bottom:10px;">
            <i class="fa-solid fa-arrow-left" onclick="showPage('chatList')"></i>
            <b id="chatTitle">Корбар</b>
        </div>
        <div id="chatBox" style="flex:1; overflow-y:auto; display:flex; flex-direction:column; padding:15px 0;"></div>
        <div style="display:flex; gap:10px;">
            <input type="text" id="mInp" class="auth-input" placeholder="Паём..." style="margin:0;">
            <button onclick="sendMsg()" class="btn-blue" style="width:60px;">OK</button>
        </div>
    </div>

    <div id="page-otherProfile" class="page" style="padding:20px;">
        <i class="fa-solid fa-arrow-left" onclick="showPage('feed')" style="cursor:pointer;"></i><br><br>
        <div style="display:flex; align-items:center;">
            <img id="oAva" src="" class="avatar-sm" style="width:80px; height:80px;">
            <div class="profile-stats">
                <div><b id="oCp">0</b><span>Пост</span></div>
                <div><b id="oCf">0</b><span>Обуначӣ</span></div>
            </div>
        </div>
        <h3 id="oName" style="margin:15px 0;">Name</h3>
        <div style="display:flex; gap:10px;">
            <button id="fBtn" class="btn-blue" style="flex:1;">Follow</button>
            <button id="mBtn" class="btn-blue" style="flex:1; background:#eee; color:black;">Message</button>
        </div>
        <div id="oGrid" class="grid-container"></div>
    </div>

    <div id="page-profile" class="page" style="padding:20px;">
        <div style="display:flex; justify-content:space-between; align-items:center;">
            <b id="myUser" style="font-size:20px;">username</b>
            <i class="fa-solid fa-right-from-bracket" onclick="logOut()" style="color:red; cursor:pointer;"></i>
        </div>
        <div style="display:flex; align-items:center; margin-top:20px;">
            <img id="myAva" src="" class="avatar-sm" style="width:80px; height:80px;">
            <div class="profile-stats">
                <div><b id="myCp">0</b><span>Пост</span></div>
                <div><b id="myCf">0</b><span>Обуначӣ</span></div>
            </div>
        </div>
        <button class="btn-blue" style="background:#efefef; color:black; margin-top:15px;" onclick="openEdit()">Редактировать профиль</button>
        <div id="myGrid" class="grid-container"></div>
    </div>

    <nav class="nav-bottom">
        <i class="fa-solid fa-house" onclick="showPage('feed')"></i>
        <i class="fa-solid fa-magnifying-glass" onclick="showPage('search')"></i>
        <i class="fa-regular fa-square-plus" onclick="showPage('create')"></i>
        <i class="fa-regular fa-user" onclick="showPage('profile')"></i>
    </nav>
</div>

<div class="overlay" id="commOverlay" onclick="closeComm()"></div>
<div class="modal" id="commModal">
    <b>Шарҳҳо</b><hr style="margin:10px 0;">
    <div id="commList" style="max-height:250px; overflow-y:auto;"></div><br>
    <div style="display:flex; gap:10px;">
        <input type="text" id="cInp" class="auth-input" placeholder="Шарҳ..." style="margin:0;">
        <button onclick="addComment()" class="btn-blue" style="width:60px;">OK</button>
    </div>
</div>

<div class="overlay" id="editOverlay" onclick="closeEdit()"></div>
<div class="modal" id="editModal">
    <h3>Таҳрири профил</h3><br>
    <input type="text" id="editName" class="auth-input" placeholder="Номи нав">
    <button class="btn-blue" onclick="saveEdit()">Захира кардан</button>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
    import { getFirestore, collection, addDoc, query, orderBy, onSnapshot, doc, updateDoc, setDoc, getDoc, arrayUnion, arrayRemove, where } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

    const cfg = { apiKey: "AIzaSyDc3JvzuiWLbneEUBKzsrEhB5fOCnOFjhc", authDomain: "mygram-74b48.firebaseapp.com", projectId: "mygram-74b48", storageBucket: "mygram-74b48.firebasestorage.app", messagingSenderId: "262338393496", appId: "1:262338393496:web:469efc8a2bba91cd9127ed" };
    const app = initializeApp(cfg); const auth = getAuth(app); const db = getFirestore(app);
    let myData = {}, curPostId = "", curChatId = "", postImg = "";

    window.handleAuth = () => { const e=document.getElementById('email').value, p=document.getElementById('password').value; if(e && p) signInWithEmailAndPassword(auth,e,p).catch(()=>createUserWithEmailAndPassword(auth,e,p)); };
    window.logOut = () => signOut(auth);

    onAuthStateChanged(auth, async (u) => {
        if(u) {
            const d = await getDoc(doc(db, "users", u.uid));
            myData = d.exists() ? d.data() : { name: u.email.split('@')[0], pic: 'https://ui-avatars.com/api/?name='+u.email[0], followers: [] };
            if(!d.exists()) await setDoc(doc(db, "users", u.uid), myData);
            document.getElementById('authPage').classList.add('hidden');
            document.getElementById('appPage').classList.remove('hidden');
            syncData();
        } else { document.getElementById('authPage').classList.remove('hidden'); document.getElementById('appPage').classList.add('hidden'); }
    });

    window.searchUsers = () => {
        const val = document.getElementById('searchInput').value.toLowerCase();
        onSnapshot(collection(db, "users"), snap => {
            let res = document.getElementById('search-results'); res.innerHTML = "";
            snap.forEach(u => {
                if(u.data().name.toLowerCase().includes(val) && u.id !== auth.currentUser.uid) {
                    res.innerHTML += `<div onclick="openProfile('${u.id}')" style="display:flex; align-items:center; gap:10px; padding:10px; border-bottom:1px solid #eee; cursor:pointer;">
                        <img src="${u.data().pic}" class="avatar-sm"> <b>${u.data().name}</b>
                    </div>`;
                }
            });
        });
    };

    window.openProfile = async (uid) => {
        if(uid === auth.currentUser.uid) return showPage('profile');
        const ud = (await getDoc(doc(db, "users", uid))).data();
        document.getElementById('oName').innerText = ud.name;
        document.getElementById('oAva').src = ud.pic;
        document.getElementById('oCf').innerText = ud.followers?.length || 0;
        const isF = ud.followers?.includes(auth.currentUser.uid);
        const f = document.getElementById('fBtn');
        f.innerText = isF ? "Unfollow" : "Follow";
        f.style.background = isF ? "#eee" : "#0095f6"; f.style.color = isF ? "black" : "white";
        f.onclick = async () => { await updateDoc(doc(db,"users",uid), {followers: isF ? arrayRemove(auth.currentUser.uid) : arrayUnion(auth.currentUser.uid)}); openProfile(uid); };
        document.getElementById('mBtn').onclick = () => openChat(uid, ud.name);
        onSnapshot(query(collection(db,"posts"), where("uid","==",uid)), s => {
            let g = document.getElementById('oGrid'); g.innerHTML = "";
            document.getElementById('oCp').innerText = s.size;
            s.forEach(p => g.innerHTML += `<div class="grid-item"><img src="${p.data().img}"></div>`);
        });
        showPage('otherProfile');
    };

    window.openChat = (uid, name) => {
        curChatId = uid; document.getElementById('chatTitle').innerText = name; showPage('chatRoom');
        onSnapshot(query(collection(db,"chats"), orderBy("t","asc")), s => {
            let b = document.getElementById('chatBox'); b.innerHTML = "";
            s.forEach(m => {
                const d = m.data();
                if((d.f==auth.currentUser.uid && d.t==uid) || (d.f==uid && d.t==auth.currentUser.uid)) {
                    b.innerHTML += `<div class="chat-msg ${d.f==auth.currentUser.uid?'my-msg':'their-msg'}">${d.m}</div>`;
                }
            });
            b.scrollTop = b.scrollHeight;
        });
    };
    window.sendMsg = async () => { const i=document.getElementById('mInp'); if(i.value) { await addDoc(collection(db,"chats"), {f:auth.currentUser.uid, t:curChatId, m:i.value, t:Date.now()}); i.value=""; } };

    window.handleLike = async (id) => {
        const r = doc(db,"posts",id); const s = await getDoc(r);
        const l = s.data().likes || [];
        await updateDoc(r, {likes: l.includes(auth.currentUser.uid) ? arrayRemove(auth.currentUser.uid) : arrayUnion(auth.currentUser.uid)});
    };

    window.openComm = (id) => {
        curPostId = id; document.getElementById('commOverlay').style.display='block'; document.getElementById('commModal').style.display='block';
        onSnapshot(doc(db,"posts",id), s => {
            document.getElementById('commList').innerHTML = (s.data().comments || []).map(c => `<div onclick="closeComm();openProfile('${c.uid}')" style="margin-bottom:10px; cursor:pointer;"><b>${c.u}:</b> ${c.t}</div>`).join('');
        });
    };
    window.closeComm = () => { document.getElementById('commOverlay').style.display='none'; document.getElementById('commModal').style.display='none'; };
    window.addComment = async () => {
        const t = document.getElementById('cInp').value;
        if(t) await updateDoc(doc(db,"posts",curPostId), {comments: arrayUnion({u:myData.name, t, uid:auth.currentUser.uid})});
        document.getElementById('cInp').value = "";
    };

    window.openEdit = () => { document.getElementById('editOverlay').style.display='block'; document.getElementById('editModal').style.display='block'; };
    window.closeEdit = () => { document.getElementById('editOverlay').style.display='none'; document.getElementById('editModal').style.display='none'; };
    window.saveEdit = async () => {
        const n = document.getElementById('editName').value;
        if(n) { await updateDoc(doc(db,"users",auth.currentUser.uid), {name: n}); myData.name = n; }
        closeEdit();
    };

    function syncData() {
        onSnapshot(query(collection(db,"posts"), orderBy("time","desc")), (snap) => {
            const fl = document.getElementById('feed-list'), gl = document.getElementById('myGrid');
            fl.innerHTML = ""; gl.innerHTML = ""; let pc = 0;
            snap.forEach(p => {
                const d = p.data(), id = p.id, liked = d.likes?.includes(auth.currentUser.uid);
                fl.innerHTML += `<div class="post">
                    <div class="post-header" onclick="openProfile('${d.uid}')"><img src="https://ui-avatars.com/api/?name=${d.user}" class="avatar-sm"> ${d.user}</div>
                    <img src="${d.img}" class="post-img" ondblclick="handleLike('${id}')">
                    <div class="post-btns"><i class="fa-${liked?'solid liked':'regular'} fa-heart" onclick="handleLike('${id}')"></i><i class="fa-regular fa-comment" onclick="openComm('${id}')"></i></div>
                    <div style="padding:0 15px; font-size:14px;"><b>${d.likes?.length || 0} лайк</b><br><b>${d.user}</b> ${d.cap}</div>
                </div>`;
                if(d.uid === auth.currentUser.uid) { pc++; gl.innerHTML += `<div class="grid-item"><img src="${d.img}"></div>`; }
            });
            document.getElementById('myUser').innerText = myData.name;
            document.getElementById('myAva').src = myData.pic;
            document.getElementById('myCp').innerText = pc;
            document.getElementById('myCf').innerText = myData.followers?.length || 0;
        });
        
        onSnapshot(collection(db, "users"), snap => {
            let cl = document.getElementById('chat-users-list'); cl.innerHTML = "";
            snap.forEach(u => {
                if(u.id !== auth.currentUser.uid) {
                    cl.innerHTML += `<div onclick="openChat('${u.id}', '${u.data().name}')" style="display:flex; align-items:center; gap:10px; padding:10px; cursor:pointer;">
                        <img src="${u.data().pic}" class="avatar-sm"> <b>${u.data().name}</b>
                    </div>`;
                }
            });
        });
    }

    window.showPage = (id) => { document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page')); document.getElementById('page-'+id).classList.add('active-page'); };
    window.previewImg = (i) => { const r = new FileReader(); r.onload = e => { postImg = e.target.result; document.getElementById('pre').src = e.target.result; document.getElementById('pre').style.display='block'; }; r.readAsDataURL(i.files[0]); };
    window.uploadPost = async () => { 
        if(!postImg) return;
        await addDoc(collection(db,"posts"), {img:postImg, cap:document.getElementById('cap').value, uid:auth.currentUser.uid, user:myData.name, time:Date.now(), likes:[], comments:[]});
        document.getElementById('pre').style.display='none'; document.getElementById('cap').value="";
        showPage('feed'); 
    };
</script>
</body>
</html>
