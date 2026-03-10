<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>Mini Craft - Enhanced</title>
<style>
body{margin:0;overflow:hidden;background:#87ceeb;font-family:sans-serif;}
#crosshair{position:fixed;left:50%;top:50%;transform:translate(-50%,-50%);color:white;font-size:24px;pointer-events:none;z-index:10;}
#netUI{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.8);display:flex;justify-content:center;align-items:center;z-index:200;color:white;}
#menuContent{background:#222;padding:30px;border-radius:10px;text-align:center;width:320px;}
.large-btn{font-size:18px;padding:10px;margin:5px 0;cursor:pointer;background:#4caf50;color:white;border:none;width:100%;border-radius:5px;}
#gameUI{position:fixed;bottom:20px;left:50%;transform:translateX(-50%);display:none;z-index:100;}
#hotbar{display:flex;background:rgba(0,0,0,0.5);padding:2px;border:2px solid #333;}
.slot{width:40px;height:40px;border:2px solid #555;display:flex;justify-content:center;align-items:center;}
.slot.active{border-color:white;background:rgba(255,255,255,0.3);}
.icon{width:20px;height:20px;}
</style>
</head>
<body>

<div id="netUI">
  <div id="menuContent">
    <h1 style="color:#4caf50">Mini Craft Pro</h1>
    <input id="userNameInput" placeholder="名前" style="padding:10px;width:80%;margin-bottom:10px;">
    <button class="large-btn" onclick="startHost()">ホストとして開始</button>
    <input id="hostIdInput" placeholder="4桁のIDを入力" style="padding:10px;width:80%;margin-top:10px;">
    <button class="large-btn" style="background:#2196f3" onclick="joinHost()">参加する</button>
  </div>
</div>

<div id="crosshair">+</div>
<div id="gameUI">
  <div id="hotbar">
    <div class="slot active" id="slot0"><div class="icon" style="background:#4caf50"></div></div>
    <div class="slot" id="slot1"><div class="icon" style="background:#8b5a2b"></div></div>
    <div class="slot" id="slot2"><div class="icon" style="background:#888888"></div></div>
    <div class="slot" id="slot3"><div class="icon" style="background:#2196f3;opacity:0.6"></div></div>
    <div class="slot" id="slot4"><div class="icon" style="background:#ddd"></div></div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.160/build/three.min.js"></script>
<script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>

<script>
// --- 設定 ---
const PLAYER_H = 1.6, GRAVITY = -20, JUMP = 8, MOVE_SPEED = 5, REACH = 10;
let selectedSlot = 0, blocks = new Map(), peer, isHost = false, connections = {}, myId;
const inventory = ["grass", "dirt", "stone", "water", "bedrock"];

// --- Three.js セットアップ ---
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x87ceeb);
const camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

// --- ライトの追加 (これで色が付きます) ---
const ambientLight = new THREE.AmbientLight(0xffffff, 0.6); // 環境光
scene.add(ambientLight);
const sunLight = new THREE.DirectionalLight(0xffffff, 0.8); // 太陽光
sunLight.position.set(10, 20, 10);
scene.add(sunLight);

// --- マテリアル ---
const mats = {
  grass: new THREE.MeshLambertMaterial({ color: 0x4caf50 }),
  dirt: new THREE.MeshLambertMaterial({ color: 0x8b5a2b }),
  stone: new THREE.MeshLambertMaterial({ color: 0x888888 }),
  water: new THREE.MeshLambertMaterial({ color: 0x2196f3, transparent: true, opacity: 0.6 }),
  bedrock: new THREE.MeshLambertMaterial({ color: 0x444444 })
};
const boxGeo = new THREE.BoxGeometry(1, 1, 1);

// --- 選択枠 ---
const selectionGeo = new THREE.EdgesGeometry(new THREE.BoxGeometry(1.02, 1.02, 1.02));
const selectionMat = new THREE.LineBasicMaterial({ color: 0x000000, linewidth: 2 });
const selectionCube = new THREE.LineSegments(selectionGeo, selectionMat);
selectionCube.visible = false;
scene.add(selectionCube);

function addBlock(x, y, z, type, send=true) {
  const k = `${x},${y},${z}`;
  if (blocks.has(k)) return;
  const mesh = new THREE.Mesh(boxGeo, mats[type]);
  mesh.position.set(x + 0.5, y + 0.5, z + 0.5);
  scene.add(mesh);
  blocks.set(k, { mesh, type });
  if (send && peer) {
    const d = { type: "add", x, y, z, blockType: type };
    isHost ? sendToAll(d) : sendToHost(d);
  }
}

function removeBlock(x, y, z, send=true) {
  const k = `${x},${y},${z}`;
  if (!blocks.has(k) || blocks.get(k).type === 'bedrock' && !isHost) return;
  scene.remove(blocks.get(k).mesh);
  blocks.delete(k);
  if (send && peer) {
    const d = { type: "break", x, y, z };
    isHost ? sendToAll(d) : sendToHost(d);
  }
}

function checkCollision(x, y, z) {
  const ix = Math.floor(x), iy = Math.floor(y - PLAYER_H + 0.1), iz = Math.floor(z);
  const b = blocks.get(`${ix},${iy},${iz}`);
  return b && b.type !== 'water';
}

// --- 通信処理 ---
function startHost() {
  const id = Math.floor(1000 + Math.random() * 9000).toString();
  peer = new Peer(id);
  peer.on('open', (id) => {
    isHost = true; alert("サーバーID: " + id);
    setupGame();
    // 初期床生成
    for(let x=-8; x<8; x++) for(let z=-8; z<8; z++) addBlock(x, -1, z, "grass", false);
  });
  peer.on('connection', conn => {
    conn.on('open', () => {
      connections[conn.peer] = conn;
      const world = []; blocks.forEach((v,k) => { 
        const [x,y,z]=k.split(',').map(Number); world.push({x,y,z,type:v.type}); 
      });
      conn.send({ type: "world", world });
    });
    conn.on('data', data => handleData(data, conn.peer));
  });
}

function joinHost() {
  const hostId = document.getElementById("hostIdInput").value;
  peer = new Peer();
  peer.on('open', id => {
    const conn = peer.connect(hostId);
    conn.on('open', () => { connections[hostId] = conn; setupGame(); });
    conn.on('data', data => handleData(data, hostId));
  });
}

function handleData(data, sender) {
  if (data.type === "world") data.world.forEach(b => addBlock(b.x, b.y, b.z, b.type, false));
  else if (data.type === "add") { addBlock(data.x, data.y, data.z, data.blockType, false); if(isHost) sendToAll(data, sender); }
  else if (data.type === "break") { removeBlock(data.x, data.y, data.z, false); if(isHost) sendToAll(data, sender); }
}

function sendToAll(d, skip) { for(let id in connections) if(id !== skip) connections[id].send(d); }
function sendToHost(d) { Object.values(connections)[0]?.send(d); }

// --- メインループ ---
let py = 5, vy = 0, yaw = 0, pitch = 0, locked = false;
const keys = {}, raycaster = new THREE.Raycaster();

function setupGame() {
  document.getElementById("netUI").style.display = "none";
  document.getElementById("gameUI").style.display = "block";
  
  document.addEventListener('keydown', e => {
    keys[e.code] = true;
    if(e.code.startsWith("Digit")) {
      document.getElementById("slot"+selectedSlot).classList.remove("active");
      selectedSlot = Math.min(parseInt(e.code.slice(-1)) - 1, 4);
      document.getElementById("slot"+selectedSlot).classList.add("active");
    }
  });
  document.addEventListener('keyup', e => keys[e.code] = false);
  document.body.onclick = () => renderer.domElement.requestPointerLock();
  document.addEventListener('pointerlockchange', () => locked = document.pointerLockElement === renderer.domElement);
  document.addEventListener('mousemove', e => {
    if(!locked) return;
    yaw -= e.movementX * 0.002;
    pitch = Math.max(-Math.PI/2, Math.min(Math.PI/2, pitch - e.movementY * 0.002));
    camera.rotation.set(pitch, yaw, 0, 'YXZ');
  });
  document.addEventListener('mousedown', e => {
    if(!locked) return;
    raycaster.setFromCamera({x:0, y:0}, camera);
    const hits = raycaster.intersectObjects(Array.from(blocks.values()).map(b => b.mesh));
    if(hits.length > 0 && hits[0].distance < REACH) {
      const bPos = hits[0].object.position;
      if(e.button === 0) removeBlock(Math.floor(bPos.x), Math.floor(bPos.y), Math.floor(bPos.z));
      if(e.button === 2) {
        const p = bPos.clone().add(hits[0].face.normal);
        addBlock(Math.floor(p.x), Math.floor(p.y), Math.floor(p.z), inventory[selectedSlot]);
      }
    }
  });

  animate();
}

function animate() {
  requestAnimationFrame(animate);
  if(locked) {
    const dt = 0.016;
    // 移動計算
    const move = new THREE.Vector3();
    if(keys["KeyW"]) move.z -= 1; if(keys["KeyS"]) move.z += 1;
    if(keys["KeyA"]) move.x -= 1; if(keys["KeyD"]) move.x += 1;
    move.applyAxisAngle(new THREE.Vector3(0,1,0), yaw).normalize().multiplyScalar(MOVE_SPEED * dt);
    
    if(!checkCollision(camera.position.x + move.x, py, camera.position.z)) camera.position.x += move.x;
    if(!checkCollision(camera.position.x, py, camera.position.z + move.z)) camera.position.z += move.z;

    // 重力
    vy += GRAVITY * dt;
    py += vy * dt;
    if(checkCollision(camera.position.x, py, camera.position.z)) {
      py = Math.floor(py - PLAYER_H + 0.1) + 1 + PLAYER_H;
      vy = keys["Space"] ? JUMP : 0;
    }
    camera.position.y = py;

    // 選択枠の更新
    raycaster.setFromCamera({x:0, y:0}, camera);
    const hits = raycaster.intersectObjects(Array.from(blocks.values()).map(b => b.mesh));
    if(hits.length > 0 && hits[0].distance < REACH) {
      selectionCube.position.copy(hits[0].object.position);
      selectionCube.visible = true;
    } else {
      selectionCube.visible = false;
    }
  }
  renderer.render(scene, camera);
}

window.onresize = () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
};
</script>
</body>
</html>
