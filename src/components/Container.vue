<template>
    <div>
        <div ref="container"></div>
    </div>
</template>

<script>
    import * as THREE from "three";
    // import {GLTFLoader} from "three/addons/loaders/GLTFLoader";
    // import {OrbitControls} from "three/addons/controls/OrbitControls";
    export default {
        name: "ContainerComponent",
        data(){
            return{

            }
        },
        created() {

        },
        mounted() {
            this.scene = null,
            this.camera = null,
            this.scene = null
        },
        // beforeDestroy() {
        //     this.scene = null,
        //     this.camera = null,
        //     this.scene = null
        // },
        methods:{
            init(){
                this.scene = new THREE.Scene()

                this.camera = new THREE.PerspectiveCamera(
                    45, //视角,视角越大，看的东西越多
                    window.innerWidth / window.innerHeight, //宽高比
                    0.1, //近平面，相机最近看到的物体
                    1000 //远平面，相机最远看到的物体
                );


                // 创建渲染器,最终由渲染器渲染到canvas画布上
                const renderer = new THREE.WebGL1Renderer();
                // 设置渲染的尺寸大小
                renderer.setSize(window.innerWidth, window.innerHeight);
                // 设置好大小之后，将画布添加到页面的body上
                document.body.appendChild(renderer.domElement);

                // 创建几何体,下面创建的是正方体
                const geometry = new THREE.BoxGeometry(1, 1, 1);
                // 创建材质,不同材质效果是不同的,下面设置的是基础的黄色的材质
                const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
                // 创建网格,将几何体和材质都传进去（形成物体）
                const cube = new THREE.Mesh(geometry, material);

                // 最后将网格添加到场景当中，我们才能看到它
                this.scene.add(cube);
                // 设置相机位置，表示我们要在哪个位置看这个物体
                this.camera.position.z = 5; //xy轴不设置默认为0
                // 设置相机看向原点
                this.camera.lookAt(0, 0, 0);


            },
            animate(){
                // requestAnimationFrame(animate);

            }
        }
    }
</script>

<style scoped>

</style>
