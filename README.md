  <!DOCTYPE html>
  <html lang="en">

  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blender-like UI</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three/examples/js/controls/OrbitControls.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three/examples/js/controls/TransformControls.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three/examples/js/loaders/GLTFLoader.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
      * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }
      renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

// Ejemplo: Hacer que el plano reciba sombras
gridHelper.receiveShadow = true;


      body {
        font-family: Arial, sans-serif;
        overflow: hidden;
        background-color: #333;
        height: 100%;
      }

      /* Header */
      #header {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 35px;
        background-color: #2c2c2c;
        color: #fff;
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0 10px;
        z-index: 2;
      }

      #header .menu {
        display: flex;
      }

      #header .menu button {
        background-color: #444;
        border: none;
        color: #fff;
        padding: 4px 8px;
        margin: 0 5px;
        cursor: pointer;
        font-size: 12px;
        display: flex;
        align-items: center;
      }

      #header .menu button i {
        margin-right: 5px;
      }

      #header .menu button:hover {
        background-color: #555;
      }

      /* Sidebar */
      #sidebar {
        position: fixed;
        top: 35px;
        left: 0;
        width: 160px;
        height: calc(100% - 135px);
        background-color: #2c2c2c;
        color: #fff;
        padding: 5px;
        overflow-y: auto;
        z-index: 1;
      }

      #sidebar .section {
        margin-bottom: 10px;
      }

      #sidebar .section h3 {
        font-size: 12px;
        margin-bottom: 8px;
        border-bottom: 1px solid #444;
        padding-bottom: 4px;
      }

      #sidebar .section button {
        width: 100%;
        background-color: #444;
        border: none;
        color: #fff;
        padding: 5px;
        margin-bottom: 4px;
        cursor: pointer;
        font-size: 12px;
        display: flex;
        align-items: center;
      }

      #sidebar .section button i {
        margin-right: 8px;
      }

      #sidebar .section button:hover {
        background-color: #555;
      }

      /* Timeline */
      #timeline {
        position: fixed;
        bottom: 0;
        left: 0;
        width: 100%;
        height: 100px;
        background-color: #2c2c2c;
        color: #fff;
        padding: 10px;
        border-top: 1px solid #444;
        z-index: 1;
      }

      /* Main Viewport */
      #viewport {
        position: absolute;
        top: 0.1px;
        left: 160px;
        bottom: 1000px;
        right: 0;
        background-color: #555;
      }

      #viewport canvas {
        display: block;
        width: 100%;
        height: 100%;
      }

      /* Object list panel */
      #objectPanel {
        position: fixed;
        top: 30px;
        right: 10px;
        width: 200px;
        background-color: rgba(44, 44, 44, 0.7);
        color: #fff;
        padding: 10px;
        z-index: 2;
        max-height: 500px;
        overflow-y: auto;
      }

      #objectPanel h3 {
        font-size: 14px;
        margin-bottom: 10px;
      }

      #objectPanel ul {
        list-style: none;
        padding: 0;
      }

      #objectPanel ul li {
        margin-bottom: 5px;
        cursor: pointer;
      }

      #objectPanel ul li:hover {
        color: #ffcc00;
      }
      #fileOptionsModal button {
  background-color: #444;
  color: #fff;
  border: none;
  padding: 0px 0px;
  margin: 0px 0;
  cursor: pointer;
  border-radius: 0px;
}

#fileOptionsModal button:hover {
  background-color: #555;
}


      /* Modal for object options */
      #objectOptionsModal {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-color: rgba(0, 0, 0, 0.8);
        padding: 20px;
        color: white;
        display: none;
        z-index: 3;
      }

      #objectOptionsModal button {
        background-color: #444;
        color: #fff;
        border: none;
        padding: 8px 12px;
        margin-top: 10px;
        cursor: pointer;
      }

      #objectOptionsModal button:hover {
        background-color: #555;
      }

      #closeModal {
        position: absolute;
        top: 5px;
        right: 5px;
        color: white;
        font-size: 18px;
        cursor: pointer;
      }
    </style>
  </head>

  <body>
    <!-- Header -->
    <div id="header">
      <div class="menu">
        <button><i class="fas fa-file"></i>File</button>
        <!-- Modal for File Options -->
