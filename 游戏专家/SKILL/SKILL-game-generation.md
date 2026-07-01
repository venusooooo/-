# SKILL-game-generation.md - 游戏直接生成与自检质控

> 供流程G（框架完成后直接出游戏）和流程I（游戏自检与修复）调用。
> 包含两部分：**生成规范**（3D/2D技术选型 + Three.js生成标准）+ **自检质控**（三层检查 + 各类型专项清单）。

---

## 零、已知高频 Bug 修复规范（生成时必须遵守）

> 以下是游戏首次生成后最常见的 bug，**生成代码时直接按规范写，不要等自检再发现**。

### Bug #1：开始按钮点击无响应（最高频）

**根本原因**：点击坐标用了页面坐标（`e.clientX/Y`），没有减去 canvas 的偏移量，导致点击区域判断全部偏移，看起来按钮可见但点不到。

**错误写法**：
```javascript
canvas.addEventListener('click', function(e) {
  // ❌ 直接用 clientX/Y，没有减 canvas 偏移
  if (e.clientX > btnX && e.clientX < btnX + btnW &&
      e.clientY > btnY && e.clientY < btnY + btnH) {
    startGame();
  }
});
```

**正确写法（所有原生 Canvas 游戏必须用这个模板）**：
```javascript
canvas.addEventListener('click', function(e) {
  // ✅ 用 getBoundingClientRect() 计算实际偏移，并处理缩放比例
  const rect = canvas.getBoundingClientRect();
  const scaleX = canvas.width / rect.width;
  const scaleY = canvas.height / rect.height;
  const x = (e.clientX - rect.left) * scaleX;
  const y = (e.clientY - rect.top) * scaleY;

  if (x > btnX && x < btnX + btnW && y > btnY && y < btnY + btnH) {
    startGame();
  }
});
```

**p5.js 游戏的处理方式**：p5.js 的 `mouseX / mouseY` 已经自动处理了 canvas 偏移，可以直接用。但如果 canvas 用了 CSS `transform: scale()` 或 `zoom`，仍需手动修正：
```javascript
function mousePressed() {
  // p5.js 正常情况直接用 mouseX/mouseY，无需修正
  if (mouseX > btnX && mouseX < btnX + btnW &&
      mouseY > btnY && mouseY < btnY + btnH) {
    gameState = 'playing';
  }
}
```

**自检验证**：生成后在开始界面，想象鼠标点击按钮中心点，坐标计算路径是否从 canvas 左上角开始计算。如果 canvas 居中显示，clientX 不减 `rect.left` 就是错的。

---

### Bug #2：canvas 尺寸与显示尺寸不一致导致点击偏移

**根本原因**：canvas 的 `width/height` 属性（逻辑像素）与 CSS 显示尺寸不一致，点击坐标需要按比例缩放。

**标准 canvas 初始化（所有游戏必须用）**：
```javascript
const canvas = document.getElementById('gameCanvas');
// ✅ canvas 逻辑尺寸 = 显示尺寸，避免坐标偏移
canvas.width = 800;   // 与 CSS width 保持一致
canvas.height = 600;  // 与 CSS height 保持一致
// CSS: canvas { width: 800px; height: 600px; display: block; margin: 0 auto; }
```

如果需要响应式（canvas 随窗口缩放），用上方 Bug #1 的 `scaleX/scaleY` 方案统一处理。

---

## 一、游戏生成规范

### 技术选型

| 游戏类型 | 选用技术 | 判断依据 |
|---------|---------|---------|
| 2D 游戏（扫雷/大鱼吃小鱼/2D塔防/PvZ/消消乐/射击） | `p5js-creative-coding` | 轻量、移动端友好、快速出原型 |
| 3D 游戏（3D塔防/FPS/TPS/3D跑酷）或用户指定要3D效果 | `three.js`（本文件规范） | PBR材质/光影/粒子等3D效果 |

**选型原则**：用户没说3D就选p5js；用户说「要3D效果」或「第一/第三人称」才选Three.js。

---

### Three.js 生成规范（3D游戏专用）

#### 输出格式
- **单个 HTML 文件**，Three.js 通过 CDN 引入（ES modules 方式）
- 所有游戏逻辑内联在 `<script type="module">` 中
- 无需任何安装，直接在浏览器中打开运行

