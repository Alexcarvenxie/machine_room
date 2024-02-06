<template>
<!--    <div>-->
<!--        <img src="static/svg1.svg">-->
<!--    </div>-->
    <div id="app">
        <form @submit.prevent="submitForm">
            <label for="machineRoomLength">机房长度:</label>
            <input type="text" id="machineRoomLength" v-model="machineRoomLength" required>

            <label for="machineRoomWidth">机房宽度:</label>
            <input type="text" id="machineRoomWidth" v-model="machineRoomWidth" required>

            <label for="machineRoomWidth">机柜数量:</label>
            <input type="text" id="machineRoomWidth" v-model="cabinetSum" required>

            <button type="submit">Submit</button>
        </form>
    </div>
    <div id="canvas-container">
        <canvas id="canvas" ref="canvas"></canvas>
    </div>
</template>

<script>
    import wallData from '../../public/static/GeoJson/Wall.json'
    import * as THREE from 'three';
    import { DragControls } from 'three/examples/jsm/controls/DragControls.js';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

    export default {
        data(){
            return{
                wallData:wallData,
                machineRoomLength: '',
                machineRoomWidth: '',
                cabinetSum:'',
                formSubmitted: false,

                options: {
                    selectObj: '',
                },
                gui: null,
            }
        },
        mounted() {
            // this.initThree();
        },
        methods: {
            submitForm() {
                // 在这里可以执行表单提交的逻辑
                // 这里简单地将表单数据标记为已提交
                this.formSubmitted = true;
                this.initThree();
            },
            initThree() {
                const length = this.machineRoomLength / 2 * 2
                const width = this.machineRoomWidth / 2 * 2
                // const length = 50
                // const width = 50
                // const height = this.machineRoomLength >= 200 ? 10 : 5
                const height = 3
                const geoJsonData = {
                    "type": "FeatureCollection",
                    "features": [
                        {
                            "type": "Feature",
                            "geometry": {
                                "type": "Polygon",
                                "coordinates": [
                                    [
                                        [-length - 0.5, -width - 0.5],
                                        [-length - 0.5, width + 0.5],
                                        [length + 0.5, width + 0.5],
                                        [length + 0.5, -width - 0.5],
                                        [-length - 0.5, -width - 0.5],
                                    ],
                                    [-1],
                                    [{ "color": "0x808080" }]
                                ]
                            }
                        },
                        {
                            "type": "Feature",
                            "geometry": {
                                "type": "Polygon",
                                "coordinates": [
                                    [
                                        [-length, width],
                                        [-length, width + 0.5],
                                        [length, width + 0.5],
                                        [length, width],
                                        [-length, width],
                                    ],
                                    [height],
                                    [{ color: 0x83978c }]
                                ]
                            }
                        },
                        {
                            "type": "Feature",
                            "geometry": {
                                "type": "Polygon",
                                "coordinates": [
                                    [
                                        [-length, -width - 0.5],
                                        [-length, -width],
                                        [length, -width],
                                        [length, -width - 0.5],
                                        [-length, -width - 0.5],
                                    ],
                                    [height],
                                    [{ color: 0x83978c }]
                                ]
                            }
                        },
                        {
                            "type": "Feature",
                            "geometry": {
                                "type": "Polygon",
                                "coordinates": [
                                    [
                                        [-length - 0.5, -width - 0.5],
                                        [-length - 0.5, width + 0.5],
                                        [-length, width + 0.5],
                                        [-length, -width - 0.5],
                                        [-length - 0.5, -width - 0.5],
                                    ],
                                    [height],
                                    [{ color: 0x83978c }]
                                ]
                            }
                        },
                        {
                            "type": "Feature",
                            "geometry": {
                                "type": "Polygon",
                                "coordinates": [
                                    [
                                        [length, -width - 0.5],
                                        [length, width + 0.5],
                                        [length + 0.5, width + 0.5],
                                        [length + 0.5, -width - 0.5],
                                        [length, -width - 0.5],
                                    ],
                                    [height],
                                    [{ color: 0x83978c }]
                                ]
                            }
                        }
                    ]
                };

                // 获取canvas元素
                const canvas = this.$refs.canvas;

                // 初始化场景
                const scene = new THREE.Scene();
                // scene.background = new THREE.Color(0xffffff);

                // 初始化相机
                const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 10000);
                const x = this.machineRoomLength >= 200 ? 10 : 100
                const y = this.machineRoomLength >= 200 ? 320 : 200
                const z = this.machineRoomLength >= 200 ? 250 : 170
                camera.position.set(x,y,z)

                // 初始化渲染器
                const renderer = new THREE.WebGLRenderer({ canvas, antialias: true });
                renderer.setSize(window.innerWidth, window.innerHeight);

                // 初始化OrbitControls
                const controls = new OrbitControls(camera, renderer.domElement);
                controls.enableDamping = true;
                controls.dampingFactor = 0.25;
                controls.screenSpacePanning = false;
                controls.maxPolarAngle = Math.PI / 2;

                // 创建XYZ坐标轴.
                const axesHelper = new THREE.AxesHelper(1000); // 参数表示坐标轴的长度
                scene.add(axesHelper);

                // 创建网格
                const gridHelper = new THREE.GridHelper(
                    300,
                    100,
                    "rgb(248, 248, 248)",
                    "rgb(248, 248, 248)",
                );
                scene.add(gridHelper)


                // 遍历GeoJSON中的每个feature
                for (const feature of geoJsonData.features) {
                    // 从GeoJSON中提取多边形坐标
                    const coordinates = feature.geometry.coordinates[0];

                    // 创建THREE.Shape
                    const shape = new THREE.Shape(coordinates.map(point => new THREE.Vector2(point[0], point[1])));

                    // 使用THREE.ExtrudeGeometry将形状拉伸
                    const extrudeSettings = {
                        depth: feature.geometry.coordinates[1],
                    };

                    const geometry = new THREE.ExtrudeGeometry(shape, extrudeSettings);

                    // 创建网格
                    // const material = new THREE.MeshBasicMaterial(feature.geometry.coordinates[2][0].color);
                    const material = new THREE.MeshBasicMaterial({ color: parseInt(feature.geometry.coordinates[2][0].color) });
                    const mesh = new THREE.Mesh(geometry, material);
                    const axis = new THREE.Vector3(1, 0, 0);//向量axis
                    mesh.rotateOnAxis(axis,Math.PI/(-2));//绕axis轴旋转π/8
                    scene.add(mesh);
                }

                // 存储立方体的数组
                const cubes = [];

                this.$nextTick(() => {
                    // 生成10个立方体
                    for (let i = 0; i < this.cabinetSum; i++) {
                        const cubeGeometry = new THREE.BoxGeometry(3, 10, 6);
                        const cubeMaterial = new THREE.MeshBasicMaterial({ color: 0xFFA500 });
                        const cubeMesh = new THREE.Mesh(cubeGeometry, cubeMaterial);

                        // 设置每个立方体的初始位置
                        cubeMesh.position.set(i * 10, 5, 0);

                        // 将立方体添加到场景和数组中
                        scene.add(cubeMesh);
                        cubes.push(cubeMesh);
                    }

                    // 添加DragControls，传入所有立方体
                    const dragControls = new DragControls(cubes, camera, renderer.domElement);

                    // 监听拖拽开始事件
                    dragControls.addEventListener('dragstart', (event) => {
                        // 标记所有立方体为未拖拽状态
                        cubes.forEach(cubeMesh => {
                            cubeMesh.userData.isDragged = false;
                        });

                        // 获取当前拖拽的立方体
                        const draggedCube = event.object;

                        // 标记当前拖拽的立方体
                        if (draggedCube) {
                            draggedCube.userData.isDragged = true;
                        }

                        controls.enabled = false;
                    });

                    // 监听拖拽结束事件
                    dragControls.addEventListener('dragend', (event) => {
                        // 获取当前拖拽的立方体
                        const draggedCube = event.object;

                        // 调整拖拽的立方体的位置
                        if (draggedCube) {
                            draggedCube.userData.isDragged = false;

                            const posX = draggedCube.position.x;
                            const posZ = draggedCube.position.z;
                            const gridSize = 10;
                            const alignedX = Math.round(posX / gridSize) * gridSize;
                            const alignedZ = Math.round(posZ / gridSize) * gridSize;

                            // 检查是否与其他立方体重叠，如果是，则返回拖拽前的位置
                            const overlapping = cubes.some(cube => cube !== draggedCube && cube.position.x === alignedX && cube.position.z === alignedZ);

                            if (!overlapping) {
                                draggedCube.position.set(alignedX, 5, alignedZ);
                            } else {
                                // 返回拖拽前的位置
                                draggedCube.position.set(posX, 5, posZ);
                            }
                        }

                        controls.enabled = true;
                    });

                    animate();
                });


                function animate() {
                    requestAnimationFrame(animate);

                    // 循环更新每个立方体的位置
                    cubes.forEach(cubeMesh => {
                        if (cubeMesh.userData.isDragged) {
                            const position = new THREE.Vector3();
                            cubeMesh.getWorldPosition(position);
                            cubeMesh.position.set(position.x, position.y, position.z);
                        }
                    });

                    // 渲染场景
                    renderer.render(scene, camera);
                }

                // 启动渲染循环
                animate();

                // 添加环境光
                const ambientLight = new THREE.AmbientLight(0xffffff); // 白色光
                scene.add(ambientLight);


                // 处理窗口大小变化
                window.addEventListener('resize', () => {
                    camera.aspect = window.innerWidth / window.innerHeight;
                    camera.updateProjectionMatrix();
                    renderer.setSize(window.innerWidth, window.innerHeight);
                });

            },

        }
    };
</script>

<style scoped>
    /* Add any necessary styles for your component */
    #app {
        max-width: 400px;
        margin: auto;
        padding: 10px;
    }

    form {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    label {
        margin-bottom: 8px;
    }

    input {
        width: 100%;
        padding: 8px;
        margin-bottom: 16px;
        box-sizing: border-box;
    }

    button {
        padding: 10px;
        background-color: #4caf50;
        color: white;
        border: none;
        cursor: pointer;
    }

    button:hover {
        background-color: #45a049;
    }

    .submitted-message {
        margin-top: 16px;
        text-align: center;
    }
</style>