<div id="fileOptionsModal" style="display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background-color: rgba(44, 44, 44, 0.9); padding: 2000px; border-radius: 100px; color: white; z-index: 1000;">
  <h3>File Options</h3>
  <button id="importButton" style="margin-bottom: 5px;">Importar</button><br>
  <button id="exportButton">Exportar</button><br>
  <button id="closeFileModal" style="margin-top: 5px;">Cerrar</button>
</div>

        <button><i class="fas fa-edit"></i>Edit</button>
        <button><i class="fas fa-play"></i>Render</button>
      </div>
      <div class="menu">
        <button><i class="fas fa-question-circle"></i>Help</button>
      </div>
    </div>

    <!-- Sidebar -->
    <div id="sidebar">
      <div class="section">
        <h3>Tools</h3>
        <button id="move"><i class="fas fa-arrows-alt"></i> Move</button>
        <button id="rotate"><i class="fas fa-redo"></i> Rotate</button>
        <button id="scale"><i class="fas fa-expand"></i> Scale</button>
      </div>
      <!-- Add Menu -->
      <div class="section">
        <h3>Add</h3>
        <button onclick="createCube()"><i class="fas fa-cube"></i> Cube</button>
        <button onclick="createSphere()"><i class="fas fa-circle"></i> Sphere</button>
        <button onclick="createCylinder()"><i class="fas fa-caret-up"></i> Cylinder</button>
        <button onclick="createPlane()"><i class="fas fa-th-large"></i> Plane</button>
        <button onclick="createCone()"><i class="fas fa-caret-square-up"></i> Cone</button>
        <button onclick="addSunLight()"><i class="fas fa-lightbulb"></i> Luz</button>

        <button onclick="createMonkey()"><i class="fas fa-user"></i> Monkey</button>
        <div class="section">
  <h3>Materiales</h3>
  <button onclick="applyTexture()"><i class="fas fa-image"></i> Texturizar</button>
  <button id="btnColor"><i class="fas fa-palette"></i> Color</button>

  <button onclick="adjustTransparency()"><i class="fas fa-adjust"></i> Transparencia</button>
  <button onclick="adjustEmission()"><i class="fas fa-sun"></i> Emisión</button>
  <button onclick="showPhysicsMenu()"><i class="fas fa-weight"></i> Físicas</button>
</div>
<div id="menuRender">
  <h3>Render</h3>
  <button onclick="renderImage()">Render Imagen</button>
  <button onclick="renderVideo()">Render Video</button>
</div>


      </div>
    </div>

    <!-- Timeline -->
    <div id="timeline">
      Timeline (Frames will go here)
    </div>

    <!-- Main Viewport -->
    <div id="viewport">
      <canvas id="canvas3D"></canvas>
    </div>

    <!-- Object Panel -->
    <div id="objectPanel">
      <h3>Objects</h3>
      <ul id="objectList"></ul>
    </div>

    <!-- Modal for object options -->
    <div id="objectOptionsModal">
      <span id="closeModal">X</span>
      <p>Options for <span id="selectedObjectName"></span></p>
      <input type="text" id="newName" placeholder="Enter new name" />
      <button id="renameObject">Rename</button>
      <button id="deleteObject">Delete</button>
    </div>

    <script>
      // Configuración inicial de la escena
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0xf0f0f0);

      // Grid Helper
      const gridHelper = new THREE.GridHelper(50, 50);
      scene.add(gridHelper);

      const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      camera.position.set(0, 5, 10);
      camera.lookAt(0, 0, 0);

      const renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('canvas3D') });
      renderer.setSize(window.innerWidth - 16, window.innerHeight - 13);
      document.body.appendChild(renderer.domElement);

      const controls = new THREE.OrbitControls(camera, renderer.domElement);

      // TransformControls
      const transformControls = new THREE.TransformControls(camera, renderer.domElement);
      scene.add(transformControls);

      const objects = [];
      const objectNames = [];