#### 标准场景初始化模板
```javascript
import * as THREE from 'https://cdn.skypack.dev/three@0.160.0';
import { OrbitControls } from 'https://cdn.skypack.dev/three@0.160.0/examples/jsm/controls/OrbitControls.js';

// 场景
const scene = new THREE.Scene();
scene.fog = new THREE.FogExp2(0x1a1a2e, 0.02);

// 相机
const camera = new THREE.PerspectiveCamera(75, innerWidth/innerHeight, 0.1, 1000);
camera.position.set(0, 10, 20);

// 渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(innerWidth, innerHeight);
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.outputColorSpace = THREE.SRGBColorSpace;
renderer.toneMapping = THREE.ACESFilmicToneMapping;
document.body.appendChild(renderer.domElement);

// 控制器
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;

// 光照（三点布光）
const ambientLight = new THREE.AmbientLight(0x404060, 0.5);
const dirLight = new THREE.DirectionalLight(0xffffff, 1.5);
dirLight.position.set(10, 20, 10);
dirLight.castShadow = true;
dirLight.shadow.mapSize.width = 2048;
dirLight.shadow.mapSize.height = 2048;
scene.add(ambientLight, dirLight);

// 渲染循环
const clock = new THREE.Clock();
function animate() {
  requestAnimationFrame(animate);
  const delta = clock.getDelta();
  controls.update();
  // gameUpdate(delta);
  renderer.render(scene, camera);
}
animate();

// 响应式
window.addEventListener('resize', () => {
  camera.aspect = innerWidth / innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});
```

#### 3D 质量要求
- **PBR材质**：使用 `MeshStandardMaterial` 或 `MeshPhysicalMaterial`，不用 `MeshBasicMaterial`
- **阴影必开**：至少方向光开启 `castShadow`，地面开启 `receiveShadow`
- **相机阻尼**：`controls.enableDamping = true`，相机运动要有惯性感
- **色调映射**：`ACESFilmicToneMapping`，防止颜色过曝过暗
- **帧率目标**：桌面端 60fps；超过 10000 个物体改用 `InstancedMesh`

---

### 生成交付流程

```
用户确认框架/配置
  ↓
选定技术（p5js 或 Three.js）
  ↓
生成完整游戏 HTML（含完整游戏循环：开始→游戏中→结束→重开）
  ↓
强制触发「二、游戏自检质控」流程（不可跳过）
  ↓
输出自检报告 + 交付
  ↓
告知运行方式（说人话）：
  - 2D："下载下来直接双击用浏览器打开就能玩，不用装任何东西"
  - 3D："浏览器直接打开，鼠标左键拖转视角，滚轮缩放，W/A/S/D 移动"
```

---

## 二、游戏自检质控

> **自检是强制流程，不能跳过，不能「大概没问题」就交付。**
> 发现问题立即修复，修完复查，全部通过才交付。

### 自检三步走

**第一步：代码主路径走查**

在脑中追踪一遍完整流程，确认每个环节有入口、有出口、状态转换正确：

```
页面加载 → 开始界面显示 → 玩家触发开始 → 游戏循环运行
→ 玩家输入响应 → 游戏状态更新 → 结束条件触发
→ 结束界面显示 → 重开 → 所有变量正确重置 → 游戏循环重新运行
```

**第二步：按清单逐项检查**（见下方详细清单）

**第三步：发现问题 → 立即修复 → 复查**

修复优先级：
- P0（必须修）：游戏无法启动、结束后无法重开、核心玩法逻辑错误
- P1（必须修）：明显影响体验的bug（如扫雷第一次踩雷、蛇可以反向移动）
- P2（酌情处理）：小体验问题（如分数显示位置不好看）

---

### 通用基础检查（所有游戏必查）

#### 语法与结构
- [ ] HTML 结构完整：`<!DOCTYPE html>` / `<html>` / `<head>` / `<body>` 不缺
- [ ] 所有 JS 代码在 `<script>` 标签内，无语法错误（括号/引号/大括号匹配）
- [ ] 外部库使用有效的 CDN 链接（p5.js / three.js 等）
- [ ] Canvas 或渲染容器已正确初始化，尺寸设置合理

#### 游戏生命周期
- [ ] **开始界面**：有开始按钮或提示，不会直接进入游戏
- [ ] **游戏中状态**：核心循环在运行（update + render 正常调用）
- [ ] **游戏结束判断**：有明确触发条件，不会陷入无限循环
- [ ] **重开机制**：结束后可重新开始，重开时所有变量正确重置（最常见bug：分数/状态没清空）

#### 输入响应
- [ ] 键盘事件：已注册监听，不会多次注册导致响应叠加
- [ ] 鼠标/触摸事件：坐标换算正确（canvas内坐标 vs 页面坐标）
- [ ] 游戏结束状态时输入已屏蔽或正确处理

#### 基础体验
- [ ] 有分数/进度显示，玩家知道当前状态
- [ ] 有操作说明（至少在开始界面说明控制方式）
- [ ] 使用 `requestAnimationFrame` 循环，不用 `setInterval` 替代

---

### 各游戏类型专项检查

#### 一、休闲类

**扫雷**
- [ ] 首次点击安全：第一次点击后才生成地雷（首次不能踩雷）
- [ ] 空白递归展开：点开空白格时周围空白格自动展开（BFS/递归）
- [ ] 地雷总数与设定匹配
- [ ] 胜利条件：非地雷格全部翻开 = 胜利（不是只判断旗子数）
- [ ] 旗子操作：右键插/取旗正常，计数正确
- [ ] 重开时棋盘完全重置（含地雷位置、翻开状态、旗子、计时器）

