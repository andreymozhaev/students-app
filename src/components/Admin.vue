<template>
  <b-container fluid>
    <b-row>
      <b-col v-for="student in students" :key="student.docId" lg="2" md="3" sm="4" xs="12">
        <b-card
          :title="student.name"
          :img-src="student.image"
          img-alt="Image"
          img-top
          tag="article"
          class="m-2"
        >
          <b-button variant="danger" @click="deleteStudent(student.docId)"
            ><b-icon icon="trash"></b-icon></b-button
          >
          <b-button variant="warning" v-if="!progress" @click="updateRating(student, 4)">4</b-button>
          <b-button variant="success" v-if="!progress" @click="updateRating(student, 5)">5</b-button>
        </b-card>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import { initializeApp } from "firebase/app";
import {
  getFirestore,
  collection,
  query,
  where,
  onSnapshot,
  doc,
  deleteDoc,
  updateDoc
} from "firebase/firestore";

export default {
  name: "Admin",
  data() {
    return {
      students: [],
      studentAvatar: require("@/assets/student.png"),
      db: {},
      progress: false
    };
  },
  methods: {
    getImgUrl(img) {
      return img ? require("" + img) : "";
    },
    async deleteStudent(docId) {
      let value = await this.$bvModal.msgBoxConfirm("Вы уверены?");
      if (value) await deleteDoc(doc(this.db, "students", docId));
    },
    async updateRating(student, value) {
      this.progress = true;
      const ref = doc(this.db, "students", student.docId);
      const rating = student.rating += value;
      await updateDoc(ref, {rating: rating});
      this.progress = false;
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
    this.db = getFirestore();
    const q = query(collection(this.db, "students"));
    const unsubscribe = onSnapshot(q, (querySnapshot) => {
      this.students = [];
      querySnapshot.forEach((doc) => {
        let student = {};
        student.name = doc.data().name;
        student.image = doc.data().image;
        student.rating = doc.data().rating;
        student.docId = doc.id;
        this.students.push(student);
      });
    });
  },
};
</script>

<style>

</style>