// Botón "File" abre el modal de opciones
document.querySelector('.menu button:first-child').addEventListener('click', () => {
  document.getElementById('fileOptionsModal').style.display = 'block';
});

// Cerrar el modal
document.getElementById('closeFileModal').addEventListener('click', () => {
  document.getElementById('fileOptionsModal').style.display = 'none';
});

// Función para exportar el proyecto
document.getElementById('exportButton').addEventListener('click', () => {
  const exporter = new THREE.GLTFExporter();
  exporter.parse(
    scene,
    (result) => {
      const blob = new Blob([JSON.stringify(result)], { type: 'application/json' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = 'proyecto.gltf';
      link.click();
    },
    { binary: false }
  );
});

// Función para importar proyectos y archivos 3D
document.getElementById('importButton').addEventListener('click', () => {
  const input = document.createElement('input');
  input.type = 'file';
  input.accept = '.gltf,.glb,.fbx';
  input.addEventListener('change', (event) => {
    const file = event.target.files[0];
    const fileType = file.name.split('.').pop().toLowerCase();

    if (fileType === 'gltf' || fileType === 'glb') {
      const reader = new FileReader();
      reader.onload = (e) => {
        loader.parse(e.target.result, '', (gltf) => {
          scene.add(gltf.scene);
          updateObjectList();
        });
      };
      reader.readAsArrayBuffer(file);
    } else if (fileType === 'fbx') {
      const fbxLoader = new THREE.FBXLoader();
      fbxLoader.load(URL.createObjectURL(file), (fbx) => {
        scene.add(fbx);
        updateObjectList();
      });
    } else {
      alert('Formato no soportado. Usa GLTF, GLB o FBX.');
    }
  });
  input.click();
});

      // Función para crear cubo
      function createCube() {
        const geometry = new THREE.BoxGeometry(1, 1, 1);
        const material = new THREE.MeshBasicMaterial({ color: 0xb8b8b8 });
        const cube = new THREE.Mesh(geometry, material);
        scene.add(cube);
        objects.push(cube);
        objectNames.push('Cube ' + objects.length);
        addObjectToPanel('Cube ' + objects.length, cube);
      }
      function addSunLight() {
    // Crear la luz
    const sunLight = new THREE.DirectionalLight(0xffffff, 1);
    sunLight.position.set(10, 10, 10);
    sunLight.castShadow = true;
    sunLight.shadow.radius = 5; // Difuminado de sombras
    scene.add(sunLight);

    // Mostrar panel de configuración
    showLightOptions(sunLight);
}

function showLightOptions(light) {
    // Crear el contenedor para las opciones
    const optionsDiv = document.createElement('div');
    optionsDiv.id = 'lightOptions';
    optionsDiv.style.position = 'fixed';
    optionsDiv.style.bottom = '110px';
    optionsDiv.style.left = '10px';
    optionsDiv.style.backgroundColor = '#222';
    optionsDiv.style.padding = '10px';
    optionsDiv.style.borderRadius = '5px';
    optionsDiv.style.color = '#fff';
    optionsDiv.style.zIndex = '2';

    // Agregar controles
    optionsDiv.innerHTML = `
        <label>Intensidad: <input type="range" min="0" max="2" step="0.1" value="${light.intensity}" id="intensityControl"></label><br>
        <label>Color: <input type="color" value="#ffffff" id="colorControl"></label><br>
        <label>Sombra Difusa: <input type="range" min="0" max="10" step="1" value="${light.shadow.radius}" id="shadowControl"></label><br>
        <button id="closeLightOptions">OK</button>
    `;

    document.body.appendChild(optionsDiv);

    // Eventos para controlar los parámetros
    document.getElementById('intensityControl').addEventListener('input', (e) => {
        light.intensity = e.target.value;
    });
    document.getElementById('colorControl').addEventListener('input', (e) => {
        light.color.set(e.target.value);
    });
    document.getElementById('shadowControl').addEventListener('input', (e) => {
        light.shadow.radius = e.target.value;
    });

    // Botón para cerrar opciones
    document.getElementById('closeLightOptions').addEventListener('click', () => {
        document.body.removeChild(optionsDiv);
    });
}


      // Función para crear esfera
      function createSphere() {
        const geometry = new THREE.SphereGeometry(1, 32, 32);
        const material = new THREE.MeshBasicMaterial({ color: 0xb8b8b8 });
        const sphere = new THREE.Mesh(geometry, material);
        scene.add(sphere);
        objects.push(sphere);
        objectNames.push('Sphere ' + objects.length);
        addObjectToPanel('Sphere ' + objects.length, sphere);
      }

// Función para renderizar y guardar una imagen PNG
function renderImage() {
  renderer.render(scene, camera); // Asegúrate de que 'renderer', 'scene' y 'camera' estén configurados.
  const imgData = renderer.domElement.toDataURL("image/png");

  const link = document.createElement("a");
  link.download = "render.png";
  link.href = imgData;
  link.click();
}

// Función para Render Video (placeholder)
function renderVideo() {
  alert("La funcionalidad de Render Video aún no está disponible.");
}

      // Función para crear cilindro
      function createCylinder() {
        const geometry = new THREE.CylinderGeometry(1, 1, 2, 32);
        const material = new THREE.MeshBasicMaterial({ color: 0xb8b8b8 });
        const cylinder = new THREE.Mesh(geometry, material);
        scene.add(cylinder);
        objects.push(cylinder);
        objectNames.push('Cylinder ' + objects.length);
        addObjectToPanel('Cylinder ' + objects.length, cylinder);
      }

      // Función para crear plano
      function createPlane() {
        const geometry = new THREE.PlaneGeometry(5, 5);
        const material = new THREE.MeshBasicMaterial({ color: 0xb8b8b8, side: THREE.DoubleSide });
        const plane = new THREE.Mesh(geometry, material);
        scene.add(plane);
        objects.push(plane);
        objectNames.push('Plane ' + objects.length);
        addObjectToPanel('Plane ' + objects.length, plane);
      }
      // Cambiar el color del objeto seleccionado
document.getElementById('btnColor').addEventListener('click', () => {
  if (!transformControls.object) {
    alert("Selecciona un objeto primero.");
    return;
  }

  // Crear un selector de color dinámico
  const colorPicker = document.createElement('input');
  colorPicker.type = 'color';
  colorPicker.style.position = 'absolute';
  colorPicker.style.top = '50%';
  colorPicker.style.left = '50%';
  colorPicker.style.transform = 'translate(-50%, -50%)';
  colorPicker.style.zIndex = '1000';
  document.body.appendChild(colorPicker);

  // Detectar el cambio de color
  colorPicker.addEventListener('input', (event) => {
    const color = event.target.value;
    transformControls.object.material.color = new THREE.Color(color);
    transformControls.object.material.needsUpdate = true;
  });

  // Eliminar el selector de color al perder foco
  colorPicker.addEventListener('blur', () => {
    document.body.removeChild(colorPicker);
  });

  // Forzar la apertura del selector de color
  colorPicker.focus();
});


      // Función para crear cono
      function createCone() {
        const geometry = new THREE.ConeGeometry(1, 2, 32);
        const material = new THREE.MeshBasicMaterial({ color: 0xb8b8b8 });
        const cone = new THREE.Mesh(geometry, material);
        scene.add(cone);
        objects.push(cone);
        objectNames.push('Cone ' + objects.length);
        addObjectToPanel('Cone ' + objects.length, cone);
      }

      const loader = new THREE.GLTFLoader();

// Seleccionar una textura desde los archivos del usuario
function applyTexture() {
  const input = document.createElement('input');
  input.type = 'file';
  input.accept = 'image/*';
  input.onchange = (event) => {
    const file = event.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = () => {
        const texture = new THREE.TextureLoader().load(reader.result);
        if (transformControls.object) {
          transformControls.object.material.map = texture;
          transformControls.object.material.needsUpdate = true;
        } else {
          alert("Por favor selecciona un objeto primero.");
        }
      };
      reader.readAsDataURL(file);
    }
  };
  input.click();
}

// Ajustar la transparencia del objeto seleccionado
function adjustTransparency() {
  if (transformControls.object) {
    const object = transformControls.object;
    const newTransparency = prompt("Ingresa un valor de transparencia (0.0 a 1.0):", object.material.opacity || 1);
    if (newTransparency !== null) {
      object.material.transparent = true;
      object.material.opacity = parseFloat(newTransparency);
      object.material.needsUpdate = true;
    }
  } else {
    alert("Por favor selecciona un objeto primero.");
  }
}

// Ajustar la emisión del objeto seleccionado
function adjustEmission() {
  if (transformControls.object) {
    const object = transformControls.object;
    const newEmission = prompt("Ingresa un valor de emisión (0.0 a 1.0):", object.material.emissiveIntensity || 0);
    if (newEmission !== null) {
      object.material.emissive = new THREE.Color(0xffffff);
      object.material.emissiveIntensity = parseFloat(newEmission);
      object.material.needsUpdate = true;
    }
  } else {
    alert("Por favor selecciona un objeto primero.");
  }
}

// Mostrar el menú de físicas
function showPhysicsMenu() {
  if (transformControls.object) {
    const object = transformControls.object;

    const physicsDiv = document.createElement('div');
    physicsDiv.style.position = 'fixed';
    physicsDiv.style.bottom = '150px';
    physicsDiv.style.left = '10px';
    physicsDiv.style.backgroundColor = '#222';
    physicsDiv.style.padding = '10px';
    physicsDiv.style.borderRadius = '5px';
    physicsDiv.style.color = '#fff';
    physicsDiv.style.zIndex = '2';

    physicsDiv.innerHTML = `
      <label>Peso (kg): <input type="number" id="weightControl" value="${object.userData.weight || 1}" step="0.1"></label><br>
      <button id="applyPhysics">Aplicar</button>
      <button id="closePhysicsMenu">Cerrar</button>
    `;

    document.body.appendChild(physicsDiv);

    document.getElementById('applyPhysics').addEventListener('click', () => {
      const weight = parseFloat(document.getElementById('weightControl').value);
      object.userData.weight = weight;
      object.userData.physicsEnabled = true;
      alert(`Físicas aplicadas: Peso = ${weight} kg`);
      document.body.removeChild(physicsDiv);
    });

    document.getElementById('closePhysicsMenu').addEventListener('click', () => {
      document.body.removeChild(physicsDiv);
    });
  } else {
    alert("Por favor selecciona un objeto primero.");
  }
}

      // Función para cargar el modelo "Monkey"
      function createMonkey() {
        const url = 'https://www.mediafire.com/file/08a9k7b200n4sbd/Zussane_modelo.gltf/file';

        loader.load(
          url,
          function(gltf) {
            const model = gltf.scene;
            model.scale.set(1, 1, 1); // Ajusta la escala si es necesario
            model.position.set(0, 0, 0); // Posiciona el modelo en el centro de la escena
            scene.add(model);
            updateObjectList();
          },
          function(xhr) {
            console.log((xhr.loaded / xhr.total * 100) + '% loaded');
          },
          function(error) {
            console.error('An error happened while loading the model:', error);
          }
        );
      }

      // Función para agregar objetos al panel
      function addObjectToPanel(name, object) {
        const listItem = document.createElement('li');
        listItem.textContent = name;
        listItem.addEventListener('click', () => selectObject(object));
        document.getElementById('objectList').appendChild(listItem);
      }

      // Función para seleccionar un objeto y activar la herramienta de mover
      function selectObject(object) {
        transformControls.attach(object);
        transformControls.setMode('translate');
        document.getElementById('selectedObjectName').textContent = objectNames[objects.indexOf(object)];
        document.getElementById('objectOptionsModal').style.display = 'block';
        document.getElementById('closeModal').style.display = 'block';
      }


      // Función para eliminar el objeto
      document.getElementById('deleteObject').addEventListener('click', () => {
        const objectName = document.getElementById('selectedObjectName').textContent;
        const objectIndex = objectNames.indexOf(objectName);
        if (objectIndex !== -1) {
          scene.remove(objects[objectIndex]);
          objects.splice(objectIndex, 1);
          objectNames.splice(objectIndex, 1);
          document.getElementById('objectList').innerHTML = '';
          objects.forEach((obj, index) => addObjectToPanel(objectNames[index], obj));
          document.getElementById('objectOptionsModal').style.display = 'none';
          document.getElementById('closeModal').style.display = 'none';
        }
      });

      // Función para cambiar el nombre
      document.getElementById('renameObject').addEventListener('click', () => {
        const newName = document.getElementById('newName').value;
        if (newName) {
          const objectName = document.getElementById('selectedObjectName').textContent;
          const objectIndex = objectNames.indexOf(objectName);
          if (objectIndex !== -1) {
            objectNames[objectIndex] = newName;
            document.getElementById('selectedObjectName').textContent = newName;
            document.getElementById('newName').value = '';
            document.getElementById('objectList').innerHTML = '';
            objects.forEach((obj, index) => addObjectToPanel(objectNames[index], obj));
          }
        }
      });

      // Cerrar el modal de opciones
      document.getElementById('closeModal').addEventListener('click', () => {
        document.getElementById('objectOptionsModal').style.display = 'none';
        document.getElementById('closeModal').style.display = 'none';
      });

      // Cambiar el modo de TransformControls
      document.getElementById('move').addEventListener('click', () => transformControls.setMode('translate'));
      document.getElementById('rotate').addEventListener('click', () => transformControls.setMode('rotate'));
      document.getElementById('scale').addEventListener('click', () => transformControls.setMode('scale'));

      // Evento para congelar y descongelar la cámara
      transformControls.addEventListener('mouseDown', () => controls.enabled = false);
      transformControls.addEventListener('mouseUp', () => controls.enabled = true);

      // Animación
      function animate() {
        requestAnimationFrame(animate);
        controls.update();
        renderer.render(scene, camera);
      }

      animate();
      
  // Configuración para seleccionar objetos táctilmente
  const raycaster = new THREE.Raycaster();
  const pointer = new THREE.Vector2();

  function onPointerDown(event) {
    // Obtener las coordenadas del toque/clic
    pointer.x = (event.clientX / window.innerWidth) * 2 - 1;
    pointer.y = -(event.clientY / window.innerHeight) * 2 + 1;

    // Lanzar un rayo desde la cámara
    raycaster.setFromCamera(pointer, camera);

    // Detectar intersecciones con los objetos en la escena
    const intersects = raycaster.intersectObjects(objects, true);

    if (intersects.length > 0) {
      const selectedObject = intersects[0].object;

      // Conectar el TransformControls al objeto seleccionado
      transformControls.attach(selectedObject);

      // Mostrar el nombre del objeto en la consola (opcional)
      console.log('Objeto seleccionado:', selectedObject.name || 'Sin nombre');
    }
  }

  // Escuchar eventos táctiles y de mouse
  renderer.domElement.addEventListener('pointerdown', onPointerDown);
</script>

    </script>
  </body>

  </html>