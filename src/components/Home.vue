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
        <b-card-text>
          <b-icon icon="star-fill" variant="warning"></b-icon>
          {{ student.rating }}
        </b-card-text>
        </b-card>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import { initializeApp } from 'firebase/app'
import {
  getFirestore,
  collection,
  query,
  onSnapshot
} from 'firebase/firestore'

export default {
  name: 'Home',
  data () {
    return {
      students: [],
      studentAvatar: require('@/assets/student.png')
    }
  },
  methods: {
    getImgUrl (img) {
      return img ? require('' + img) : ''
    }
  },
  created () {
    // Your web app's Firebase configuration
    // For Firebase JS SDK v7.20.0 and later, measurementId is optional
    const firebaseConfig = {
      apiKey: 'AIzaSyACaPJkNGz7wViEK1yXRDMV5IImf9P_CD0',
      authDomain: 'students-app-9354e.firebaseapp.com',
      projectId: 'students-app-9354e',
      storageBucket: 'students-app-9354e.appspot.com',
      messagingSenderId: '51399176797',
      appId: '1:51399176797:web:761c67edf08c67d26a53b4',
      measurementId: 'G-5X9E365468'
    }

    // Initialize Firebase
    initializeApp(firebaseConfig)
    const db = getFirestore()
    const q = query(collection(db, 'students'))
    onSnapshot(q, (querySnapshot) => {
      this.students = []
      querySnapshot.forEach((doc) => {
        let student = {}
        student.name = doc.data().name
        student.image = doc.data().image
        student.rating = doc.data().rating
        student.docId = doc.id
        this.students.push(student)
      })
    })
  }
}
</script>

<style>

</style>
