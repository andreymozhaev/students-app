<template>
  <b-container>
    <b-row>
      <b-col md="4" offset-md="4">
        <b-input
          placeholder="Введите имя и фамилию"
          v-model="student"
        ></b-input>
      </b-col>
      <b-col v-if="false">
        <b-form-file @change="previewImage" accept="image/*"></b-form-file>
      </b-col>
      <b-col v-if="false">
        <b-button variant="danger" @click="onUpload" v-if="!progress"
          >Зарегистрироваться</b-button
        >
        <b-spinner variant="primary" v-if="progress"></b-spinner>
      </b-col>
    </b-row>
    <b-row v-if = "!student.trim() == ''">
      <b-col class="text-center" md="4" offset-md="4">
        <b-button variant="primary" class="m-5" @click="toggleCamera">
          <span v-if="!isCameraOpen">Открыть камеру</span>
          <span v-else>Закрыть камеру</span>
        </b-button>
      </b-col>
    </b-row>
    <b-row v-if = "!student.trim() == ''">
      <b-col class="text-center" md="4" offset-md="4">
        <b-spinner
          variant="primary"
          v-show="isCameraOpen && isLoading"
        ></b-spinner>
      </b-col>
    </b-row>

    <b-row
      v-if="isCameraOpen && !student.trim() == ''"
      v-show="!isLoading"
      class="camera-box"
      :class="{ flash: isShotPhoto }"
    >
      <b-col md="4" offset-md="4">
        <div class="camera-shutter" :class="{ flash: isShotPhoto }"></div>

        <video
          id="camera"
          v-show="!isPhotoTaken"
          ref="camera"
          width="100%"
          autoplay
        ></video>

        <canvas
          v-show="isPhotoTaken"
          id="photoTaken"
          ref="canvas"
        ></canvas>
      </b-col>
    </b-row>

    <b-row v-if = "!student.trim() == ''">
      <b-col></b-col>
      <b-col class="text-center">
        <b-button
          type="button"
          variant="success"
          @click="takePhoto"
          v-if="isCameraOpen && !isLoading"
          class="m-5"
        >
          <b-icon icon="camera" font-scale="2"></b-icon>
        </b-button>
      </b-col>
      <b-col></b-col>
    </b-row>

    <b-row v-if="isPhotoTaken && isCameraOpen && !student.trim() == ''" class="camera-download">
      <b-col></b-col>
      <b-col class="text-center">
        <b-button variant="success" id="downloadPhoto" @click="downloadImage" v-if="!progress">
          <b-icon icon="download" font-scale="2" ></b-icon>
          Сохранить
        </b-button>
        <b-spinner variant="primary" v-if="progress"></b-spinner>
      </b-col>
      <b-col></b-col>
    </b-row>
  </b-container>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getFirestore, addDoc, collection } from "firebase/firestore";
import {
  getDownloadURL,
  getStorage,
  ref,
  uploadBytes,
  uploadString,
} from "firebase/storage";
import canvasToBlob from "async-canvas-to-blob";

export default {
  name: "Add",
  data() {
    return {
      uploadValue: 0,
      picture: null,
      imageData: null,
      student: "",
      progress: false,
      isCameraOpen: false,
      isPhotoTaken: false,
      isShotPhoto: false,
      isLoading: false,
      link: "#"
    };
  },
  methods: {
    previewImage(event, img) {
      this.uploadValue = 0;
      this.picture = null;
      this.imageData = event.target.files[0];
    },
    async onUpload() {
      this.progress = true;
      const firebaseConfig = {
        apiKey: "AIzaSyACaPJkNGz7wViEK1yXRDMV5IImf9P_CD0",
        authDomain: "students-app-9354e.firebaseapp.com",
        projectId: "students-app-9354e",
        storageBucket: "students-app-9354e.appspot.com",
        messagingSenderId: "51399176797",
        appId: "1:51399176797:web:761c67edf08c67d26a53b4",
        measurementId: "G-5X9E365468",
      };

      const app = initializeApp(firebaseConfig);
      const db = getFirestore();
      const storage = getStorage();
      let timestamp = new Date().getTime();
      const storageRef = ref(storage, timestamp + `_${this.imageData.name}`);

      let snapshot = await uploadBytes(storageRef, this.imageData);
      let url = await getDownloadURL(storageRef);
      let docRef = await addDoc(collection(db, "students"), {
        name: this.student,
        image: url,
      });
      this.progress = false;
      this.$router.push({ name: "home" });
    },
    /*Camera*/
    toggleCamera() {
      if (this.isCameraOpen) {
        this.isCameraOpen = false;
        this.isPhotoTaken = false;
        this.isShotPhoto = false;
        this.stopCameraStream();
      } else {
        this.isCameraOpen = true;
        this.createCameraElement();
      }
    },

    createCameraElement() {
      this.isLoading = true;

      const constraints = (window.constraints = {
        audio: false,
        video: true,
      });

      navigator.mediaDevices
        .getUserMedia(constraints)
        .then((stream) => {
          this.isLoading = false;
          this.$refs.camera.srcObject = stream;
        })
        .catch((error) => {
          this.isLoading = false;
          alert("May the browser didn't support or there is some errors.");
        });
    },

    stopCameraStream() {
      let tracks = this.$refs.camera.srcObject.getTracks();

      tracks.forEach((track) => {
        track.stop();
      });
    },

    takePhoto() {
      const h = this.$refs.camera.videoHeight;
      const w = this.$refs.camera.videoWidth;
      const ratio = h / w;
      if (!this.isPhotoTaken) {
        this.isShotPhoto = true;

        const FLASH_TIMEOUT = 50;

        setTimeout(() => {
          this.isShotPhoto = false;
        }, FLASH_TIMEOUT);
      }

      this.isPhotoTaken = !this.isPhotoTaken;

      const context = this.$refs.canvas.getContext("2d");
      this.$refs.canvas.height = this.$refs.canvas.width * ratio;
      context.drawImage(this.$refs.camera, 0, 0, this.$refs.canvas.width, this.$refs.canvas.height);
    },

    async downloadImage() {
      this.progress = true;
      const firebaseConfig = {
        apiKey: "AIzaSyACaPJkNGz7wViEK1yXRDMV5IImf9P_CD0",
        authDomain: "students-app-9354e.firebaseapp.com",
        projectId: "students-app-9354e",
        storageBucket: "students-app-9354e.appspot.com",
        messagingSenderId: "51399176797",
        appId: "1:51399176797:web:761c67edf08c67d26a53b4",
        measurementId: "G-5X9E365468",
      };

      const app = initializeApp(firebaseConfig);
      const db = getFirestore();
      const storage = getStorage();
      let timestamp = new Date().getTime();
      const storageRef = ref(storage, timestamp + `_${this.student}`);

      const download = document.getElementById("downloadPhoto");
      const canvas = document.getElementById("photoTaken");
      const blob = await canvasToBlob(canvas);
      let snapshot = await uploadBytes(storageRef, blob);
      let url = await getDownloadURL(storageRef);
      let docRef = await addDoc(collection(db, "students"), {
        name: this.student,
        image: url,
      });
      this.progress = false;
      this.$router.push({ name: "home" });
    },
  },
  created() {
  },
};
</script>

<style>
#photoTaken{
  width: 100%;
}
</style>