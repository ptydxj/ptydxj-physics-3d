[Uploading index.html…]()
# ptydxj-physics-3d<!doctype html>
<html lang="zh-CN">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<title>第13题｜摩擦力三维演示</title>
<style>
  :root{
    --bg:#070b12; --line:rgba(255,255,255,.075);
    --txt:#e9eefb; --muted:#93a3bf;
    --accent:#5aa2ff;
    --cG:#ff6b6b; --cN:#4ade80; --cT:#38bdf8; --cS:#fbbf24; --cK:#f472b6; --cX:#94a3b8;
  }
  *{box-sizing:border-box}
  html,body{margin:0;height:100%;background:var(--bg);color:var(--txt);
    font-family:ui-sans-serif,system-ui,"PingFang SC","Microsoft YaHei",sans-serif;
    -webkit-font-smoothing:antialiased;-webkit-tap-highlight-color:transparent}
  body{overflow:hidden}
  .app{display:flex;height:100vh;width:100vw}

  /* 舞台 */
  .stage{position:relative;flex:1;min-width:0;overflow:hidden;user-select:none;-webkit-user-select:none;
    background:radial-gradient(120% 95% at 48% 8%,#17263f 0%,#0c1526 52%,#070b12 100%)}
  .stage::after{content:"";position:absolute;inset:0;pointer-events:none;z-index:1;
    background:radial-gradient(115% 100% at 50% 42%,transparent 52%,rgba(0,0,0,.55) 100%)}
  #scene{position:absolute;inset:0;width:100%;height:100%;display:block;cursor:grab;touch-action:none}
  #scene:active{cursor:grabbing}

  .labels{position:absolute;inset:0;overflow:hidden;pointer-events:none;z-index:3}
  .lbl{position:absolute;left:0;top:0;transform:translate(-50%,-50%);
    font-size:11.5px;line-height:1;padding:4px 7px;border-radius:6px;white-space:nowrap;
    background:rgba(7,11,19,.8);border:1px solid rgba(255,255,255,.13);
    color:#dbe6fa;font-variant-numeric:tabular-nums;letter-spacing:.2px;
    backdrop-filter:blur(4px);display:none}
  .lbl.on{display:block}
  .lbl.force{font-weight:600;font-size:12px}
  .lbl.obj{background:rgba(10,18,32,.82);color:#cfe0ff;border-color:rgba(120,170,255,.26);
    font-weight:600;font-size:11px;letter-spacing:.3px}

  .stage-head{position:absolute;left:28px;top:22px;z-index:4;pointer-events:none}
  .stage-head .tag{font-size:10px;letter-spacing:.18em;color:#7ea6dd;margin-bottom:6px}
  .stage-head h1{margin:0;font-size:20px;font-weight:620;letter-spacing:.4px;color:#eef4ff}

  .legend{position:absolute;left:28px;bottom:20px;z-index:4;display:flex;gap:14px;flex-wrap:wrap;
    padding:7px 12px;border-radius:9px;background:rgba(8,13,23,.55);border:1px solid var(--line);
    backdrop-filter:blur(6px);font-size:10.5px;color:#a8b8d4}
  .legend i{display:inline-block;width:7px;height:7px;border-radius:50%;margin-right:5px}
  .hint{position:absolute;right:20px;bottom:20px;z-index:4;font-size:10px;color:#5d6d86;letter-spacing:.3px}
  .hint-m{display:none}

  /* 侧栏 */
  .panel{width:372px;flex:0 0 372px;background:linear-gradient(180deg,#0d1728 0%,#0a1120 100%);
    border-left:1px solid var(--line);padding:20px 20px 24px;overflow-y:auto;z-index:2}
  .panel::-webkit-scrollbar{width:6px}
  .panel::-webkit-scrollbar-thumb{background:#22304a;border-radius:6px}

  .presets{display:grid;grid-template-columns:1fr 1fr;gap:6px;margin-bottom:13px}
  .preset{font:inherit;text-align:left;font-size:11.5px;color:#b3c3db;cursor:pointer;
    background:rgba(255,255,255,.028);border:1px solid var(--line);border-radius:9px;
    padding:7px 9px;transition:.14s;line-height:1.3}
  .preset:hover{background:rgba(90,162,255,.09);border-color:rgba(90,162,255,.28);color:#e6f0ff}
  .preset.on{background:linear-gradient(135deg,#1e4bb4,#2f6df6);border-color:transparent;color:#fff}

  .state{border:1px solid var(--line);border-radius:11px;padding:9px 12px;margin-bottom:13px;
    background:linear-gradient(135deg,rgba(255,255,255,.045),rgba(255,255,255,.015))}
  .state-top{display:flex;align-items:baseline;justify-content:space-between;gap:10px}
  #stateName{font-size:17px;font-weight:650;letter-spacing:.4px}
  #stateF{font-size:16px;font-weight:650;font-variant-numeric:tabular-nums}
  .state-sub{font-size:10.5px;color:#8296b4;margin-top:4px;font-variant-numeric:tabular-nums}
  .st-static #stateName{color:#ffd066}
  .st-slide #stateName{color:#ff8fbf}

  .sec-label{font-size:10px;letter-spacing:.12em;color:#7c90af;margin:0 0 4px}

  .prow{display:grid;grid-template-columns:58px 1fr 48px 14px;align-items:center;gap:7px;margin:6px 0}
  .prow>span{font-size:11px;color:#9fb0ca;display:flex;align-items:center;gap:5px;white-space:nowrap}
  .prow b{font-size:11px;color:#e8f1ff;text-align:right;font-variant-numeric:tabular-nums;font-weight:600}
  .prow i{font-style:normal;font-size:9px;color:#6f82a0}
  .num{width:100%;background:rgba(255,255,255,.05);border:1px solid rgba(255,255,255,.1);border-radius:5px;
    color:#e8f1ff;font:inherit;font-size:10.5px;padding:2px 3px;text-align:right;
    font-variant-numeric:tabular-nums;-moz-appearance:textfield}
  .num::-webkit-outer-spin-button,.num::-webkit-inner-spin-button{-webkit-appearance:none;margin:0}
  .num:focus{outline:none;border-color:#5aa2ff;background:rgba(90,162,255,.08)}
  .num:disabled{opacity:.35;cursor:not-allowed}
  .prow input[type=range]{height:18px}
  .tgl{font:inherit;font-size:9px;line-height:1;padding:2px 5px;border-radius:4px;cursor:pointer;
    border:1px solid rgba(255,255,255,.16);background:rgba(255,255,255,.05);color:#9fb0ca}
  .tgl.on{border-color:rgba(90,162,255,.45);background:rgba(90,162,255,.18);color:#d9e9ff}
  .tgl:disabled{opacity:.35;cursor:not-allowed}

  input[type=range]{-webkit-appearance:none;appearance:none;width:100%;height:18px;background:transparent;
    outline:none;cursor:pointer;margin:0}
  input[type=range]::-webkit-slider-runnable-track{height:4px;border-radius:4px;
    background:linear-gradient(90deg,#3f7ff0,#2a3d5e)}
  input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;width:12px;height:12px;border-radius:50%;
    background:#dcebff;border:2px solid #2f6df6;margin-top:-4px}
  input[type=range]:disabled{opacity:.35;cursor:not-allowed}

  .staticTime{display:none;font-size:10.5px;color:#7f92b0;margin:13px 0 15px}
  .time{display:grid;grid-template-columns:26px 1fr auto;align-items:center;gap:9px;margin:9px 0 12px}
  .play{width:26px;height:26px;border-radius:50%;border:1px solid rgba(90,162,255,.4);
    background:rgba(90,162,255,.14);color:#cfe4ff;font-size:10px;cursor:pointer;line-height:1;padding:0}
  .play:hover{background:rgba(90,162,255,.28)}
  .play:disabled{opacity:.35;cursor:not-allowed}
  #tVal{font-size:10.5px;color:#94a8c6;font-variant-numeric:tabular-nums;min-width:78px;text-align:right}

  .objs{display:flex;gap:5px;background:rgba(255,255,255,.03);padding:3px;border-radius:9px;
    border:1px solid var(--line);margin-bottom:12px}
  .objs button{flex:1;font:inherit;font-size:10.5px;color:#9fb1cc;background:transparent;border:0;
    padding:6px 2px;border-radius:6px;cursor:pointer;transition:.14s}
  .objs button:hover:not(:disabled){color:#e8f1ff;background:rgba(255,255,255,.05)}
  .objs button.on{background:rgba(90,162,255,.18);color:#dcebff;box-shadow:inset 0 0 0 1px rgba(90,162,255,.35)}
  .objs button:disabled{opacity:.3;cursor:not-allowed}

  .tbl{width:100%;border-collapse:collapse;font-size:10.5px;font-variant-numeric:tabular-nums}
  .tbl th{font-weight:500;color:#6f82a0;text-align:right;padding:3px 2px;border-bottom:1px solid var(--line)}
  .tbl th:first-child,.tbl td:first-child{text-align:left}
  .tbl td{padding:5px 2px;text-align:right;color:#c9d6ea;border-bottom:1px dashed rgba(255,255,255,.05)}
  .tbl tr:last-child td{border-bottom:0}
  .tbl tr.on td{background:rgba(90,162,255,.08)}
  .tbl td:first-child{color:#dbe7fb;font-weight:600}
  .unit{font-size:9.5px;color:#5f728e;margin-top:5px}
  .warn{font-size:10px;line-height:1.5;color:#e8a45c;margin-top:6px}
  .tbl td.fc{cursor:pointer;border-bottom:1px dotted rgba(120,170,255,.35)}
  .tbl td.fc:hover{color:#fff;background:rgba(90,162,255,.14)}
  .calc{position:fixed;z-index:60;width:min(268px,86vw);border:1px solid rgba(90,162,255,.35);border-radius:10px;
    padding:9px 11px 10px;background:rgba(9,15,26,.97);box-shadow:0 20px 50px -18px rgba(0,0,0,.95);
    backdrop-filter:blur(8px)}
  .calc-head{display:flex;align-items:center;gap:7px;margin-bottom:5px}
  .calc-title{font-size:11.5px;font-weight:600;color:#e8f1ff}
  .badge{font-size:9px;line-height:1;padding:2px 6px;border-radius:4px;border:1px solid;white-space:nowrap}
  .badge.known{color:#8ee7b0;border-color:rgba(80,220,150,.4);background:rgba(80,220,150,.1)}
  .badge.derived{color:#9cc8ff;border-color:rgba(90,162,255,.4);background:rgba(90,162,255,.1)}
  .badge.given{color:#ffd066;border-color:rgba(255,208,102,.4);background:rgba(255,208,102,.1)}
  .calc-x{margin-left:auto;background:none;border:0;color:#7f92b0;font-size:15px;line-height:1;
    cursor:pointer;padding:0 2px}
  .calc-x:hover{color:#fff}
  .cline{font-size:10.5px;line-height:1.6;color:#b9c9e0;font-variant-numeric:tabular-nums}
  .lbl.force{pointer-events:none}
  .lbl.force.on{pointer-events:auto;cursor:pointer}
  .lbl.force.on:hover{border-color:#5aa2ff}

  @media (max-width: 820px){
    body{overflow:hidden}
    .app{flex-direction:column;height:100vh;height:100dvh}
    .stage{height:clamp(240px,46dvh,430px);flex:none;min-height:0}
    .stage-head{left:16px;top:12px}
    .stage-head .tag{display:none}
    .stage-head h1{font-size:16px}
    .legend{left:12px;bottom:12px;gap:10px;font-size:9.5px;padding:6px 10px}
    .legend i{width:6px;height:6px;margin-right:4px}
    .hint{display:none}
    .hint-m{display:block;right:12px;top:14px;bottom:auto;font-size:9px}
    .panel{width:auto;flex:1;min-height:0;border-left:0;border-top:1px solid var(--line);
      border-radius:18px 18px 0 0;padding:16px 16px calc(18px + env(safe-area-inset-bottom));
      box-shadow:0 -14px 34px rgba(0,0,0,.4);overscroll-behavior:contain}
    .presets{gap:7px;margin-bottom:12px}
    .preset{font-size:12px;padding:9px 10px}
    .state{padding:10px 12px;margin-bottom:12px}
    #stateName{font-size:18px}
    #stateF{font-size:17px}
    .state-sub{font-size:11px}
    .tgl{font-size:10px;padding:3px 7px}
    .prow{grid-template-columns:50px 1fr 46px 12px;gap:8px;margin:9px 0}
    .prow>span{font-size:11.5px}
    .num{font-size:12px;padding:4px}
    input[type=range]{height:26px}
    input[type=range]::-webkit-slider-thumb{width:18px;height:18px;margin-top:-7px}
    .time{grid-template-columns:32px 1fr auto;gap:10px;margin:10px 0 13px}
    .play{width:32px;height:32px;font-size:12px}
    #tVal{font-size:11px}
    .objs button{padding:9px 2px;font-size:11.5px}
    .tbl{font-size:10.5px}
    .tbl th,.tbl td{padding:6px 2px}
    .unit{font-size:9px}
    .calc{width:min(300px,86vw)}
    .calc-title{font-size:12px}
    .cline{font-size:11px}
  }
</style>
</head>
<body>
<div class="app">
  <div class="stage" id="stage">
    <canvas id="scene"></canvas>
    <div class="labels" id="labels"></div>
    <div class="stage-head">
      <div class="tag">2026 秋｜高一物理｜期中第 13 题</div>
      <h1>摩擦力：三维受力</h1>
    </div>
    <div class="legend">
      <span><i style="background:var(--cG)"></i>重力 G</span>
      <span><i style="background:var(--cN)"></i>支持力 N</span>
      <span><i style="background:var(--cT)"></i>拉力 T</span>
      <span><i style="background:var(--cS)"></i>静摩擦</span>
      <span><i style="background:var(--cK)"></i>滑动摩擦</span>
    </div>
    <div class="hint">拖动旋转｜滚轮缩放｜双击复位</div>
    <div class="hint hint-m">单指旋转｜双指缩放</div>
  </div>

  <aside class="panel">
    <div class="presets" id="presets"></div>

    <div class="state st-static" id="state">
      <div class="state-top"><span id="stateName">静止</span><span id="stateF">f = 1.0 N</span></div>
      <div class="state-sub" id="stateSub">G桶 1.0 N ≤ f_max 4.5 N</div>
    </div>

    <div class="sec-label">参数</div>
    <div class="prow"><span>M</span><input type="range" id="rM" min="10" max="60" step="1" value="20"><input class="num" id="nM" type="number" min="1" max="6" step="0.1" value="2.0"><i>kg</i></div>
    <div class="prow"><span>m₁ <button class="tgl" id="topTgl">无</button></span><input type="range" id="rM1" min="5" max="60" step="1" value="30" disabled><input class="num" id="nM1" type="number" min="0.5" max="6" step="0.1" value="3.0" disabled><i>kg</i></div>
    <div class="prow"><span>沙</span><input type="range" id="rSand" min="0" max="100" step="5" value="0"><input class="num" id="nSand" type="number" min="0" max="1" step="0.05" value="0.00"><i>kg</i></div>
    <div class="prow"><span>μ₁ 地面</span><input type="range" id="rMu" min="5" max="45" step="1" value="20"><input class="num" id="nMu" type="number" min="0.05" max="0.45" step="0.01" value="0.20"><i></i></div>
    <div class="prow"><span>μ₂ 物块间</span><input type="range" id="rMu2" min="2" max="45" step="1" value="20" disabled><input class="num" id="nMu2" type="number" min="0.02" max="0.45" step="0.01" value="0.20" disabled><i></i></div>

    <div class="time" id="timeRow">
      <button class="play" id="play">▶</button>
      <input type="range" id="rTime" min="0" max="1000" value="0" step="1">
      <span id="tVal">t 0.00 s</span>
    </div>
    <div class="staticTime" id="staticTime">静止，各时刻状态相同</div>

    <div class="objs" id="objs"></div>

    <table class="tbl" id="tbl"></table>
    <div class="unit">m：kg｜v：m s⁻¹｜a：m s⁻²｜力：N</div>
    <div class="warn" id="warn" style="display:none"></div>
  </aside>
</div>

<div class="calc" id="calc" hidden>
  <div class="calc-head">
    <span class="calc-title" id="calcTitle"></span>
    <span class="badge" id="calcBadge"></span>
    <button class="calc-x" id="calcClose">×</button>
  </div>
  <div class="calc-body" id="calcBody"></div>
</div>

<script>
'use strict';
/* ---------- 向量 ---------- */
const v3=(x=0,y=0,z=0)=>({x,y,z});
const vadd=(a,b)=>({x:a.x+b.x,y:a.y+b.y,z:a.z+b.z});
const vsub=(a,b)=>({x:a.x-b.x,y:a.y-b.y,z:a.z-b.z});
const vmul=(a,s)=>({x:a.x*s,y:a.y*s,z:a.z*s});
const vdot=(a,b)=>a.x*b.x+a.y*b.y+a.z*b.z;
const vcross=(a,b)=>({x:a.y*b.z-a.z*b.y,y:a.z*b.x-a.x*b.z,z:a.x*b.y-a.y*b.x});
const vlen=a=>Math.hypot(a.x,a.y,a.z);
const vnorm=a=>{const l=vlen(a)||1;return{x:a.x/l,y:a.y/l,z:a.z/l};};
const clamp=(v,a,b)=>Math.max(a,Math.min(b,v));
const fmt=v=>{
  if(Math.abs(v-Math.round(v))<0.005) return String(Math.round(v));
  const s=v.toFixed(2);
  return s.endsWith('0')?v.toFixed(1):s;
};

/* ---------- 参数 ---------- */
const P={g:10,m0:0.1,M:2,mu:0.2,mu2:0.2,topOn:false,m1:3,sand:0};
const PRESETS={
  c1:{M:2,mu:0.2,mu2:0.2,topOn:false,m1:3,sand:0},
  c2:{M:2,mu:0.2,mu2:0.2,topOn:true,m1:3,sand:0},
  c3:{M:2,mu:0.2,mu2:0.2,topOn:false,m1:3,sand:0.5}
};
const S={preset:'c1',target:'M',t:0,playing:false,speed:0.35};
const X0=-0.95, S_MAX=0.75, PULLEY_R=0.19, MU_S=1.125;

/* ---------- 物理 ---------- */
function physics(){
  const g=P.g, M=P.M, m1=P.topOn?P.m1:0, mb=P.m0+P.sand;
  const N=(M+m1)*g;
  const fmax=MU_S*P.mu*N;
  const fk=P.mu*N;
  const Gb=mb*g;
  const sliding=Gb>fmax+1e-9;
  const k=clamp(Math.cbrt(P.M/2),0.72,1.35);
  const kt=clamp(Math.cbrt((P.m1||3)/3),0.75,1.2);
  const bw=0.8*k, tw=0.5*kt;
  let aM=0,a1=0,T=Gb,f=Gb,slipTop=false,fTop=0,f2max=0;
  if(P.topOn) f2max=MU_S*P.mu2*m1*g;
  if(sliding){
    const a0=(Gb-fk)/(M+m1+mb);
    const need=m1*a0;
    if(P.topOn && need>f2max+1e-9){
      slipTop=true;
      fTop=P.mu2*m1*g;
      a1=P.mu2*g;
      aM=(Gb-fk-fTop)/(M+mb);
    } else {
      aM=a1=a0;
      fTop=need;
    }
    f=fk;
    T=mb*(g-aM);
  }
  let tEnd=0;
  if(sliding){
    tEnd=Math.min(3,Math.sqrt(2*S_MAX/Math.max(aM,1e-6)));
    if(slipTop){
      const relMax=Math.max(0.05,(bw-tw)/2);
      tEnd=Math.min(tEnd,Math.sqrt(2*relMax/Math.max(aM-a1,1e-6)));
    }
  }
  const t=sliding?clamp(S.t,0,tEnd):0;
  const sM=0.5*aM*t*t, s1=0.5*a1*t*t;
  return {
    g,M,m1,mb,N,fmax,fk,Gb,sliding,aM,a1,a:aM,T,f,tEnd,t,
    sM,s1,vM:aM*t,v1:a1*t,drop:sM,bx:X0+sM,topX:X0+s1,
    slipTop,fTop,f2max,k,kt,bw,tw
  };
}

/* ---------- 画布与相机 ---------- */
const canvas=document.getElementById('scene');
const ctx=canvas.getContext('2d');
const arrowCanvas=document.createElement('canvas');
const actx=arrowCanvas.getContext('2d');
const stage=document.getElementById('stage');
const labelLayer=document.getElementById('labels');
let W=0,H=0,DPR=1,focal=1,CX=0,CY=0;
const cam={yaw:0.62,pitch:0.37,dist:7.2,target:v3(0.06,0.12,0)};
const HOME={yaw:0.62,pitch:0.37,dist:7.2,target:v3(0.06,0.12,0)};

function resize(){
  const r=stage.getBoundingClientRect();
  W=Math.max(1,r.width); H=Math.max(1,r.height);
  DPR=Math.min(window.devicePixelRatio||1,2);
  canvas.width=Math.round(W*DPR); canvas.height=Math.round(H*DPR);
  canvas.style.width=W+'px'; canvas.style.height=H+'px';
  arrowCanvas.width=canvas.width; arrowCanvas.height=canvas.height;
  focal=(H*0.5)/Math.tan(19*Math.PI/180);
  CX=W*0.5; CY=H*0.5+(window.innerWidth<=820?22:0);
}
window.addEventListener('resize',resize);

let camBasis=null;
function updateCamera(){
  const cp=Math.cos(cam.pitch), sp=Math.sin(cam.pitch);
  const eye=v3(
    cam.target.x+cam.dist*cp*Math.sin(cam.yaw),
    cam.target.y+cam.dist*sp,
    cam.target.z+cam.dist*cp*Math.cos(cam.yaw)
  );
  const fwd=vnorm(vsub(cam.target,eye));
  const right=vnorm(vcross(fwd,v3(0,1,0)));
  const up=vcross(right,fwd);
  camBasis={eye,fwd,right,up};
}
function project(p){
  const d=vsub(p,camBasis.eye);
  const zc=vdot(d,camBasis.fwd);
  if(zc<=0.15) return null;
  const xc=vdot(d,camBasis.right), yc=vdot(d,camBasis.up);
  return {x:CX+xc/zc*focal, y:CY-yc/zc*focal, z:zc};
}

/* ---------- 几何 ---------- */
const COL={
  table:[104,118,142], leg:[46,55,73], metal:[152,164,186], pulley:[186,198,218],
  block:[70,128,244], top:[36,192,148], bucket:[230,152,36], rim:[248,186,92],
  sand:[222,188,118], shadow:[0,0,0]
};
const L1=vnorm(v3(0.36,0.92,0.55));
const L2=vnorm(v3(-0.55,0.35,-0.6));

function addBox(out,cx,cy,cz,w,h,d,col,opt={}){
  const x0=cx-w/2,x1=cx+w/2,y0=cy-h/2,y1=cy+h/2,z0=cz-d/2,z1=cz+d/2;
  const a=opt.alpha;
  out.push({p:[v3(x0,y0,z1),v3(x1,y0,z1),v3(x1,y1,z1),v3(x0,y1,z1)],n:v3(0,0,1),c:col,a});
  out.push({p:[v3(x1,y0,z0),v3(x0,y0,z0),v3(x0,y1,z0),v3(x1,y1,z0)],n:v3(0,0,-1),c:col,a});
  out.push({p:[v3(x1,y0,z1),v3(x1,y0,z0),v3(x1,y1,z0),v3(x1,y1,z1)],n:v3(1,0,0),c:col,a});
  out.push({p:[v3(x0,y0,z0),v3(x0,y0,z1),v3(x0,y1,z1),v3(x0,y1,z0)],n:v3(-1,0,0),c:col,a});
  out.push({p:[v3(x0,y1,z1),v3(x1,y1,z1),v3(x1,y1,z0),v3(x0,y1,z0)],n:v3(0,1,0),c:col,a});
  out.push({p:[v3(x0,y0,z0),v3(x1,y0,z0),v3(x1,y0,z1),v3(x0,y0,z1)],n:v3(0,-1,0),c:col,a});
}
function addQuad(out,pts,n,col,alpha){
  out.push({p:pts.map(p=>v3(p[0],p[1],p[2])),n,c:col,a:alpha});
}
function addCyl(out,pa,pb,r,col,seg=12,caps=true,opt={}){
  const axis=vnorm(vsub(pb,pa));
  const ref=Math.abs(axis.y)>0.9?v3(1,0,0):v3(0,1,0);
  const u=vnorm(vcross(axis,ref)), w=vcross(axis,u);
  const A=[],B=[];
  for(let i=0;i<seg;i++){
    const th=i/seg*Math.PI*2;
    const off=vadd(vmul(u,Math.cos(th)*r),vmul(w,Math.sin(th)*r));
    A.push(vadd(pa,off)); B.push(vadd(pb,off));
  }
  for(let i=0;i<seg;i++){
    const j=(i+1)%seg, th=(i+0.5)/seg*Math.PI*2;
    const n=vnorm(vadd(vmul(u,Math.cos(th)),vmul(w,Math.sin(th))));
    out.push({p:[A[i],A[j],B[j],B[i]],n,c:col,a:opt.alpha});
  }
  if(caps){
    out.push({p:A.slice().reverse(),n:vmul(axis,-1),c:col,a:opt.alpha});
    out.push({p:B,n:axis,c:col,a:opt.alpha});
  }
}
function addCone(out,base,tip,r,col,seg=10,opt={}){
  const axis=vnorm(vsub(tip,base));
  const ref=Math.abs(axis.y)>0.9?v3(1,0,0):v3(0,1,0);
  const u=vnorm(vcross(axis,ref)), w=vcross(axis,u);
  const ring=[];
  for(let i=0;i<seg;i++){
    const th=i/seg*Math.PI*2;
    ring.push(vadd(base,vadd(vmul(u,Math.cos(th)*r),vmul(w,Math.sin(th)*r))));
  }
  for(let i=0;i<seg;i++){
    const j=(i+1)%seg, th=(i+0.5)/seg*Math.PI*2;
    const n=vnorm(vadd(vmul(u,Math.cos(th)),vmul(w,Math.sin(th))));
    out.push({p:[ring[i],ring[j],tip],n,c:col,a:opt.alpha});
  }
  out.push({p:ring.slice().reverse(),n:vmul(axis,-1),c:col,a:opt.alpha});
}

/* ---------- 绘制 ---------- */
function shade(col,b,a){
  const r=clamp(col[0]*b,0,255)|0, g=clamp(col[1]*b,0,255)|0, bl=clamp(col[2]*b,0,255)|0;
  return a==null?`rgb(${r},${g},${bl})`:`rgba(${r},${g},${bl},${a})`;
}
function drawFaces(faces,c=ctx){
  const list=[];
  const eye=camBasis.eye;
  for(const f of faces){
    /* 背面剔除：法线朝外的封闭体只看正面，避免排序错乱导致的“破面” */
    if(f.n){
      let cx=0,cy=0,cz=0;
      for(const p of f.p){ cx+=p.x; cy+=p.y; cz+=p.z; }
      const n=f.p.length;
      const vx=cx/n-eye.x, vy=cy/n-eye.y, vz=cz/n-eye.z;
      if(f.n.x*vx+f.n.y*vy+f.n.z*vz>=0) continue;
    }
    let z=0, ok=true; const pts=[];
    for(const p of f.p){
      const q=project(p);
      if(!q){ok=false;break;}
      pts.push(q); z+=q.z;
    }
    if(!ok) continue;
    list.push({f,pts,z:z/f.p.length});
  }
  list.sort((a,b)=>b.z-a.z);
  for(const it of list){
    const f=it.f, pts=it.pts;
    let b=0.46+0.56*Math.max(0,vdot(f.n,L1))+0.10*Math.max(0,vdot(f.n,L2));
    b=clamp(b,0.18,1.12);
    const col=shade(f.c,b,f.a);
    c.beginPath();
    c.moveTo(pts[0].x,pts[0].y);
    for(let i=1;i<pts.length;i++) c.lineTo(pts[i].x,pts[i].y);
    c.closePath();
    c.fillStyle=col; c.fill();
    c.strokeStyle=col; c.lineWidth=0.8; c.stroke();
  }
}
function drawGrid(){
  const y=-1.62;
  ctx.lineWidth=1;
  for(let i=-5;i<=5;i++){
    ctx.strokeStyle=`rgba(96,146,220,${i%2===0?0.075:0.038})`;
    ctx.beginPath();
    let a=project(v3(i*0.7,y,-4.2)), b=project(v3(i*0.7,y,4.2));
    if(a&&b){ctx.moveTo(a.x,a.y);ctx.lineTo(b.x,b.y);}
    a=project(v3(-5,y,i*0.7)); b=project(v3(5,y,i*0.7));
    if(a&&b){ctx.moveTo(a.x,a.y);ctx.lineTo(b.x,b.y);}
    ctx.stroke();
  }
}
function drawString(points){
  ctx.save();
  ctx.lineJoin='round'; ctx.lineCap='round';
  ctx.beginPath();
  let started=false;
  for(const p of points){
    const q=project(p);
    if(!q){started=false;continue;}
    if(!started){ctx.moveTo(q.x,q.y);started=true;}
    else ctx.lineTo(q.x,q.y);
  }
  ctx.strokeStyle='rgba(0,0,0,.45)'; ctx.lineWidth=4.4; ctx.stroke();
  ctx.strokeStyle='#dde7f7'; ctx.lineWidth=2; ctx.stroke();
  ctx.restore();
}


function convexHull2(points){
  const pts=[...points].sort((a,b)=>a.x-b.x||a.y-b.y);
  const cross=(o,a,b)=>(a.x-o.x)*(b.y-o.y)-(a.y-o.y)*(b.x-o.x);
  const lower=[];
  for(const p of pts){
    while(lower.length>=2&&cross(lower[lower.length-2],lower[lower.length-1],p)<=0) lower.pop();
    lower.push(p);
  }
  const upper=[];
  for(let i=pts.length-1;i>=0;i--){
    const p=pts[i];
    while(upper.length>=2&&cross(upper[upper.length-2],upper[upper.length-1],p)<=0) upper.pop();
    upper.push(p);
  }
  lower.pop(); upper.pop();
  return lower.concat(upper);
}
function fillSilhouette(c,b){
  const pts=[];
  for(const sx of [-0.5,0.5]) for(const sy of [-0.5,0.5]) for(const sz of [-0.5,0.5]){
    const q=project(v3(b.cx+sx*b.w,b.cy+sy*b.h,b.cz+sz*b.d));
    if(q) pts.push({x:q.x,y:q.y});
  }
  if(pts.length<3) return;
  const hull=convexHull2(pts);
  c.beginPath(); c.moveTo(hull[0].x,hull[0].y);
  for(let i=1;i<hull.length;i++) c.lineTo(hull[i].x,hull[i].y);
  c.closePath(); c.fill();
}

/* ---------- 标签 ---------- */
const labelPool=new Map();
function getLabel(id){
  let el=labelPool.get(id);
  if(!el){ el=document.createElement('div'); el.className='lbl'; labelLayer.appendChild(el); labelPool.set(id,el); }
  return el;
}
function applyLabels(list){
  for(const el of labelPool.values()) el.classList.remove('on');
  const ordered=[...list].sort((a,b)=>(a.cls==='obj'?0:1)-(b.cls==='obj'?0:1));
  const placed=[];
  for(const L of ordered){
    const q=project(L.pos);
    if(!q) continue;
    const el=getLabel(L.id);
    el.textContent=L.text;
    el.className='lbl on '+(L.cls||'');
    el.dataset.key=L.key||'';
    if(L.color){ el.style.color=L.color; el.style.borderColor='rgba(255,255,255,.14)'; }
    else { el.style.color=''; el.style.borderColor=''; }
    let x=q.x+(L.ox||0), y=q.y+(L.oy||0);
    const w=el.offsetWidth||70, h=el.offsetHeight||22;
    let tries=0;
    while(tries<8 && placed.some(p=>Math.abs(p.x-x)<(p.w+w)/2-3 && Math.abs(p.y-y)<(p.h+h)/2-3)){
      y+=18; tries++;
    }
    placed.push({x,y,w,h});
    el.style.transform=`translate(${x.toFixed(1)}px,${y.toFixed(1)}px) translate(-50%,-50%)`;
  }
}

/* ---------- 场景 ---------- */
const FCOL={G:'#ff6b6b',N:'#4ade80',T:'#38bdf8',S:'#fbbf24',K:'#f472b6',X:'#94a3b8'};
const FC={G:[255,107,107],N:[74,222,128],T:[56,189,248],S:[251,191,36],K:[244,114,182],X:[148,163,184]};

function arrow(out,labels,o,dir,len,col,text,textCol,opt={}){
  const d=vnorm(dir);
  const head=Math.min(0.17,Math.max(0.10,len*0.38));
  const shaft=Math.max(0.025,len-head);
  addCyl(out,o,vadd(o,vmul(d,shaft)),opt.r||0.027,col,8,true,{alpha:opt.alpha});
  addCone(out,vadd(o,vmul(d,shaft)),vadd(o,vmul(d,len)),opt.hr||0.068,col,10,{alpha:opt.alpha});
  if(text) labels.push({id:opt.id,key:opt.id,text,color:textCol,pos:vadd(o,vmul(d,len)),ox:d.x*16,oy:-d.y*16,cls:'force'});
}

function buildScene(st){
  const FT=[],FS=[],FM=[],FTop=[],FRig=[],FB=[],arrows=[],labels=[];
  const bx=st.bx, bw=0.8*st.k, bh=0.55*st.k, bd=0.8*st.k;
  const all=S.target==='all';

  addBox(FT,-0.325,-0.09,0,3.25,0.18,1.70,COL.table);
  for(const lx of [-1.82,1.17]) for(const lz of [-0.72,0.72]) addBox(FT,lx,-0.84,lz,0.085,1.32,0.085,COL.leg);
  addQuad(FS,[[bx-0.5*bw,0.004,-0.5*bd],[bx+0.5*bw,0.004,-0.5*bd],[bx+0.5*bw,0.004,0.5*bd],[bx-0.5*bw,0.004,0.5*bd]],
    v3(0,1,0),COL.shadow,0.30);

  addBox(FRig,1.27,0.17,0,0.07,0.34,0.07,COL.metal);
  addBox(FRig,1.385,0.34,0,0.23,0.05,0.05,COL.metal);
  addCyl(FRig,v3(1.50,0.34,-0.045),v3(1.50,0.34,0.045),PULLEY_R,COL.pulley,22,true);
  addCyl(FRig,v3(1.50,0.34,0.045),v3(1.50,0.34,0.07),0.045,COL.metal,10,true);
  for(let k=0;k<2;k++){
    const th2=st.s/PULLEY_R+k*Math.PI/2;
    addCyl(FRig,v3(1.50,0.34,0.047),v3(1.50+0.15*Math.cos(th2),0.34+0.15*Math.sin(th2),0.047),0.01,COL.metal,6,true);
  }

  addBox(FM,bx,bh/2,0,bw,bh,bd,COL.block);
  const tw=0.5*st.kt, th=0.30*st.kt;
  if(P.topOn) addBox(FTop,st.topX,bh+th/2,0,tw,th,tw,COL.top);

  const bTop=-0.05-st.drop, bH=0.34, bR=0.18, bX=1.69;
  addCyl(FB,v3(bX,bTop-bH,0),v3(bX,bTop,0),bR,COL.bucket,18,true);
  addCyl(FB,v3(bX,bTop-0.028,0),v3(bX,bTop,0),bR+0.014,COL.rim,18,true);
  if(P.sand>0.001){
    const frac=clamp(P.sand/1,0.05,1);
    addCone(FB,v3(bX,bTop-0.02,0),v3(bX,bTop+0.04+0.09*frac,0),0.10+0.055*frac,COL.sand,16,{});
  }

  const yS=0.34, arc=[];
  for(let i=0;i<=20;i++){
    const t=Math.PI-(Math.PI*i/20);
    arc.push(v3(1.50+PULLEY_R*Math.cos(t),yS+PULLEY_R*Math.sin(t),0));
  }
  const stringPts=[v3(bx+bw/2,yS,0),arc[0],...arc.slice(1),v3(bX,yS,0),v3(bX,bTop)];

  const tag=all?{M:'M：',T:'m₁：',B:'桶：'}:{M:'',T:'',B:''};
  const showM=all||S.target==='M';
  const showT=all||S.target==='top';
  const showB=all||S.target==='bucket';

  if(showM){
    const cy=bh/2;
    const V=[
      {k:'N',v:st.N,o:v3(bx-(P.topOn?bw*0.56:0),bh,0),d:v3(0,1,0),c:FC.N,cc:FCOL.N},
      {k:'G',v:st.M*st.g,o:v3(bx-bw*0.22,0,0),d:v3(0,-1,0),c:FC.G,cc:FCOL.G}
    ];
    if(P.topOn&&!all) V.push({k:'N₁',v:st.m1*st.g,o:v3(st.topX+tw*0.4,bh+0.36,0),d:v3(0,-1,0),c:[134,239,172],cc:'#86efac'});
    const H=[
      {k:'T',v:st.T,o:v3(bx+bw/2,yS,0),d:v3(1,0,0),c:FC.T,cc:FCOL.T},
      {k:'f',v:st.f,o:v3(bx-bw/2,cy-0.05,0),d:v3(-1,0,0),c:st.sliding?FC.K:FC.S,cc:st.sliding?FCOL.K:FCOL.S}
    ];
    const mV=Math.max(...V.map(x=>x.v),1), mH=Math.max(...H.map(x=>x.v),st.sliding?st.fmax:1);
    for(const x of V) arrow(arrows,labels,x.o,x.d,x.k==='N₁'?0.34:0.8*Math.max(x.v/mV,0.14),x.c,`${tag.M}${x.k} = ${fmt(x.v)} N`,x.cc,{id:'M_'+x.k});
    for(const x of H) arrow(arrows,labels,x.o,x.d,0.8*Math.max(x.v/mH,0.14),x.c,`${tag.M}${x.k} = ${fmt(x.v)} N`,x.cc,{id:'M_'+x.k});
    if(st.sliding&&!all) arrow(arrows,labels,v3(bx-bw/2,cy-0.16,0),v3(-1,0,0),0.8*(st.fmax/mH),[148,163,184],`f_max = ${fmt(st.fmax)} N`,FCOL.X,{id:'M_fmax',alpha:.45,r:.016,hr:.045});
    labels.push({id:'objM',text:`M ${P.M.toFixed(1)} kg`,pos:v3(bx-bw*0.5,0.02,bd*0.55),ox:-8,oy:20,cls:'obj'});
  }
  if(showT&&P.topOn){
    const tx=st.topX, cy=bh+th/2;
    const V=[
      {k:'N',v:st.m1*st.g,o:v3(tx,bh+th,0),d:v3(0,1,0),c:FC.N,cc:FCOL.N},
      {k:'G',v:st.m1*st.g,o:v3(tx-tw*0.2,bh,0),d:v3(0,-1,0),c:FC.G,cc:FCOL.G}
    ];
    const mV=Math.max(...V.map(x=>x.v),1);
    for(const x of V) arrow(arrows,labels,x.o,x.d,0.62*Math.max(x.v/mV,0.14),x.c,`${tag.T}${x.k} = ${fmt(x.v)} N`,x.cc,{id:'T_'+x.k});
    if(st.fTop>0.01){
      const fn=st.slipTop?'f₂':'f₁';
      const fc=st.slipTop?FC.K:FC.S, fcc=st.slipTop?FCOL.K:FCOL.S;
      arrow(arrows,labels,v3(tx+tw/2,bh+0.03,0),v3(1,0,0),0.55*clamp(Math.sqrt(st.fTop/5),0.2,1),fc,`${tag.T}${fn} = ${fmt(st.fTop)} N`,fcc,{id:'T_f'});
    }
    labels.push({id:'objT',text:`m₁ ${P.m1.toFixed(1)} kg`,pos:v3(tx-tw*0.5,bh+th,0.02),ox:0,oy:-16,cls:'obj'});
  }
  if(showB){
    const cy=bTop-bH/2;
    const V=[
      {k:'T',v:st.T,o:v3(bX,bTop,0),d:v3(0,1,0),c:FC.T,cc:FCOL.T},
      {k:'G',v:st.Gb,o:v3(bX,bTop-bH,0),d:v3(0,-1,0),c:FC.G,cc:FCOL.G}
    ];
    const mV=Math.max(...V.map(x=>x.v),1);
    for(const x of V) arrow(arrows,labels,x.o,x.d,0.8*Math.max(x.v/mV,0.14),x.c,`${tag.B}${x.k} = ${fmt(x.v)} N`,x.cc,{id:'B_'+x.k});
    labels.push({id:'objB',text:`桶 ${st.mb.toFixed(2)} kg`,pos:v3(bX,cy,0),ox:44,oy:0,cls:'obj'});
  }

  const masks=[
    {cx:bx,cy:bh/2,cz:0,w:bw,h:bh,d:bd}
  ];
  if(P.topOn) masks.push({cx:st.topX,cy:bh+th/2,cz:0,w:tw,h:th,d:tw});
  masks.push({cx:bX,cy:bTop-bH/2,cz:0,w:2*bR,h:bH,d:2*bR});

  return {FT,FS,FM,FTop,FRig,FB,arrows,labels,stringPts,masks};
}

/* ---------- 主循环 ---------- */
let bgGrad=null,lastW=0,lastH=0;
function draw(){
  updateCamera();
  if(!bgGrad||lastW!==W||lastH!==H){
    bgGrad=ctx.createLinearGradient(0,0,0,H);
    bgGrad.addColorStop(0,'#111d31'); bgGrad.addColorStop(.55,'#0b1424'); bgGrad.addColorStop(1,'#070b12');
    lastW=W; lastH=H;
  }
  ctx.setTransform(DPR,0,0,DPR,0,0);
  ctx.clearRect(0,0,W,H);
  ctx.fillStyle=bgGrad; ctx.fillRect(0,0,W,H);
  drawGrid();
  const st=physics();
  const {FT,FS,FM,FTop,FRig,FB,arrows,labels,stringPts,masks}=buildScene(st);
  drawFaces(FT); drawFaces(FS); drawFaces(FM); drawFaces(FTop); drawFaces(FRig); drawFaces(FB);
  drawString(stringPts);
  drawFaces(arrows);
  applyLabels(labels);
  return st;
}

let prev=performance.now();
function loop(now){
  const dt=Math.min(0.05,(now-prev)/1000); prev=now;
  const st=physics();
  if(st.sliding&&S.playing){
    S.t+=dt*S.speed;
    if(S.t>=st.tEnd){ S.t=st.tEnd; S.playing=false; syncTime(st); }
    else syncTime(st);
  }
  draw();
  requestAnimationFrame(loop);
}

/* ---------- 界面 ---------- */
const PR=[
  {id:'c1',t:'① 只挂空桶'},
  {id:'c2',t:'② 再加 3 kg'},
  {id:'c3',t:'③ 加 0.5 kg 沙'},
  {id:'custom',t:'自由设置'}
];
const elPresets=document.getElementById('presets');
elPresets.innerHTML=PR.map(p=>`<button class="preset" data-p="${p.id}">${p.t}</button>`).join('');
elPresets.querySelectorAll('.preset').forEach(b=>b.addEventListener('click',()=>{
  const id=b.dataset.p;
  if(id!=='custom'){ Object.assign(P,PRESETS[id]); S.preset=id; S.t=0; S.playing=physics().sliding; }
  else { S.preset='custom'; S.t=0; S.playing=false; }
  syncAll();
}));

const OB=[{id:'M',t:'物体 M'},{id:'top',t:'上方'},{id:'bucket',t:'桶'},{id:'all',t:'全部'}];
const elObjs=document.getElementById('objs');
elObjs.innerHTML=OB.map(o=>`<button data-o="${o.id}">${o.t}</button>`).join('');
elObjs.querySelectorAll('button').forEach(b=>b.addEventListener('click',()=>{
  S.target=b.dataset.o; syncAll();
}));

const elState=document.getElementById('state');
const elName=document.getElementById('stateName');
const elF=document.getElementById('stateF');
const elSub=document.getElementById('stateSub');
const elTable=document.getElementById('tbl');
const elPlay=document.getElementById('play');
const elTime=document.getElementById('rTime');
const elTVal=document.getElementById('tVal');
const rM=document.getElementById('rM'), rM1=document.getElementById('rM1'),
      rSand=document.getElementById('rSand'), rMu=document.getElementById('rMu');
const nM=document.getElementById('nM'), nM1=document.getElementById('nM1'),
      nSand=document.getElementById('nSand'), nMu=document.getElementById('nMu'), nMu2=document.getElementById('nMu2');
const topTgl=document.getElementById('topTgl');
const rMu2=document.getElementById('rMu2'), vMu2=document.getElementById('vMu2');
const calc=document.getElementById('calc'), calcTitle=document.getElementById('calcTitle'),
      calcBadge=document.getElementById('calcBadge'), calcBody=document.getElementById('calcBody'),
      calcClose=document.getElementById('calcClose');
let calcKey=null;

function markCustom(){
  S.preset='custom'; S.t=0; S.playing=false;
}

function bindRange(el,fn){ el.addEventListener('input',fn); el.addEventListener('change',fn); }
bindRange(rM,()=>{ P.M=rM.value/10; markCustom(); syncAll(); });
bindRange(rM1,()=>{ P.m1=rM1.value/10; markCustom(); syncAll(); });
bindRange(rSand,()=>{ P.sand=rSand.value/100; markCustom(); syncAll(); });
bindRange(rMu,()=>{ P.mu=rMu.value/100; markCustom(); syncAll(); });
bindRange(rMu2,()=>{ P.mu2=rMu2.value/100; markCustom(); syncAll(); });

function bindNum(el,key,min,max,step){
  el.addEventListener('focus',()=>{ try{el.select();}catch(e){} });
  el.addEventListener('change',()=>{
    let v=parseFloat(el.value);
    if(!isFinite(v)) v=P[key];
    v=clamp(v,min,max);
    v=Math.round(v/step)*step;
    P[key]=Number(v.toFixed(3));
    markCustom(); syncAll();
  });
}
bindNum(nM,'M',1,6,0.1);
bindNum(nM1,'m1',0.5,6,0.1);
bindNum(nSand,'sand',0,1,0.05);
bindNum(nMu,'mu',0.05,0.45,0.01);
bindNum(nMu2,'mu2',0.02,0.45,0.01);
topTgl.addEventListener('click',()=>{ P.topOn=!P.topOn; markCustom(); if(!P.topOn&&S.target==='top') S.target='M'; syncAll(); });

elPlay.addEventListener('click',()=>{
  const st=physics();
  if(!st.sliding) return;
  if(S.t>=st.tEnd-1e-4) S.t=0;
  S.playing=!S.playing;
  syncTime(st);
});
bindRange(elTime,()=>{
  const st=physics();
  S.t=elTime.value/1000*st.tEnd;
  S.playing=false;
  syncTime(st);
});

function syncTime(st){
  elPlay.textContent=S.playing?'❚❚':'▶';
  elTime.disabled=!st.sliding;
  document.getElementById('timeRow').style.display=st.sliding?'grid':'none';
  document.getElementById('staticTime').style.display=st.sliding?'none':'block';
  if(st.sliding){
    elTime.value=Math.round(S.t/Math.max(st.tEnd,1e-6)*1000);
    elTVal.textContent=`t = ${st.t.toFixed(2)} s，v = ${st.vM.toFixed(2)} m s⁻¹`;
    const setTxt=(id,val)=>{const el=document.getElementById(id); if(el) el.textContent=val;};
    setTxt('v_M',st.vM.toFixed(2)); setTxt('a_M',st.aM.toFixed(2));
    setTxt('v_top',st.v1.toFixed(2)); setTxt('a_top',st.a1.toFixed(2));
    setTxt('v_bucket',st.vM.toFixed(2)); setTxt('a_bucket',st.aM.toFixed(2));
  } else {
    elTVal.textContent='静止';
  }
  const warn=document.getElementById('warn');
  if(st.slipTop&&st.t>=st.tEnd-0.02){
    warn.style.display='block';
    warn.textContent='上方物块已滑到 M 的边缘，演示到此为止。';
  } else warn.style.display='none';
}

function syncAll(){
  if(!P.topOn&&S.target==='top') S.target='M';
  const st=physics();
  /* 预设高亮 */
  elPresets.querySelectorAll('.preset').forEach(b=>b.classList.toggle('on',b.dataset.p===S.preset));
  /* 参数控件 */
  rM.value=Math.round(P.M*10); nM.value=P.M.toFixed(1);
  rM1.value=Math.round(P.m1*10); rM1.disabled=!P.topOn; nM1.disabled=!P.topOn; nM1.value=P.m1.toFixed(1);
  topTgl.textContent=P.topOn?'有':'无'; topTgl.classList.toggle('on',P.topOn);
  rSand.value=Math.round(P.sand*100); nSand.value=P.sand.toFixed(2);
  rMu.value=Math.round(P.mu*100); nMu.value=P.mu.toFixed(2);
  rMu2.value=Math.round(P.mu2*100); rMu2.disabled=!P.topOn; nMu2.disabled=!P.topOn; nMu2.value=P.mu2.toFixed(2);
  /* 状态 */
  elState.className='state '+(st.sliding?'st-slide':'st-static');
  elName.textContent=st.sliding?'滑动':'静止';
  elF.textContent=`f = ${st.f.toFixed(1)} N`;
  elSub.innerHTML=st.sliding
    ? `G桶 = ${st.Gb.toFixed(1)} N &gt; f<sub>max</sub> = ${fmt(st.fmax)} N，a = ${st.aM.toFixed(2)} m s⁻²`+(st.slipTop?'，上方打滑':'')
    : `G桶 = ${st.Gb.toFixed(1)} N ≤ f<sub>max</sub> = ${fmt(st.fmax)} N`;
  /* 时间 */
  syncTime(st);
  /* 对象按钮 */
  elObjs.querySelectorAll('button').forEach(b=>{
    const id=b.dataset.o;
    b.classList.toggle('on',id===S.target);
    b.disabled=(id==='top'&&!P.topOn);
  });
  /* 表格 */
  const rows=[
    {id:'M',cells:['M',P.M.toFixed(1),st.vM.toFixed(2),st.aM.toFixed(2),fmt(st.M*st.g),fmt(st.N),fmt(st.T),fmt(st.f)],
     keys:[null,null,null,null,'M_G','M_N','M_T','M_f']}
  ];
  if(P.topOn) rows.push({id:'top',cells:['m₁',P.m1.toFixed(1),st.v1.toFixed(2),st.a1.toFixed(2),fmt(st.m1*st.g),fmt(st.m1*st.g),'—',fmt(st.fTop)],
     keys:[null,null,null,null,'T_G','T_N',null,'T_f']});
  rows.push({id:'bucket',cells:['桶',st.mb.toFixed(2),st.vM.toFixed(2),st.aM.toFixed(2),fmt(st.Gb),'—',fmt(st.T),'—'],
     keys:[null,null,null,null,'B_G',null,'B_T',null]});
  const head='<thead><tr><th>物体</th><th>m</th><th>v</th><th>a</th><th>G</th><th>N</th><th>T</th><th>f</th></tr></thead>';
  const body=rows.map(r=>{
    const on=(S.target===r.id||S.target==='all')?' class="on"':'';
    const c=r.cells;
    const tails=c.slice(4).map((x,i)=>{
      const k=r.keys[i+4];
      return k?`<td class="fc" data-key="${k}">${x}</td>`:`<td>${x}</td>`;
    }).join('');
    return `<tr${on}><td>${c[0]}</td><td>${c[1]}</td>`+
      `<td id="v_${r.id}">${c[2]}</td><td id="a_${r.id}">${c[3]}</td>`+tails+'</tr>';
  }).join('');
  elTable.innerHTML=head+'<tbody>'+body+'</tbody>';
  const warn=document.getElementById('warn');
  if(st.slipTop&&st.t>=st.tEnd-0.02){
    warn.style.display='block';
    warn.textContent='上方物块已滑到 M 的边缘，演示到此为止。';
  } else warn.style.display='none';
  if(calcKey&&!calc.hidden) renderCalc(calcKey,st);
  draw();
}

/* ---------- 计算说明 ---------- */
function forceInfo(key,st){
  const g=P.g, M=P.M, m1=P.topOn?P.m1:0, mb=st.mb;
  const mu=P.mu.toFixed(2), mu2=P.mu2.toFixed(2), A=st.aM.toFixed(2);
  if(key==='M_G') return {title:'物体 M：重力 G',badge:'已知',type:'known',
    lines:[`G = Mg = ${M.toFixed(1)} × ${g} = ${fmt(M*g)} N`,
           `M 与 g 都是已知量，直接算出。`]};
  if(key==='M_N') return {title:'物体 M：支持力 N',badge:'求出',type:'derived',
    lines:(P.topOn
      ? [`竖直方向平衡：N = G_M + N₁`,
         `= ${fmt(M*g)} + ${fmt(m1*g)} = ${fmt(st.N)} N`]
      : [`竖直方向平衡：N = G_M = ${fmt(st.N)} N`])};
  if(key==='M_N1') return {title:'上方物块对 M 的压力 N₁',badge:'求出',type:'derived',
    lines:[`N₁ = m₁g = ${m1.toFixed(1)} × ${g} = ${fmt(m1*g)} N`,
           `与 M 对上方物块的支持力是一对作用力与反作用力。`]};
  if(key==='M_T') return {title:'物体 M：绳拉力 T',badge:'求出',type:'derived',
    lines:(st.sliding
      ? [`桶加速下降：G桶 − T = m桶a`,
         `T = m桶(g − a) = ${mb.toFixed(2)} × (${g} − ${A}) = ${fmt(st.T)} N`]
      : [`桶静止，二力平衡：T = G桶`,
         `T = ${mb.toFixed(2)} × ${g} = ${fmt(st.T)} N`])};
  if(key==='M_f') return {title:'物体 M：摩擦力 f',badge:'求出',type:'derived',
    lines:(st.sliding
      ? [`M 滑动，用滑动摩擦定律`,
         `f = μ₁N = ${mu} × ${fmt(st.N)} = ${fmt(st.f)} N`]
      : [`G桶 ${fmt(st.Gb)} N ≤ f_max ${fmt(st.fmax)} N，M 保持静止`,
         `水平方向平衡：f = T = ${fmt(st.f)} N（静摩擦）`])};
  if(key==='M_fmax') return {title:'最大静摩擦 f_max',badge:'题目给定',type:'given',
    lines:[`题目给定 F_max = 4.5 N（M = 2 kg、N = 20 N 时）`,
           `本页按 f_max = 1.125 μ₁N 随正压力缩放：`,
           `= 1.125 × ${mu} × ${fmt(st.N)} = ${fmt(st.fmax)} N`]};
  if(key==='T_G') return {title:'上方物块：重力 G',badge:'已知',type:'known',
    lines:[`G = m₁g = ${m1.toFixed(1)} × ${g} = ${fmt(m1*g)} N`]};
  if(key==='T_N') return {title:'上方物块：支持力 N',badge:'求出',type:'derived',
    lines:[`N = m₁g = ${fmt(m1*g)} N`,
           `M 对上方物块的支持力。`]};
  if(key==='T_f') return {title:'上方物块：摩擦力',badge:'求出',type:'derived',
    lines:(st.slipTop
      ? [`所需静摩擦超过 f_max2，发生打滑`,
         `f₂ = μ₂m₁g = ${mu2} × ${m1.toFixed(1)} × ${g} = ${fmt(st.fTop)} N（滑动摩擦）`,
         `f_max2 = 1.125 μ₂m₁g = ${fmt(st.f2max)} N`]
      : (st.sliding
        ? [`随 M 一起加速，由静摩擦提供`,
           `f₁ = m₁a = ${m1.toFixed(1)} × ${A} = ${fmt(st.fTop)} N`,
           `f_max2 = 1.125 μ₂m₁g = ${fmt(st.f2max)} N，未超过`]
        : [`整体静止，水平方向不需要摩擦力 f = 0`]))};
  if(key==='B_G') return {title:'小桶：重力 G',badge:'已知',type:'known',
    lines:[`G = m桶g = ${mb.toFixed(2)} × ${g} = ${fmt(st.Gb)} N`]};
  if(key==='B_T') return {title:'小桶：绳拉力 T',badge:'求出',type:'derived',
    lines:(st.sliding
      ? [`加速下降：T = m桶(g − a)`,
         `= ${mb.toFixed(2)} × (${g} − ${A}) = ${fmt(st.T)} N`]
      : [`静止，T = G桶 = ${fmt(st.T)} N`])};
  return null;
}

function renderCalc(key,st){
  if(!P.topOn&&(key.charAt(0)==='T'||key==='M_N1')){ calc.hidden=true; calcKey=null; return; }
  const info=forceInfo(key,st);
  if(!info) return;
  calcKey=key;
  calcTitle.textContent=info.title;
  calcBadge.textContent=info.badge;
  calcBadge.className='badge '+info.type;
  calcBody.innerHTML=info.lines.map(x=>`<div class="cline">${x}</div>`).join('');
}
function openCalc(key,x,y){
  const st=physics();
  if(!forceInfo(key,st)) return;
  renderCalc(key,st);
  calc.hidden=false;
  const w=calc.offsetWidth||264, h=calc.offsetHeight||86;
  const px=(x==null)?innerWidth/2:x, py=(y==null)?innerHeight/2:y;
  let left=px-24, top=py-h-16;
  if(top<10) top=py+18;
  left=clamp(left,10,innerWidth-w-10);
  top=clamp(top,10,innerHeight-h-10);
  calc.style.left=left+'px';
  calc.style.top=top+'px';
}

elTable.addEventListener('click',e=>{
  const td=e.target.closest('td[data-key]');
  if(td&&td.dataset.key) openCalc(td.dataset.key,e.clientX,e.clientY);
});
labelLayer.addEventListener('click',e=>{
  const el=e.target.closest('.lbl');
  if(el&&el.dataset.key) openCalc(el.dataset.key,e.clientX,e.clientY);
});
calcClose.addEventListener('click',()=>{ calc.hidden=true; });
document.addEventListener('pointerdown',e=>{
  if(calc.hidden) return;
  if(calc.contains(e.target)) return;
  if(e.target.closest('td[data-key]')||e.target.closest('.lbl')) return;
  calc.hidden=true;
});

/* 相机交互 */
let drag=null, pinch=null;
const ptrs=new Map();
const ptrDist=()=>{ const a=[...ptrs.values()]; return Math.hypot(a[0].x-a[1].x,a[0].y-a[1].y); };
canvas.addEventListener('pointerdown',e=>{
  ptrs.set(e.pointerId,{x:e.clientX,y:e.clientY});
  try{ canvas.setPointerCapture(e.pointerId); }catch(err){}
  if(ptrs.size===1){ drag={x:e.clientX,y:e.clientY,yaw:cam.yaw,pitch:cam.pitch}; pinch=null; }
  else if(ptrs.size===2){ drag=null; pinch={d0:cam.dist,dist:Math.max(20,ptrDist())}; }
});
canvas.addEventListener('pointermove',e=>{
  if(!ptrs.has(e.pointerId)) return;
  ptrs.set(e.pointerId,{x:e.clientX,y:e.clientY});
  if(ptrs.size>=2&&pinch){
    cam.dist=clamp(pinch.d0*pinch.dist/Math.max(20,ptrDist()),4.5,17);
    return;
  }
  if(!drag) return;
  cam.yaw=drag.yaw-(e.clientX-drag.x)*0.006;
  cam.pitch=clamp(drag.pitch+(e.clientY-drag.y)*0.004,0.06,1.18);
});
function endPtr(e){
  ptrs.delete(e.pointerId);
  if(ptrs.size<2) pinch=null;
  if(ptrs.size===0) drag=null;
}
canvas.addEventListener('pointerup',endPtr);
canvas.addEventListener('pointercancel',endPtr);
canvas.addEventListener('wheel',e=>{ e.preventDefault(); cam.dist=clamp(cam.dist*(1+Math.sign(e.deltaY)*0.07),6.2,17); },{passive:false});
canvas.addEventListener('dblclick',()=>{ cam.yaw=HOME.yaw; cam.pitch=HOME.pitch; cam.dist=HOME.dist; });

document.addEventListener('keydown',e=>{
  if(e.code==='Space'&&!elPlay.disabled){ e.preventDefault(); elPlay.click(); }
});

/* 启动 */
resize();
syncAll();
if(window.innerWidth<=820){
  cam.dist=6.3; cam.pitch=0.38; cam.target.y=-0.10;
  HOME.dist=6.3; HOME.pitch=0.38; HOME.target.y=-0.10;
}
(function(){
  const q=new URLSearchParams(location.search);
  if(q.has('yaw')) cam.yaw=parseFloat(q.get('yaw'));
  if(q.has('pitch')) cam.pitch=parseFloat(q.get('pitch'));
  if(q.has('dist')) cam.dist=parseFloat(q.get('dist'));
})();
requestAnimationFrame(loop);
</script>
</body>
</html>
