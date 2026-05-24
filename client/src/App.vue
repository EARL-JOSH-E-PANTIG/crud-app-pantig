<script setup>
import { ref, onMounted } from 'vue'
const API = 'http://localhost:4000/api/items'
const items = ref([])
const form = ref({ name: '', description: '', price: '' })
const editId = ref(null)
async function load() {
  items.value = await fetch(API).then(r => r.json())
}
async function save() {
  if (editId.value) {
    await fetch(`${API}/${editId.value}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form.value)
    })
    editId.value = null
  } else {
    await fetch(API, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form.value)
    })
  }
  form.value = { name: '', description: '', price: '' }
  load()
}
function startEdit(item) {
  editId.value = item.id
  form.value = { name: item.name, description: item.description, price: item.price }
}
async function remove(id) {
  await fetch(`${API}/${id}`, { method: 'DELETE' })
  load()
}
onMounted(load)
</script>

<template>
  <main>
    <h1>Items</h1>
    <form @submit.prevent="save" class="item-form">
      <input v-model="form.name" placeholder="Name" required />
      <input v-model="form.description" placeholder="Description" />
      <input v-model="form.price" placeholder="Price" type="number" step="0.01" min="0" />
      <button type="submit">{{ editId ? 'Update' : 'Add' }}</button>
    </form>
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Description</th>
          <th>Price</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in items" :key="item.id">
          <td>{{ item.name }}</td>
          <td>{{ item.description }}</td>
          <td class="price">₱{{ item.price != null ? Number(item.price).toFixed(2) : '0.00' }}</td>
          <td>
            <button class="btn-edit" @click="startEdit(item)">Edit</button>
            <button class="btn-delete" @click="remove(item.id)">Delete</button>
          </td>
        </tr>
        <tr v-if="items.length === 0">
          <td colspan="4" class="empty">No items found.</td>
        </tr>
      </tbody>
    </table>
  </main>
</template>

<style>
* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  background: #121212;
  padding: 2rem;
}

main {
  max-width: 800px;
  margin: 0 auto;
  background: #1e1e1e;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0,0,0,0.5);
}

h1 {
  font-size: 1.8rem;
  margin-bottom: 1.5rem;
  color: #ffffff;
}

.item-form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
  flex-wrap: wrap;
}

.item-form input {
  flex: 1;
  min-width: 120px;
  padding: 0.5rem 0.75rem;
  border: 1px solid #333;
  border-radius: 4px;
  font-size: 0.95rem;
  background: #2a2a2a;
  color: #ffffff;
}

.item-form input::placeholder { color: #666; }

.item-form input:focus {
  outline: none;
  border-color: #4a90e2;
}

.item-form button {
  padding: 0.5rem 1.2rem;
  background: #4a90e2;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 0.95rem;
  cursor: pointer;
}

.item-form button:hover { background: #357abd; }

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 0.75rem 1rem;
  text-align: left;
  border-bottom: 1px solid #2a2a2a;
  color: #e0e0e0;
}

th {
  background: #252525;
  font-weight: bold;
  color: #aaaaaa;
}

tbody tr:hover { background: #252525; }

.price { color: #4caf50; font-weight: bold; }

.btn-edit, .btn-delete {
  padding: 0.3rem 0.75rem;
  border: none;
  border-radius: 4px;
  font-size: 0.85rem;
  cursor: pointer;
  margin-right: 0.4rem;
}

.btn-edit { background: #1a3a5c; color: #4a90e2; }
.btn-delete { background: #3a1a1a; color: #e53935; }
.btn-edit:hover { background: #1e4a7a; }
.btn-delete:hover { background: #4a2020; }

.empty {
  text-align: center;
  color: #555;
  padding: 2rem;
}
</style>