**三消（消消乐）**
- [ ] 匹配检测横向和纵向都要有
- [ ] 消除后上方格子正确下落，新格子从顶部填入
- [ ] 下落后立即检测是否产生连消
- [ ] 无效交换：不产生三消时格子退回原位

**贪吃蛇**
- [ ] 边界碰撞正确触发结束
- [ ] 自身碰撞正确触发结束
- [ ] 反向移动屏蔽（向右走时左键无效）
- [ ] 食物不生成在蛇身上
- [ ] 吃食物后长度正确增加1

**打砖块**
- [ ] 碰挡板不同位置反弹角度不同（不能永远垂直）
- [ ] 球从侧面撞砖 vs 从上下撞砖反弹方向不同
- [ ] 球速随时间加快
- [ ] 全部砖块消除 = 胜利

#### 二、射击类

- [ ] 子弹飞出屏幕后从数组中移除（防内存泄漏）
- [ ] 敌人消灭或飞出屏幕后从数组中移除
- [ ] 碰撞检测双向：玩家子弹打敌人 + 敌人子弹打玩家
- [ ] 玩家受伤后有短暂无敌帧
- [ ] 游戏结束后停止生成敌人和子弹
- [ ] 消灭敌人时分数正确更新

#### 三、塔防策略类

**通用塔防**
- [ ] 寻路正确：敌人能找到从起点到终点的路径，不会原地打转
- [ ] 路径动态更新（若支持建墙）：放塔后路径依然可达，不能堵死
- [ ] 炮塔攻击范围可视化（放置时显示攻击圈）
- [ ] 炮塔只攻击范围内敌人
- [ ] 敌人血量扣减正确，不出现负值
- [ ] 波次结束判断正确
- [ ] 生命值系统：敌人到达终点扣血，归零=失败

**PvZ 类**
- [ ] 同一格不能放两株植物
- [ ] 阳光产出速率与植物费用匹配，初期不至于完全无法操作
- [ ] 僵尸与植物只在同一行攻击（跨行不碰撞）
- [ ] 植物死亡后格子释放

#### 四、策略经营类

- [ ] 资源不出现负值（消耗前检查余量）
- [ ] 时间系统正确推进，不会因其他逻辑卡顿
- [ ] 存档/读档（若有）：数据完整保存，重载后状态一致
- [ ] 离线收益（若有）：以实际离线时间计算，不超上限

#### 五、横版闯关动作

- [ ] 重力系统：角色在空中持续下落，不会悬浮
- [ ] 跳跃只在地面触发（空中不能二段跳，除非特殊设计）
- [ ] 平台碰撞：站在平台上不穿透，侧面不卡墙
- [ ] 角色落出屏幕底部 = 死亡
- [ ] 敌人碰撞：从上方踩死 vs 侧面碰到受伤，两种都处理
- [ ] 关卡终点触发：到达出口/旗子时正确进入下一关或胜利

#### 六、角色扮演 RPG

- [ ] 战斗数值平衡：1级玩家不会被初始敌人秒杀，也不会造成0伤害
- [ ] 死亡处理：HP归零有处理（死亡界面/复活点），不陷入死亡循环
- [ ] 技能/物品效果正确叠加，不会叠出异常大数值
- [ ] NPC/对话（若有）：触发后有退出方式，不会锁死界面

#### 七、竞速 / 体育

- [ ] 车辆物理：加速/刹车/转向手感合理，不会瞬移
- [ ] 赛道边界：出界有惩罚或阻挡，不直接穿透
- [ ] 计时器：开始计时、完成停止、格式正确（分:秒:毫秒）
- [ ] 圈数/终点判断正确结算

#### 八、解谜密室

- [ ] 谜题有解：当前关卡配置确实存在正确解法
- [ ] 状态回滚（若有撤销）：撤销后状态完全一致
- [ ] 胜利判断精确：所有目标满足才触发胜利，不误判
- [ ] 提示系统（若有）：提示内容与当前谜题状态匹配

---

### 交付前最终确认

- [ ] 追踪一次完整游戏流程（开始→核心操作×3轮→结束→重开），主路径无断裂
- [ ] `CONFIG` 对象：所有用户确认的参数值已正确填入，无遗漏占位符
- [ ] 无明显语法错误（未定义变量、括号不匹配等）
- [ ] 文件是完整的自包含 HTML，不依赖本地文件路径

---

### 自检报告输出格式

发现并修复了问题时：
```
代码写好了，我先自己过了一遍——检查了 XX 项，发现了 Y 个问题已经修掉了：

🔧 修复：[问题描述]（[修复方式]）
🔧 修复：[问题描述]（[修复方式]）

现在可以直接打开玩了，你试一下，有什么不对再告诉我。
```

没有发现问题时：
```
代码写好了，自检过了一遍，XX 项全部通过，没发现问题。直接打开玩就行，有什么不对随时说。
```

严重问题无法当轮修复时：明确告知是什么问题、为什么暂时无法修复、下一步怎么解决。不交付有已知严重bug的版本。
