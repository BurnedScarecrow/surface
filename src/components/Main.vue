<script>
import * as THREE from "three-full";

export default {
  name: "Main",
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      controls: null,

      p1x: 0,
      p1y: 0,
      p1z: 0,

      p2x: 0,
      p2y: 2,
      p2z: 0,

      p3x: 2,
      p3y: 2,
      p3z: 0,

      p4x: 2,
      p4y: 0,
      p4z: 0,

      rx: 0,
      ry: 0,
      rz: 0,
    };
  },
  methods: {
    init() {
      let container = document.getElementById("container");

      // Renderer
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      const canvas = this.renderer.domElement;
      container.appendChild(canvas);

      this.camera = new THREE.PerspectiveCamera(
        50,
        container.clientWidth / container.clientHeight,
        0.1,
        50,
      );
      const controls = new THREE.OrbitControls(this.camera, canvas);
      controls.target.set(0, 0, 0);
      controls.update();

      this.scene = new THREE.Scene();

      this.camera.position.x = 5;
      this.camera.position.z = 5;
      this.camera.position.y = 5;
      this.camera.lookAt(new THREE.Vector3(0, 0, 0));
    },
    async animate() {
      while (this.scene.children.length > 0) {
        this.scene.remove(this.scene.children[0]);
      }

      this.drawGrid(10, 5);

      this.p1x = Number(this.p1x);
      this.p1y = Number(this.p1y);
      this.p1z = Number(this.p1z);
      this.p2x = Number(this.p2x);
      this.p2y = Number(this.p2y);
      this.p2z = Number(this.p2z);
      this.p3x = Number(this.p3x);
      this.p3y = Number(this.p3y);
      this.p3z = Number(this.p3z);
      this.p4x = Number(this.p4x);
      this.p4y = Number(this.p4y);
      this.p4z = Number(this.p4z);
      this.rx = Number(this.rx);
      this.ry = Number(this.ry);
      this.rz = Number(this.rz);

      if (
        typeof this.p1x == "number" &&
        typeof this.p1y == "number" &&
        typeof this.p1z == "number" &&
        typeof this.p2x == "number" &&
        typeof this.p2y == "number" &&
        typeof this.p2z == "number" &&
        typeof this.p3x == "number" &&
        typeof this.p3y == "number" &&
        typeof this.p3z == "number" &&
        typeof this.p4x == "number" &&
        typeof this.p4y == "number" &&
        typeof this.p4z == "number" &&
        typeof this.rx == "number" &&
        typeof this.ry == "number" &&
        typeof this.rz == "number"
      ) {
        requestAnimationFrame(this.animate);
        this.addSurf();
        this.renderer.render(this.scene, this.camera);
      }
    },

    async addSurf() {
      const edge1 = new THREE.Line3(
        new THREE.Vector3(this.p1x, this.p1y, this.p1z),
        new THREE.Vector3(this.p2x, this.p2y, this.p2z),
      );

      const edge2 = new THREE.Line3(
        new THREE.Vector3(this.p4x, this.p4y, this.p4z),
        new THREE.Vector3(this.p3x, this.p3y, this.p3z),
      );

      const edge3 = new THREE.Line3(
        new THREE.Vector3(this.p1x, this.p1y, this.p1z),
        new THREE.Vector3(this.p4x, this.p4y, this.p4z),
      );

      const edge4 = new THREE.Line3(
        new THREE.Vector3(this.p2x, this.p2y, this.p2z),
        new THREE.Vector3(this.p3x, this.p3y, this.p3z),
      );

      const lineGeom = new THREE.Geometry();
      const line_material1 = new THREE.LineBasicMaterial({ color: 0xcccccc });

      for (let i = 0; i <= 1; i += 0.0249) {
        const start = new THREE.Vector3();
        const end = new THREE.Vector3();
        edge3.at(i, start);
        edge4.at(i, end);
        lineGeom.vertices.push(start);
        lineGeom.vertices.push(end);

        const start_perp = new THREE.Vector3();
        const end_perp = new THREE.Vector3();
        edge2.at(i, start_perp);
        edge1.at(i, end_perp);
        lineGeom.vertices.push(start_perp);
        lineGeom.vertices.push(end_perp);
      }

      lineGeom.rotateX((Math.PI / 180) * this.rx);
      lineGeom.rotateY((Math.PI / 180) * this.ry);
      lineGeom.rotateZ((Math.PI / 180) * this.rz);

      const line = new THREE.LineSegments(lineGeom, line_material1);
      this.scene.add(line);
    },
    drawGrid(size, step) {
      var lineMaterial = new THREE.LineBasicMaterial({
        color: 0x333333,
        linewidth: 1,
      });
      var lineGeom = new THREE.Geometry();
      for (let nn = -size; nn < size; nn += 1 / step) {
        lineGeom.vertices.push(new THREE.Vector3(nn, -0.001, -size));
        lineGeom.vertices.push(new THREE.Vector3(nn, -0.001, size));

        lineGeom.vertices.push(new THREE.Vector3(-size, -0.001, nn));
        lineGeom.vertices.push(new THREE.Vector3(size, -0.001, nn));
      }
      let line = new THREE.LineSegments(lineGeom, lineMaterial);
      this.scene.add(line);

      const orange = new THREE.LineBasicMaterial({
        color: 0xff6600,
        linewidth: 1,
      });

      const blue = new THREE.LineBasicMaterial({
        color: 0x0066ff,
        linewidth: 1,
      });

      const green = new THREE.LineBasicMaterial({
        color: 0x00ff00,
        linewidth: 1,
      });
      let x_line = new THREE.Geometry();
      x_line.vertices.push(new THREE.Vector3(-size, 0.0, 0));
      x_line.vertices.push(new THREE.Vector3(size, 0.0, 0));
      const line1 = new THREE.LineSegments(x_line, orange);
      this.scene.add(line1);

      let y_line = new THREE.Geometry();
      y_line.vertices.push(new THREE.Vector3(0, -size, 0));
      y_line.vertices.push(new THREE.Vector3(0, size, 0));
      const line2 = new THREE.LineSegments(y_line, green);
      this.scene.add(line2);

      let z_line = new THREE.Geometry();
      z_line.vertices.push(new THREE.Vector3(0, 0, -size));
      z_line.vertices.push(new THREE.Vector3(0, 0, size));
      const line3 = new THREE.LineSegments(z_line, blue);
      this.scene.add(line3);

      const arrowHelper1 = new THREE.ArrowHelper(
        new THREE.Vector3(1, 0, 0).normalize(),
        new THREE.Vector3(0, 0, 0),
        2,
        0xff6600,
      );
      const arrowHelper2 = new THREE.ArrowHelper(
        new THREE.Vector3(0, 1, 0).normalize(),
        new THREE.Vector3(0, 0, 0),
        2,
        0x00ff00,
      );
      const arrowHelper3 = new THREE.ArrowHelper(
        new THREE.Vector3(0, 0, 1).normalize(),
        new THREE.Vector3(0, 0, 0),
        2,
        0x0066ff,
      );
      this.scene.add(arrowHelper1);
      this.scene.add(arrowHelper2);
      this.scene.add(arrowHelper3);
    },
  },
  mounted() {
    this.init();
    this.animate();
  },
};
</script>

