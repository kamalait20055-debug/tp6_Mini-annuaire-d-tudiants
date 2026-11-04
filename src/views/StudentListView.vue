<template>
  <h1>Annuaire des étudiants</h1>

  <form @submit.prevent="ajouterEtudiant">
    <input v-model="newStudent.firstName" placeholder="Prénom" />
    <input v-model="newStudent.lastName" placeholder="Nom" />
    <input v-model="newStudent.filiere" placeholder="Filière" />
    <input v-model.number="newStudent.year" type="number" min="1" placeholder="Année" />
    <input v-model="newStudent.email" type="email" placeholder="Email" />
    <button type="submit">Ajouter</button>
  </form>

  <div class="toolbar">
    <input v-model="recherche" placeholder="Rechercher par nom ou prénom" />
    <select v-model="tri">
      <option value="lastName">Trier par nom</option>
      <option value="filiere">Trier par filière</option>
    </select>
  </div>

  <StudentCard v-for="student in etudiantsAffiches" :key="student.id" :student="student" />
</template>

<script>
import StudentCard from '../components/StudentCard.vue'
import { store } from '../store/students.js'

export default {
  name: 'StudentListView',
  components: { StudentCard },
  data() {
    return {
      store,
      recherche: '',
      tri: 'lastName',
      newStudent: { firstName: '', lastName: '', filiere: '', year: 1, email: '' }
    }
  },
  computed: {
    // Filtrer d'abord (filter() renvoie un nouveau tableau), puis trier ce
    // nouveau tableau : store.students n'est jamais modifié par le tri.
    etudiantsAffiches() {
      const texte = this.recherche.trim().toLowerCase()

      return store.students
        .filter(s =>
          s.firstName.toLowerCase().includes(texte) ||
          s.lastName.toLowerCase().includes(texte)
        )
        .sort((a, b) =>
          a[this.tri].localeCompare(b[this.tri]) ||
          a.lastName.localeCompare(b.lastName)
        )
    }
  },
  methods: {
    ajouterEtudiant() {
      const firstName = this.newStudent.firstName.trim()
      const lastName = this.newStudent.lastName.trim()
      if (!firstName || !lastName) return

      const id = store.students.reduce((max, s) => Math.max(max, s.id), 0) + 1

      store.students.push({
        id,
        firstName,
        lastName,
        filiere: this.newStudent.filiere.trim(),
        year: this.newStudent.year,
        email: this.newStudent.email.trim()
      })

      this.newStudent = { firstName: '', lastName: '', filiere: '', year: 1, email: '' }
    }
  }
}
</script>
