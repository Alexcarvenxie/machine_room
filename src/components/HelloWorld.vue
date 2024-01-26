<template>
<!--    <div>-->
<!--        <img src="static/svg1.svg">-->
<!--    </div>-->
    <div>
        <canvas ref="canvas"></canvas>
    </div>
</template>

<script>
    import * as THREE from 'three';
    // import { SVGLoader } from 'three/addons/loaders/SVGLoader.js';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

    export default {
        mounted() {
            this.initThree();
        },
        methods: {
            initThree() {
                // 获取canvas元素
                const canvas = this.$refs.canvas;

                // 初始化场景
                const scene = new THREE.Scene();
                // scene.background = new THREE.Color(0xffffff);

                // 初始化相机
                const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 10000);
                camera.position.set(100,200,150)

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
                // const gridHelper = new THREE.GridHelper(
                //     1000,
                //     100,
                //     "rgb(248, 248, 248)",
                //     "rgb(248, 248, 248)",
                // );
                // scene.add(gridHelper)


                const geoJsonData = {
                    "type": "FeatureCollection",
                    "features": [
                        {
                            "type": "Feature",
                            "geometry": {
                                "type": "Polygon",
                                "coordinates": [
                                    [
                                        [-50, -50],
                                        [-50, 50],
                                        [50, 50],
                                        [50, -50],
                                        [-50, -50]
                                    ],
                                    [1],
                                    [{ "color": "0xffffff" }]
                                ]
                            }
                        },
                        {
                            "type": "Feature",
                            "geometry": {
                                "type": "Polygon",
                                "coordinates": [
                                    [
                                        [-60, 50],
                                        [-60, 60],
                                        [60, 60],
                                        [60, 50],
                                        [-60, 50],
                                    ],
                                    [5],
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
                                        [-60, -50],
                                        [-60, -60],
                                        [60, -60],
                                        [60, -50],
                                        [-60, -50],
                                    ],
                                    [5],
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
                                        [-50, 50],
                                        [-60, 50],
                                        [-60, -50],
                                        [-50, -50],
                                        [-50, 50]
                                    ],
                                    [5],
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
                                        [50, 50],
                                        [60, 50],
                                        [60, -50],
                                        [50, -50],
                                        [50, 50],
                                    ],
                                    [5],
                                    [{ color: 0x83978c }]
                                ]
                            }
                        }
                    ]
                };


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
                    const material = new THREE.MeshBasicMaterial(feature.geometry.coordinates[2][0]);
                    const mesh = new THREE.Mesh(geometry, material);
                    const axis = new THREE.Vector3(1, 0, 0);//向量axis
                    mesh.rotateOnAxis(axis,Math.PI/(-2));//绕axis轴旋转π/8
                    scene.add(mesh);
                }

                // 渲染循环
                function animate() {
                    requestAnimationFrame(animate);

                    // 旋转网格
                    // mesh.rotation.x += 0.01;
                    // mesh.rotation.y += 0.01;

                    // 渲染场景
                    renderer.render(scene, camera);
                }

                // 启动渲染循环
                animate();

                // 添加光源到场景
                // const light = new THREE.AmbientLight(0xffffff);
                // scene.add(light);
                // 添加环境光
                const ambientLight = new THREE.AmbientLight(0xffffff); // 白色光
                scene.add(ambientLight);


                // 处理窗口大小变化
                window.addEventListener('resize', () => {
                    camera.aspect = window.innerWidth / window.innerHeight;
                    camera.updateProjectionMatrix();
                    renderer.setSize(window.innerWidth, window.innerHeight);
                });

            }
        }
    };
</script>

<style scoped>
    /* Add any necessary styles for your component */
</style>

<!--<template>-->
<!--    <div>-->
<!--        <canvas ref="container" id="container"></canvas>-->
<!--    </div>-->
<!--</template>-->

<!--<script>-->
<!--    import * as THREE from 'three';-->

<!--    export default {-->
<!--        mounted() {-->
<!--            this.$nextTick(() => {-->
<!--                this.initThree();-->
<!--            });-->
<!--        },-->
<!--        methods: {-->
<!--            initThree() {-->
<!--                // 获取HTML中的容器元素-->
<!--                const container = this.$refs.container;-->

<!--                const scene = new THREE.Scene();-->

<!--                const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);-->
<!--                camera.position.z = 5;-->

<!--                const renderer = new THREE.WebGLRenderer();-->
<!--                renderer.setSize(window.innerWidth, window.innerHeight);-->
<!--                container.appendChild(renderer.domElement);-->

<!--                // 添加立方体到场景-->
<!--                const geometry1 = new THREE.BoxGeometry();-->
<!--                const material1 = new THREE.MeshBasicMaterial({ color: 0x00ff00 });-->
<!--                const cube = new THREE.Mesh(geometry1, material1);-->
<!--                scene.add(cube);-->

<!--                // Shape表示一个平面多边形轮廓-->
<!--                const shape = new THREE.Shape([-->
<!--                    new THREE.Vector2(-50, -50),-->
<!--                    new THREE.Vector2(-50, 50),-->
<!--                    new THREE.Vector2(50, 50),-->
<!--                    new THREE.Vector2(50, -50),-->
<!--                ]);-->

<!--                const geometry = new THREE.ExtrudeGeometry(-->
<!--                    shape,-->
<!--                    {-->
<!--                        depth: 20,-->
<!--                    }-->
<!--                );-->

<!--                const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });-->
<!--                const mesh = new THREE.Mesh(geometry, material);-->
<!--                scene.add(mesh);-->

<!--                function animate() {-->
<!--                    requestAnimationFrame(animate);-->

<!--                    // 旋转网格-->
<!--                    mesh.rotation.x += 0.01;-->
<!--                    mesh.rotation.y += 0.01;-->

<!--                    // 渲染场景-->
<!--                    renderer.render(scene, camera);-->
<!--                }-->

<!--                animate();-->
<!--            }-->
<!--        }-->
<!--    };-->
<!--</script>-->


<!--<style scoped>-->
<!--    /* Add any necessary styles for your component */-->
<!--</style>-->