<template>
  <div class="wrapper">
    <div id="container"></div>
    <div class="controlls">
      <b> Controlls </b>
      <div class="inputlabel">
        1 Point
        <input type="number" v-model="p1x" class="x" />
        <input type="number" v-model="p1y" class="y" />
        <input type="number" v-model="p1z" class="z" />
      </div>
      <div class="inputlabel">
        2 Point
        <input type="number" v-model="p2x" class="x" />
        <input type="number" v-model="p2y" class="y" />
        <input type="number" v-model="p2z" class="z" />
      </div>
      <div class="inputlabel">
        3 Point
        <input type="number" v-model="p3x" class="x" />
        <input type="number" v-model="p3y" class="y" />
        <input type="number" v-model="p3z" class="z" />
      </div>
      <div class="inputlabel">
        4 Point
        <input type="number" v-model="p4x" class="x" />
        <input type="number" v-model="p4y" class="y" />
        <input type="number" v-model="p4z" class="z" />
      </div>
      <div class="inputlabel">
        Rotate
        <input
          type="number"
          v-model="rx"
          @change="animate"
          step="15"
          class="x"
        />

        <input
          type="number"
          v-model="ry"
          @change="animate"
          step="15"
          class="y"
        />

        <input
          type="number"
          v-model="rz"
          @change="animate"
          step="15"
          class="z"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
.wrapper {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #000;
}

#container {
  display: block;
  margin: 0 auto;
  width: 100%;
  height: 100vh;
}

.controlls {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  padding: 5px 5px;
  width: 250px;
  height: fit-content;
  position: absolute;
  top: 20px;
  right: 20px;
  background: rgba(0, 0, 0, 0.7);
  border-radius: 0.5rem;
  box-sizing: border-box;
  border: 0.5px solid whitesmoke;
  color: whitesmoke;
}

.inputlabel {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin: 3px 0;
}
input {
  width: 40px;
  height: 1rem;
  background: transparent;
  border: 0;
  border-bottom: 1px solid whitesmoke;
  box-sizing: border-box;

  text-align: center;
  color: whitesmoke;
}
button {
  cursor: pointer;
  background: transparent;
  border: 0;
  color: whitesmoke;
}
.x {
  color: #ff6600;
}

.y {
  color: #00ff00;
}

.z {
  color: #0066ff;
}
</style>
