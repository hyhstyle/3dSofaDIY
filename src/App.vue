<template>
  <div class="home">
      <div class="header">
        <div class="title">
          3D家具个性化定制应用
        </div>
      </div>
       <div class="content">
             <!-- 三维场景 -->
          <canvas id="canvasDom" class="canvas-container" ref="canvasDom"> </canvas>
          <div class="menuAside">
              <div class="title">选择颜色</div>
              <div class="colorList">
                <div class="item" v-for="(item,index) in colors" 
                @click="selectColor(item)"
                :key="index" :style="{'background':item.color?'#'+item.color:'',
                  'backgroundImage':item.texture?'url(' + item.texture + ')':''
                }">
                </div>
              </div>
          </div>
      </div>
    <div class="loading" id="js-loader">
      <Loader/>
    </div>
    <!-- 部件卡片 -->
    <div class="options">
       <div v-for="(item,index) in INITIAL_MAP" :key="index"
        class="option " 
        :class="activeOption==item.childID?'--is-active':''"
        :data-option="item.childID"
        @click="selectOption(item)"
        >
        <img :src="item.imgurl" alt="" />
      </div>
    </div>

    <div class="controls" >
      <div class="info">
        <div class="info__message">
            <p><strong>1、</strong> 左上角选定沙发部位 &nbsp;<strong> 2、</strong> 侧边栏色卡点击实现换装 </p>
          <p> 
            <strong>3、拾取沙发&nbsp;</strong> 可旋转;
            <strong>&nbsp;滚轮缩放&nbsp;</strong> 可放大缩小;
            <strong>&nbsp;色卡滑动&nbsp;</strong> 查看更多
          </p>
        </div>
      </div>
      </div>
    </div>
    
  </template>
  
  <script setup>
  import * as THREE from "three";
  import Loader from "./components/Loader.vue"
  import { onMounted, reactive, ref } from "vue";
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
  import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader";
  import { RoomEnvironment } from "three/examples/jsm/environments/RoomEnvironment.js";
    var theModel; //定义模型
    let canvasDom = ref(null);
    var camera; //定义相机
    var activeOption =ref("Object_6");   
    const MODEL_PATH = "./static/model/sofa_chair.glb";        //沙发模型地址
    const BACKGROUND_COLOR = 0xf1f1f1;                  //场景背景
    const INITIAL_MTL = new THREE.MeshPhongMaterial({   // 定义一个默认沙发材质material
      color: 0xf1f1f1,
      shininess: 10,
    });
    const INITIAL_MAP = [   // 沙发模型各个模块及视角
      { childID: "Object_6", mtl: INITIAL_MTL ,position:{x:0,y:1.2,z:4},imgurl:'./static/img/modeltype/front.png'},
      { childID: "Object_4", mtl: INITIAL_MTL ,position:{x:-4.16,y:0.17,z:0.167},imgurl:'./static/img/modeltype/back.png'},
      { childID: "Object_8", mtl: INITIAL_MTL ,position:{x:-0.06,y:1.88,z:3.26},imgurl:'./static/img/modeltype/down.png'},
      { childID: "Object_10", mtl: INITIAL_MTL,position:{x:-2.78,y:2.83,z:3.70},imgurl:'./static/img/modeltype/leg.png'},
      { childID: "Object_12", mtl: INITIAL_MTL,position:{x:0.6,y:0.6,z:1.52},imgurl:'./static/img/modeltype/pillow.png'},
    ];
    //色卡
    const colors = [
      {
        color: "A3AB84",
      },
  
      {
        color: "D6CCB1",
      },
  
      {
        color: "F8D5C4",
      },
  
      {
        color: "A3AE99",
      },
  
      {
        color: "EFF2F2",
      },
  
      {
        color: "B0C5C1",
      },
  
      {
        color: "8B8C8C",
      },
  
      {
        color: "565F59",
      },
  
      {
        color: "CB304A",
      },
  
      {
        color: "FED7C8",
      },
  
      {
        color: "C7BDBD",
      },
  
      {
        color: "3DCBBE",
      },
  
      {
        color: "264B4F",
      },
  
      {
        color: "389389",
      },
  
      {
        color: "85BEAE",
      },
  
      {
        color: "F2DABA",
      },
  
      {
        color: "F2A97F",
      },
  
      {
        color: "D85F52",
      },
  
      {
        color: "D92E37",
      },
  
      {
        color: "FC9736",
      },
  
      {
        color: "F7BD69",
      },
  
      {
        color: "A4D09C",
      },
  
      {
        color: "4C8A67",
      },
  
      {
        color: "25608A",
      },
  
      {
        color: "75C8C6",
      },
  
      {
        color: "F5E4B7",
      },
  
      {
        color: "E69041",
      },
  
      {
        color: "E56013",
      },
  
      {
        color: "11101D",
      },
  
      {
        color: "630609",
      },
  
      {
        color: "C9240E",
      },
  
      {
        color: "EC4B17",
      },
  
      {
        color: "281A1C",
      },
  
      {
        color: "4F556F",
      },
  
      {
        color: "64739B",
      },
  
      {
        color: "CDBAC7",
      },
  
      {
        color: "946F43",
      },
  
      {
        color: "66533C",
      },
  
      {
        color: "173A2F",
      },
  
      {
        color: "153944",
      },
  
      {
        color: "27548D",
      },
  
      {
        color: "438AAC",
      },   {
        color: "131417",
      },
  
      {
        color: "374047",
      },
  
      {
        color: "5f6e78",
      },
  
      {
        color: "7f8a93",
      },
  
      {
        color: "97a1a7",
      },
  
      {
        color: "acb4b9",
      },
  
      {
        color: "DF9998",
      },
  
      {
        color: "7C6862",
      },
          {
        texture: "./img/wood_.jpg",
        size: [2, 2, 2],
        shininess: 60,
      },
  
      {
        texture: "./img/fabric_.jpg",
        size: [4, 4, 4],
        shininess: 0,
      },
  
      {
        texture: "./img/pattern_.jpg",
        size: [8, 8, 8],
        shininess: 10,
      },
  
      {
        texture: "./img/denim_.jpg",
        size: [3, 3, 3],
        shininess: 0,
      },
  
      {
        texture: "./img/quilt_.jpg",
        size: [6, 6, 6],
        shininess: 0,
      }
    ];
  
    //  给模型各部位赋材质
    const initColor=(parent, type, mtl)=> {
      parent.traverse((o) => {
        if (o.isMesh) {
          if (o.name.includes(type)) {
            o.material = mtl;
            o.nameID = type; // 设置一个属性来标识此对象
          }
        }
      });
    }
    //设置相机视角+动画
  const setCameraPosition=(newPosition,cameraPosition)=> {
      return new TWEEN.Tween(cameraPosition)
        .to(newPosition, 1000)
        .easing(TWEEN.Easing.Cubic.Out)
        .start();
    }
  //选择子模型
  const selectOption=(item)=> {
      activeOption.value=item.childID
      console.log(camera.position)
      //设置不同模块的视角
      INITIAL_MAP.forEach(ele=>{
        if(ele.childID==item.childID){
               setCameraPosition(ele.position,camera.position)
        }
      })   
    }
    // 点击色卡给选中的子模型切换贴图或颜色
  const selectColor=(item)=>{
      let color = item
      let new_mtl;
  
      if (color.texture) {
        let txt = new THREE.TextureLoader().load(color.texture);
  
        txt.repeat.set(color.size[0], color.size[1], color.size[2]);
        txt.wrapS = THREE.RepeatWrapping;
        txt.wrapT = THREE.RepeatWrapping;
  
        new_mtl = new THREE.MeshPhongMaterial({
          map: txt,
          shininess: color.shininess ? color.shininess : 10,
        });
      } else {
        new_mtl = new THREE.MeshPhongMaterial({
          color: parseInt("0x" + color.color),
          shininess: color.shininess ? color.shininess : 10,
        });
      }
  
      theModel.traverse((o) => {
        if (o.isMesh && o.nameID != null) {
          if (o.nameID == activeOption.value) {
            o.material = new_mtl;
          }
        }
      });
  }
  
  
  onMounted(() => {
    const LOADER = document.getElementById("js-loader");
    var loaded = false;
  
   
    // 初始化场景
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(BACKGROUND_COLOR);  // 设置背景
    scene.fog = new THREE.Fog(BACKGROUND_COLOR, 20, 100);
  
  
    // 初始化 renderer
    const canvas = document.querySelector("#canvasDom");
    const renderer = new THREE.WebGLRenderer({ canvas, antialias: true });
    renderer.shadowMap.enabled = true;
    renderer.setPixelRatio(window.devicePixelRatio);
    // document.body.appendChild(renderer.domElement);
  
    // 初始化 camera
    camera = new THREE.PerspectiveCamera(
      50,
      // window.innerWidth / window.innerHeight,
       canvasDom.value.clientWidth /  canvasDom.value.clientHeight,
      0.1,
      1000
    );
    camera.position.set( 0, 1.2,4 );
  
    // 初始化GLTF模型loader
    const loader = new GLTFLoader();
    const dracoLoader = new DRACOLoader();
    dracoLoader.setDecoderPath("./draco/gltf/");
    loader.setDRACOLoader(dracoLoader);
  
    loader.load(
      MODEL_PATH,
      function (gltf) {
        theModel = gltf.scene;
        theModel.traverse((o) => { //此函数可以遍历出模型包含的各个子节点
          console.log(o.name,'节点名称');
          if (o.isMesh) {
            o.castShadow = true;
            o.receiveShadow = true;
          }
        });
  
        //设置models初始化大小
        theModel.scale.set(1.2, 1.2, 1.2);
        // 设置models位置偏移
        // theModel.rotation.y = Math.PI;
        theModel.position.y = -0.55;
        // theModel.position.y = 0;
  
  
        // 设置模型初始化材质及贴图 initial textures
        for (let object of INITIAL_MAP) {
          initColor(theModel, object.childID, object.mtl);
        }
  
        // Add the model to the scene
        scene.add(theModel);
        // Remove the loader
        setTimeout(()=>{
           LOADER.remove();
        },1000)
       
      },
      undefined,
      function (error) {
        console.error(error);
      }
    );
  
  
    // 设置场景灯光
    var hemiLight = new THREE.HemisphereLight(0xffffff, 0xffffff, 0.61);
    hemiLight.position.set(0, 50, 0);
    scene.add(hemiLight);
    var dirLight = new THREE.DirectionalLight(0xffffff, 0.54);
    dirLight.position.set(-8, 12, 8);
    dirLight.castShadow = true;
    dirLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
    scene.add(dirLight);
  
    
    // 给场景加个地板
    var floorGeometry = new THREE.PlaneGeometry(5000, 5000, 1, 1);
    var floorMaterial = new THREE.MeshPhongMaterial({
      color: 0xeeeeee,
      shininess: 0,
    });
    var floor = new THREE.Mesh(floorGeometry, floorMaterial);
    floor.rotation.x = -0.5 * Math.PI;
    floor.receiveShadow = true;
    floor.position.y = -1;
    scene.add(floor);
  
    // 设置相机控制 controls
    var controls = new OrbitControls(camera, renderer.domElement);
    controls.maxPolarAngle = Math.PI / 2;
    controls.minPolarAngle = Math.PI / 3;
    controls.enableDamping = true;
    controls.enablePan = false;
    controls.dampingFactor = 0.1;
    controls.autoRotate = false; // 
    controls.autoRotateSpeed = 0.2; // 30
  
    function animate() {
      TWEEN.update(); //tween更新
      controls.update();
      renderer.render(scene, camera);
      requestAnimationFrame(animate);
  
      if (resizeRendererToDisplaySize(renderer)) {
        const canvas = renderer.domElement;
        camera.aspect = canvas.clientWidth / canvas.clientHeight;
        camera.updateProjectionMatrix();
      }
  
      if (theModel != null && loaded == false) {
        // initialRotation();
        // DRAG_NOTICE.classList.add("start");
      }
    }
    animate();
  
    // 自适应设备
    function resizeRendererToDisplaySize(renderer) {
      const canvas = renderer.domElement;
      var width = window.innerWidth;
      var height = window.innerHeight;
      var canvasPixelWidth = canvas.width / window.devicePixelRatio;
      var canvasPixelHeight = canvas.height / window.devicePixelRatio;
  
      renderer.setPixelRatio( window.devicePixelRatio );
      renderer.setSize(document.querySelector('.canvas-container').clientWidth,document.querySelector('.canvas-container').clientHeight);
      // const needResize =
      //   canvasPixelWidth !== width || canvasPixelHeight !== height;
      // if (needResize) {
      //   renderer.setSize(width, height, false);
      // }
      // return needResize;
    }
  
  
    // 是否自动旋转模型
    let initRotate = 0;
    function initialRotation() {
      initRotate++;
      if (initRotate <= 120) {
        theModel.rotation.y += Math.PI / 60;
      } else {
        loaded = true;
      }
    }
  
  
  });
  </script>
  
  <style>
  #app {
    width: 100%;
    height: 100%;
  }
  .home{
    width: 100%;
    height: 100%;
  }
  .header{
    height:60px;
    width: 100%;
    background:#333;
  }
  .header .title{
    line-height: 60px;
    color: #fff;
    padding:0 20px;
    font-size: 20px;
  }
  .content{
    height: calc(100% - 60px);
    width: 100%;
    display: flex;
  }
  .canvas-container{
    flex: 1;
  }
  
  .menuAside{
    width: 480px;
    background:#fff;
    padding: 20px;
    overflow-y:auto;
  }
  .menuAside .title{
    margin-bottom: 10px;
    font-size: 18px;
  }
  .menuAside .colorList{
  display: grid;
  
  justify-content: space-between;
  
  grid-template-columns: repeat(auto-fill,100px);
  
  grid-gap: 0 1px;
  
  }
  .menuAside .colorList .item{
    width: 100px;
    height: 100px;
    border:5px solid #fff;
    margin-bottom:20px;
    border-radius: 5px;
    box-shadow: 1px 1px 1px 1px #ddd;
    cursor: pointer;
  }
  .menuAside .colorList .item:hover{
    transform: scale(1.1);
    transition-duration: 0.8s;
  }
  
  
  
  
  .loading {
    position: fixed;
    z-index: 50;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background: #f1f1f1;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  
  
  </style>
  