<template>
  <div id="students">
    <div v-for="student in students" :key="student.docId">
      <img v-bind:src="student.image" alt="студент" />
      <h2>{{ student.name }}</h2>
    </div>
  </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import {
  getFirestore,
  collection,
  query,
  where,
  onSnapshot,
} from "firebase/firestore";

export default {
  name: "Home",
  data() {
    return {
      students: [],
      studentAvatar: require("@/assets/student.png"),
    };
  },
  methods: {
    getImgUrl(img) {
      return img ? require("" + img) : "";
    },
  },
  created() {
    // Your web app's Firebase configuration
    // For Firebase JS SDK v7.20.0 and later, measurementId is optional
    const firebaseConfig = {
      apiKey: "AIzaSyACaPJkNGz7wViEK1yXRDMV5IImf9P_CD0",
      authDomain: "students-app-9354e.firebaseapp.com",
      projectId: "students-app-9354e",
      storageBucket: "students-app-9354e.appspot.com",
      messagingSenderId: "51399176797",
      appId: "1:51399176797:web:761c67edf08c67d26a53b4",
      measurementId: "G-5X9E365468",
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);
    const db = getFirestore();
    const q = query(collection(db, "students"));
    const unsubscribe = onSnapshot(q, (querySnapshot) => {
      this.students = [];
      querySnapshot.forEach((doc) => {
        let student = {};
        student.name = doc.data().name;
        student.image = doc.data().image;
        student.docId = doc.id;
        this.students.push(student);
      });
    });
  },
};
</script>

<style>
#students {
  max-height: 72vh;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
  column-gap: 2em;
  row-gap: 2em;
}

#students div {
  margin: auto;
  text-align: center;
}

#students img {
  width: 75%;
}

#students h2 {
  text-align: center;
}
</style>
