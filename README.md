# FaceTracking

## AIM:
To create an image-based augmented reality system using MindAR and Three.js that displays and animates a 3D model with background music when a target image is detected.

## ALGORITHM:
step 1: Initialize MindAR with the image target file.

step 2: Create scene, camera, and renderer.

step 3: Add hemisphere light to the scene.

step 4: Load 3D GLTF model and set scale and position.

step 5: Create anchor and attach the model to it.

step 6: Load audio, attach listener to camera, and add sound to the anchor.

step 7: Play audio when target is found and pause when target is lost.

step 8: Create animation mixer and start model animation.

step 9: Start MindAR, update rotation and animation, and render scene continuously.

## PROGRAM:
Index.html
```
<html>
    <head>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="./libs/mindar/mindar-image-three.prod.js"></script>
        <script src="./main.js" type="module"></script>
        <style>
            html,body {position: relative; margin: 0; width: 100%; height: 100%; overflow: hidden;}
        </style>
<link rel = "icon" href="data:,">
    </head>
    <body>
    </body>
</html>
```
main.js
```
import {loadGLTF, loadAudio} from "./libs/loader.js";

const THREE = window.MINDAR.IMAGE.THREE;

document.addEventListener('DOMContentLoaded', () => {
  const start = async () => {
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
## OUTPUT:
![alt text](<Screenshot 2025-10-18 094101.png>)

## RESULT: 
An image-based augmented reality system using MindAR and Three.js that displays and animates a 3D model with background music when a target image is detected is created successfully.