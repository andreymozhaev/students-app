<template>
  <b-container>
    <b-row>
      <b-col>
        <b-input placeholder="Введите имя и фамилию" v-model="student"></b-input>
      </b-col>
      <b-col>
        <b-form-file @change="previewImage" accept="image/*"></b-form-file>
      </b-col>
      <b-col>
        <b-button variant="danger" @click="onUpload" v-if="!progress">Зарегистрироваться</b-button>
        <b-spinner variant="primary" v-if="progress"></b-spinner>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getFirestore, addDoc, collection } from "firebase/firestore";
import { getDownloadURL, getStorage, ref, uploadBytes } from "firebase/storage";

export default {
  name: "Add",
  data() {
    return {
      uploadValue: 0,
      picture: null,
      imageData: null,
      student: "",
      progress: false
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
      //const q = query(collection(db, "students"));
      const storage = getStorage();
      let timestamp = new Date().getTime();
      const storageRef = ref(storage, timestamp + `_${this.imageData.name}`);

      /*uploadBytes(storageRef, this.imageData).then((snapshot) => {
      });
      
      getDownloadURL(storageRef).then((url)=>{
        console.log(url);
        db.collection('students').addDoc({name: 'newStudent', image: url});
        });*/

      let snapshot = await uploadBytes(storageRef, this.imageData);
      let url = await getDownloadURL(storageRef);
      let docRef = await addDoc(collection(db, "students"), {
        name: this.student,
        image: url,
      });
      //console.log(docRef);
      this.progress = false;
      this.$router.push({name: "home"});
    },
  },
  created() {},
};
</script>

<style>
</style>