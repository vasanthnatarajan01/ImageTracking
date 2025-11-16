# Marker-based WebAR — Drummer Raccoon

## AIM:
To create a **marker-based WebAR experience** using **MindAR** and **Three.js**, which detects an image marker and overlays an animated **drummer raccoon** model that plays background music when the marker is visible.

---

## ALGORITHM:

1. **Initialize AR Environment**
   - Use the `MindARThree` framework to create an AR-enabled scene using Three.js.
   - Set up camera, renderer, and container for AR display.

2. **Load the Marker**
   - Import the `.mind` file (`musicband.mind`) that defines the image marker for AR detection.

3. **Set Up Lighting**
   - Add a `HemisphereLight` to illuminate the 3D model realistically.

4. **Load and Configure the Model**
   - Load the 3D drummer raccoon model (`scene.gltf`).
   - Adjust its scale and position for proper visualization on the detected marker.

5. **Attach to Anchor**
   - Create an anchor linked to the first target in the `.mind` file and attach the raccoon model.

6. **Add Audio**
   - Load the background drumbeat audio (`musicband-background.mp3`).
   - Use `THREE.AudioListener` and `THREE.PositionalAudio` to play and spatialize sound relative to the model.

7. **Marker Event Handling**
   - On `markerFound` → Start playing music.
   - On `markerLost` → Pause music.

8. **Animate the Model**
   - Create an `AnimationMixer` for the GLTF model.
   - Continuously update and rotate the model with each render frame.

9. **Render the Scene**
   - Start MindAR and use `renderer.setAnimationLoop()` to render each frame, update animations, and synchronize movements.

---
## PROGRAM:

### index.html
```
<html>
    <head>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="./libs/mindar/mindar-image-three.prod.js"></script>
        <script src="./main3.js" type="module"></script>
        <style>
            html, body {position: relative; margin: 0; width: 100%; height: 100%; overflow: hidden}
        </style>
        <link rel="icon" href="data:, ">
    </head>
    <body>
    </body>
</html>
```
# main3.js
```
import {loadGLTF, loadAudio} from "./libs/loader.js";

const THREE = window.MINDAR.IMAGE.THREE;

document.addEventListener('DOMContentLoaded', () => {
  const start = async() => {
    const mindarThree = new window.MINDAR.IMAGE.MindARThree({
      container: document.body,
      imageTargetSrc: './asserts/musicband.mind',
    });
    const {renderer, scene, camera} = mindarThree;

    const light = new THREE.HemisphereLight( 0xffffff, 0xbbbbff, 1 );
    scene.add(light);

    const gltf = await loadGLTF('./asserts/models/musicband-raccoon/scene.gltf');
    gltf.scene.scale.set(0.1, 0.1, 0.1);
    gltf.scene.position.set(0, -0.4, 0);

    const anchor = mindarThree.addAnchor(0);
    anchor.group.add(gltf.scene);

const audioClip = await loadAudio("./asserts/sounds/musicband-background.mp3");
const listener = new THREE.AudioListener();
const audio = new THREE.PositionalAudio(listener);

camera.add(listener);
anchor.group.add(audio);

audio.setRefDistance(100);
audio.setBuffer(audioClip);
audio.setLoop(true);
anchor.onTargetFound = () =>
{
audio.play();
}
anchor.onTargetLost = () => {
audio.pause();
}

    const mixer = new THREE.AnimationMixer(gltf.scene);
    const action = mixer.clipAction(gltf.animations[0]);
    action.play();

    const clock = new THREE.Clock();

    await mindarThree.start();
    renderer.setAnimationLoop(() => {
      const delta = clock.getDelta();
      gltf.scene.rotation.set(0, gltf.scene.rotation.y+delta, 0);
      mixer.update(delta);
      renderer.render(scene, camera);
    });
  }
  start();
});
```
### File Structure
```
/marker-drummer-raccoon
├── index.html
├── main3.js
├── libs/
│ └── mindar/
│ └── mindar-image-three.prod.js
│ └── loader.js
├── asserts/
│ ├── models/
│ │ └── musicband-raccoon/scene.gltf
│ ├── sounds/
│ │ └── musicband-background.mp3
│ └── musicband.mind
└── README.md
```

## OUTPUT:


<img width="1918" height="1139" alt="Screenshot 2025-11-16 231524" src="https://github.com/user-attachments/assets/7810ed6e-0104-466d-905c-e33050ba5e2c" />



## RESULT:
The experiment successfully demonstrates marker-based AR using MindAR and Three.js.
The drummer raccoon model was successfully rendered and animated on the detected marker, and synchronized background audio was played, validating effective marker detection and real-time 3D rendering in a web-based AR environment.

