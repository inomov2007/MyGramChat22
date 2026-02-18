<html lang="tg">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Instagram Pro Max Final</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root { --blue: #0095f6; --bg: #fafafa; --border: #dbdbdb; --red: #ed4956; --gray: #8e8e8e; }
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, system-ui, sans-serif; }
        body { background: var(--bg); display: flex; justify-content: center; height: 100vh; overflow: hidden; }
        .hidden { display: none !important; }

        .app-container { width: 100%; max-width: 450px; height: 100vh; background: white; border: 1px solid var(--border); position: relative; display: flex; flex-direction: column; overflow: hidden; }
        
        header { height: 60px; border-bottom: 1px solid var(--border); display: flex; justify-content: center; align-items: center; background: white; flex-shrink: 0; z-index: 10; }
        .logo { font-size: 28px; font-weight: bold; font-style: italic; }

        .page { display: none; flex-direction: column; height: calc(100% - 115px); overflow-y: auto; background: white; flex: 1; }
        .active-page { display: flex; }

        /* POST DESIGN */
        .post { border-bottom: 1px solid var(--border); padding-bottom: 10px; margin-bottom: 5px; }
        .post-header { padding: 12px; display: flex; align-items: center; gap: 10px; font-weight: 600; font-size: 14px; }
        .avatar-sm { width: 32px; height: 32px; border-radius: 50%; object-fit: cover; background: #eee; border: 1px solid #ddd; }
        .post-img { width: 100%; aspect-ratio: 1/1; object-fit: cover; background: #f0f0f0; cursor: pointer; }
        .post-btns { padding: 12px; display: flex; gap: 18px; font-size: 24px; }
        .liked { color: var(--red) !important; animation: scaleUp 0.3s ease; }
        @keyframes scaleUp { 0% { transform: scale(1); } 50% { transform: scale(1.3); } 100% { transform: scale(1); } }
        .post-info { padding: 0 12px; font-size: 14px; line-height: 1.4; }

        /* NAVIGATION */
        .nav-bottom { height: 55px; border-top: 1px solid var(--border); display: flex; justify-content: space-around; align-items: center; background: white; flex-shrink: 0; }
        .nav-bottom i { font-size: 24px; cursor: pointer; color: #262626; }

        /* PROFILE */
        .profile-stats { display: flex; justify-content: space-around; flex: 1; text-align: center; margin-left: 20px; }
        .profile-stats b { display: block; font-size: 16px; }
        .profile-stats span { font-size: 12px; color: var(--gray); }
        .grid-container { display: grid; grid-template-columns: repeat(3, 1fr); gap: 2px; margin-top: 20px; }
        .grid-item { aspect-ratio: 1/1; overflow: hidden; }
        .grid-item img { width: 100%; height: 100%; object-fit: cover; }

        /* BUTTONS & INPUTS */
        .auth-input { width: 100%; padding: 12px; margin-bottom: 12px; border: 1px solid var(--border); border-radius: 8px; background: #fafafa; outline: none; }
        .btn-blue { width: 100%; padding: 10px; background: var(--blue); color: white; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; }

        /* MODALS */
        .overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 1000; }
        .modal { display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: white; width: 90%; max-height: 80vh; border-radius: 12px; padding: 20px; z-index: 1001; overflow-y: auto; }
    </style>
</head>
<body>

<div id="authPage" style="height:100vh; display:flex; align-items:center; justify-content:center; width:100%;">
    <div style="width:320px; text-align:center; padding:20px; border:1px solid #dbdbdb; background:white;">
        <h1 class="logo" style="margin-bottom:30px;">Instagram</h1>
        <input type="text" id="email" class="auth-input" placeholder="Email">
        <input type="password" id="password" class="auth-input" placeholder="Парол">
        <button class="btn-blue" onclick="handleAuth()">Ворид шудан</button>
        <p style="margin-top:20px; font-size:14px;">Аккаунт надоред? <span id="authToggle" style="color:var(--blue); cursor:pointer; font-weight:600;" onclick="toggleAuth()">Сабти ном</span></p>
    </div>
</div>

<div id="appPage" class="app-container hidden">
    <header><div class="logo">Instagram</div></header>

    <div id="page-feed" class="page active-page"><div id="feed-list"></div></div>
    
    <div id="page-search" class="page">
        <div style="padding:15px;"><input type="text" class="auth-input" placeholder="Ҷустуҷӯ..."></div>
        <div id="search-results"></div>
    </div>

    <div id="page-create" class="page" style="padding:20px;">
        <h3>Пости нав</h3>
        <input type="file" onchange="previewImg(this, 'pre')" style="margin:20px 0;">
        <img id="pre" style="width:100%; border-radius:8px; display:none; margin-bottom:10px;">
        <textarea id="cap" class="auth-input" placeholder="Тавсифи пост..."></textarea>
        <button class="btn-blue" onclick="uploadPost()">Пост кардан</button>
    </div>

    <div id="page-chatList" class="page"><div style="padding:15px;"><b>Паёмҳо</b></div><div id="chat-users"></div></div>

    <div id="page-profile" class="page">
        <div style="padding:20px;">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:20px;">
                <b id="profUser" style="font-size:18px;">username</b>
                <i class="fa-solid fa-right-from-bracket" onclick="logOut()" style="color:red; cursor:pointer;"></i>
            </div>
            <div style="display:flex; align-items:center;">
                <img id="myAvatar" src="" class="avatar-sm" style="width:80px; height:80px;">
                <div class="profile-stats">
                    <div><b id="count-p">0</b><span>Пост</span></div>
                    <div><b id="count-f">0</b><span>Обуначӣ</span></div>
                    <div><b id="count-ing">0</b><span>Обуна</span></div>
                </div>
            </div>
            <div style="margin-top:15px;"><b id="myName">Name</b></div>
            <button class="btn-blue" style="background:#efefef; color:black; margin-top:15px;" onclick="openEdit()">Редактировать профиль</button>
            <div id="myGrid" class="grid-container"></div>
        </div>
    </div>

    <nav class="nav-bottom">
        <i class="fa-solid fa-house" onclick="showPage('feed')"></i>
        <i class="fa-solid fa-magnifying-glass" onclick="showPage('search')"></i>
        <i class="fa-regular fa-square-plus" onclick="showPage('create')"></i>
        <i class="fa-regular fa-paper-plane" onclick="showPage('chatList')"></i>
        <i class="fa-regular fa-user" onclick="showPage('profile')"></i>
    </nav>
</div>

<div class="overlay" id="commOverlay" onclick="closeComm()"></div>
<div class="modal" id="commModal">
    <b>Шарҳҳо</b><hr style="margin:10px 0;">
    <div id="commList" style="max-height:300px; overflow-y:auto; margin-bottom:15px;"></div>
    <div style="display:flex; gap:10px;">
        <input type="text" id="commInput" class="auth-input" placeholder="Шарҳ..." style="margin:0;">
        <button onclick="addComment()" class="btn-blue" style="width:60px;">Ok</button>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
    import { getFirestore, collection, addDoc, query, orderBy, onSnapshot, doc, updateDoc, setDoc, getDoc, arrayUnion, arrayRemove, increment, deleteDoc } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

    const firebaseConfig = {
        apiKey: "AIzaSyDc3JvzuiWLbneEUBKzsrEhB5fOCnOFjhc",
        authDomain: "mygram-74b48.firebaseapp.com",
        projectId: "mygram-74b48",
        storageBucket: "mygram-74b48.firebasestorage.app",
        messagingSenderId: "262338393496",
        appId: "1:262338393496:web:469efc8a2bba91cd9127ed"
    };

    const app = initializeApp(firebaseConfig);
    const auth = getAuth(app);
    const db = getFirestore(app);

    let isReg = false, myData = {}, tempImg = "", activePostId = "";

    window.toggleAuth = () => { isReg = !isReg; document.querySelector('.btn-blue').innerText = isReg ? "Сабти ном" : "Ворид шудан"; };
    window.handleAuth = () => {
        const e = document.getElementById('email').value, p = document.getElementById('password').value;
        isReg ? createUserWithEmailAndPassword(auth, e, p) : signInWithEmailAndPassword(auth, e, p);
    };
    window.logOut = () => signOut(auth);

    onAuthStateChanged(auth, async (u) => {
        if(u) {
            document.getElementById('authPage').classList.add('hidden');
            document.getElementById('appPage').classList.remove('hidden');
            const d = await getDoc(doc(db, "users", u.uid));
            if(!d.exists()) {
                myData = { name: u.email.split('@')[0], pic: 'https://ui-avatars.com/api/?name=U', followers: [], following: [] };
                await setDoc(doc(db, "users", u.uid), myData);
            } else { myData = d.data(); }
            renderProfile();
            syncData();
        } else {
            document.getElementById('authPage').classList.remove('hidden');
            document.getElementById('appPage').classList.add('hidden');
        }
    });

    // ТУГМАИ ЛАЙК (ИД ВА ҲОЛАТРО МЕСАНҶАД)
    window.handleLike = async (postId) => {
        const myUid = auth.currentUser.uid;
        const postRef = doc(db, "posts", postId);
        const postSnap = await getDoc(postRef);
        
        if (postSnap.exists()) {
            const currentLikes = postSnap.data().likes || [];
            if (currentLikes.includes(myUid)) {
                await updateDoc(postRef, { likes: arrayRemove(myUid) });
            } else {
                await updateDoc(postRef, { likes: arrayUnion(myUid) });
            }
        }
    };

    window.openComm = (id) => {
        activePostId = id;
        document.getElementById('commOverlay').style.display='block';
        document.getElementById('commModal').style.display='block';
        onSnapshot(doc(db, "posts", id), (s) => {
            if(s.exists()) {
                document.getElementById('commList').innerHTML = (s.data().comments || []).map(c => `<div><b>${c.u}:</b> ${c.t}</div>`).join('');
            }
        });
    };
    window.closeComm = () => { document.getElementById('commOverlay').style.display='none'; document.getElementById('commModal').style.display='none'; };
    window.addComment = async () => {
        const t = document.getElementById('commInput').value;
        if(t) await updateDoc(doc(db, "posts", activePostId), { comments: arrayUnion({u: myData.name, t: t}) });
        document.getElementById('commInput').value = "";
    };

    function syncData() {
        onSnapshot(query(collection(db, "posts"), orderBy("time", "desc")), (snap) => {
            const fl = document.getElementById('feed-list'), gl = document.getElementById('myGrid');
            fl.innerHTML = ""; gl.innerHTML = ""; let pc = 0;
            snap.forEach(p => {
                const d = p.data(), id = p.id;
                const liked = d.likes?.includes(auth.currentUser.uid);
                fl.innerHTML += `
                <div class="post" onmouseenter="updateDoc(doc(db, 'posts', '${id}'), {views: increment(1)})">
                    <div class="post-header"><img src="https://ui-avatars.com/api/?name=${d.user}" class="avatar-sm"> ${d.user}</div>
                    <img src="${d.img}" class="post-img" ondblclick="handleLike('${id}')">
                    <div class="post-btns">
                        <i class="fa-${liked?'solid liked':'regular'} fa-heart" onclick="handleLike('${id}')"></i>
                        <i class="fa-regular fa-comment" onclick="openComm('${id}')"></i>
                    </div>
                    <div class="post-info">
                        <b>${d.likes?.length || 0} лайк</b> | <b>${d.views || 0} просмотр</b><br>
                        <b>${d.user}</b> ${d.cap}
                    </div>
                </div>`;
                if(d.uid === auth.currentUser.uid) {
                    pc++;
                    gl.innerHTML += `<div class="grid-item" onclick="openDet('${id}', '${d.img}')"><img src="${d.img}"></div>`;
                }
            });
            document.getElementById('count-p').innerText = pc;
        });
    }

    // ДИГАР ФУНКСИЯҲО...
    window.previewImg = (i, t) => {
        const r = new FileReader();
        r.onload = e => { tempImg = e.target.result; const el = document.getElementById(t); el.src = e.target.result; el.style.display='block'; };
        r.readAsDataURL(i.files[0]);
    };

    window.uploadPost = async () => {
        if(!tempImg) return;
        await addDoc(collection(db, "posts"), { img: tempImg, cap: document.getElementById('cap').value, uid: auth.currentUser.uid, user: myData.name, time: Date.now(), likes: [], comments: [], views: 0 });
        tempImg = ""; document.getElementById('pre').style.display='none'; showPage('feed');
    };

    window.showPage = (id) => {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
        document.getElementById('page-' + id).classList.add('active-page');
    };

    function renderProfile() {
        document.getElementById('profUser').innerText = myData.name;
        document.getElementById('myName').innerText = myData.name;
        document.getElementById('myAvatar').src = myData.pic;
        document.getElementById('count-f').innerText = myData.followers?.length || 0;
        document.getElementById('count-ing').innerText = myData.following?.length || 0;
    }
</script>
</body>
</html>
