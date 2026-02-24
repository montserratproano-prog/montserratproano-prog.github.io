<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>find the rats!</title>
<style>
body { margin:0; overflow:hidden; font-family:sans-serif }
#ui {
position:absolute;
top:10px;
left:10px;
color:white;
font-size:18px;
background:rgba(0,0,0,0.4);
padding:10px;
border-radius:10px
}
</style>
</head>

<body>

<div id="ui">
Ratbucks: <span id="money">0</span><br>
<span id="found"></span>
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js"></script>

<script>
// escena
const scene = new THREE.Scene()
scene.background = new THREE.Color(0x87ceeb)

// cámara
const camera = new THREE.PerspectiveCamera(75,innerWidth/innerHeight,0.1,1000)
camera.position.set(0,5,10)

// render
const renderer = new THREE.WebGLRenderer({antialias:true})
renderer.setSize(innerWidth,innerHeight)
document.body.appendChild(renderer.domElement)

// luz
const light = new THREE.DirectionalLight(0xffffff,1)
light.position.set(5,10,5)
scene.add(light)

// suelo ciudad
const ground = new THREE.Mesh(
new THREE.PlaneGeometry(200,200),
new THREE.MeshStandardMaterial({color:0x5a7f5a})
)
ground.rotation.x = -Math.PI/2
scene.add(ground)

// jugador
const player = new THREE.Mesh(
new THREE.BoxGeometry(1,2,1),
new THREE.MeshStandardMaterial({color:0x0000ff})
)
player.position.y = 1
scene.add(player)

// cámara sigue jugador
function followCamera(){
camera.position.x = player.position.x
camera.position.z = player.position.z + 10
camera.lookAt(player.position)
}

// controles
const keys = {}
onkeydown=e=>keys[e.key]=true
onkeyup=e=>keys[e.key]=false

// RATBUCKS
let money = 0
function addMoney(n){
money += n
document.getElementById("money").textContent = money
}

// ===== SISTEMA DE RATA =====
function createRat(name, rarity, x, z){

const color = {
COMMON:0x00ff00,
RARE:0xff69b4,
MYTHIC:0x0099ff,
LEGENDARY:0xffd700
}[rarity]

const reward = {
COMMON:10,
RARE:15,
MYTHIC:25,
LEGENDARY:50
}[rarity]

const rat = new THREE.Mesh(
new THREE.SphereGeometry(0.6),
new THREE.MeshStandardMaterial({color})
)

rat.position.set(x,0.6,z)
rat.userData = {name, rarity, reward, found:false}
scene.add(rat)
return rat
}

// ===== RATS ÚNICAS =====
const rats = []

// zonas ciudad
rats.push(createRat("rata madre","COMMON",-20,10))
rats.push(createRat("rata estudiosa","COMMON",30,25))
rats.push(createRat("rata modelo","LEGENDARY",50,-40))
rats.push(createRat("rata depresiva","RARE",-45,-10))
rats.push(createRat("rata youtuber","RARE",10,-30))
rats.push(createRat("rata panadera","COMMON",-10,35))

// templo con 22 ratas
for(let i=0;i<22;i++){
rats.push(createRat("rata templo "+(i+1),"MYTHIC",60+i*2,60))
}

// 102 ratas ciudad únicas
for(let i=0;i<102;i++){
rats.push(createRat("rata "+(i+1),"COMMON",
Math.random()*120-60,
Math.random()*120-60))
}

// ===== DETECCIÓN =====
function checkRats(){
rats.forEach(r=>{
if(!r.userData.found){
const d = player.position.distanceTo(r.position)
if(d < 2){
r.userData.found = true
r.visible = false
addMoney(r.userData.reward)

document.getElementById("found").textContent =
"Encontraste a " + r.userData.name
}
}
})
}

// ===== MOVIMIENTO =====
function move(){
const speed = 0.2
if(keys["w"]) player.position.z -= speed
if(keys["s"]) player.position.z += speed
if(keys["a"]) player.position.x -= speed
if(keys["d"]) player.position.x += speed
}

// ===== LOOP =====
function animate(){
requestAnimationFrame(animate)
move()
followCamera()
checkRats()
renderer.render(scene,camera)
}
animate()

</script>
</body>
</html>
