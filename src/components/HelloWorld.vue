<template>
  <div id="scene-container" ref="sceneContainer"></div>
</template>

<script>
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import Stats from 'stats.js'
import { TweenMax} from "gsap";
window.THREE = THREE;
export default {
  name: 'HelloWorld',
  data () {
    return {
      container: null,
      scene: null,
      camera: null,
      controls: null,
      renderer: null,
      stats: null,
      xMauseValue:null,
      yMauseValue:null
    }
  },
  methods: {
    init () {
          if (window.Event) {
            document.captureEvents(Event.MOUSEMOVE);
            }
          document.onmousemove = this.getCursorXY;

      this.scene = new THREE.Scene()
      window.scene = this.scene;

      // this.scene.rotation(-0.40,3.14,0);
      // this.scene.scale(3.32);
      // set container
      this.container = this.$refs.sceneContainer

      // add stats
      this.stats = new Stats()
      this.container.appendChild(this.stats.dom)

      // add camera
      const fov = 60 // Field of view
      const aspect = this.container.clientWidth / this.container.clientHeight
      const near = 0.1 // the near clipping plane
      const far = 30 // the far clipping plane
      const camera = new THREE.PerspectiveCamera(fov, aspect, near, far)
      camera.position.set(-1, 1, 3)
      this.camera = camera

      // create scene
    

      // this.scene.background = new THREE.Color('skyblue')

      // add lights
      const ambientLight = new THREE.HemisphereLight(
        0xffffff, // bright sky color
        0x222222, // dim ground color
        1 // intensity
      )
      const mainLight = new THREE.DirectionalLight(0xffffff, 4.0)
      mainLight.position.set(10, 10, 10)
      this.scene.add(ambientLight, mainLight)

      // add controls
      this.controls = new OrbitControls(this.camera, this.container)

      // create renderer
      this.renderer = new THREE.WebGLRenderer({ antialias: true })
      this.renderer.setSize(this.container.clientWidth, this.container.clientHeight)
      this.renderer.setPixelRatio(window.devicePixelRatio)
      this.renderer.gammaFactor = 2.2
      this.renderer.outputEncoding = THREE.sRGBEncoding
      this.renderer.physicallyCorrectLights = true
      this.container.appendChild(this.renderer.domElement)

      // set aspect ratio to match the new browser window aspect ratio
      this.camera.aspect = this.container.clientWidth / this.container.clientHeight
      this.camera.updateProjectionMatrix()
      this.renderer.setSize(this.container.clientWidth, this.container.clientHeight)

      const loader = new GLTFLoader()
      loader.load(
        '/three-assets/adamHead.gltf',
        gltf => {
          gltf.scene.traverse(function (child){
            const checkRightEye = 'node_Eye_R_Blend';
            const checkLeftEye = 'node_Eye_L_Blend';
            if(child.name.includes(checkRightEye)){
                TweenMax.to(child.rotation, 5,  {z: '-=0.9'})
            }

             if(child.name.includes(checkLeftEye)){
                TweenMax.to(child.rotation, 5,  {z: '-=0.9'})
            }
          });
          gltf.scene.rotation.y = 3;
          this.scene.add(gltf.scene)
        },
        undefined,
        undefined
      )

      this.renderer.setAnimationLoop(() => {
        
        this.render()
      })
    },
    render () {
      this.renderer.render(this.scene, this.camera)
      this.stats.update()
    }
  },
  mounted () {
    window.addEventListener('mousemove', function (e) {
      this.xMauseValue = e.x;
      this.yMauseValue = e.y;


      console.log(e.x  +' || ' + e.y);
    });
    this.init()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#scene-container {
  height: 100%;
}
</style>
