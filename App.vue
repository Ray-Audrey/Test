<script setup>
import { ref, onMounted } from 'vue'

const url = 'http://localhost:8080/api/Student'
const students = ref([])
const name = ref('')
const age = ref('')
const editId = ref(null)

async function load() {
  const res = await fetch(url)
  students.value = await res.json()
}

async function save() {
  await fetch(editId.value ? `${url}/${editId.value}` : url, {
    method: editId.value ? 'PUT' : 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: name.value, age: Number(age.value) })
  })
  clearForm()
  load()
}

function edit(s) {
  name.value = s.name
  age.value = s.age
  editId.value = s.id
}

async function remove(id) {
  if (!confirm('Delete this student?')) return
  await fetch(`${url}/${id}`, { method: 'DELETE' })
  load()
}

function clearForm() {
  name.value = ''
  age.value = ''
  editId.value = null
}

onMounted(load)
</script>

<template>
  <h1>Student Management</h1>

  <form @submit.prevent="save">
    <input v-model="name" placeholder="Name" required />
    <input v-model="age" type="number" placeholder="Age" required />
    <button>{{ editId ? 'Update' : 'Add' }}</button>
    <button type="button" @click="clearForm">Clear</button>
  </form>

  <table border="1" cellpadding="8">
    <tr>
      <th>Name</th>
      <th>Age</th>
      <th>Actions</th>
    </tr>
    <tr v-for="s in students" :key="s.id">
      <td>{{ s.name }}</td>
      <td>{{ s.age }}</td>
      <td>
        <button @click="edit(s)">Edit</button>
        <button @click="remove(s.id)">Delete</button>
      </td>
    </tr>
  </table>
</template>