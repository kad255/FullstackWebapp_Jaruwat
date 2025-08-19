<template>
  <div class="dashboard">
    <h2>User Dashboard</h2>

    <table border="1" cellpadding="8">
      <thead>
        <tr>
          <th>No</th>
          <th>Full Name</th>
          <th>Username</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(user, index) in users" :key="user.id">
          <td>{{ index + 1 }}</td>
          <td v-if="editId !== user.id">{{ user.fname }} {{ user.lname }}</td>
          <td v-else>
            <input v-model="editForm.fname" placeholder="First Name" />
            <input v-model="editForm.lname" placeholder="Last Name" />
          </td>
          <td v-if="editId !== user.id">{{ user.username }}</td>
          <td v-else>
            <input v-model="editForm.username" placeholder="Username" />
          </td>
          <td>
            <button v-if="editId !== user.id" @click="startEdit(user)">Edit</button>
            <button v-else @click="saveEdit(user.id)">Save</button>
            <button v-if="editId === user.id" @click="cancelEdit">Cancel</button>
            <button @click="deleteUser(user.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const users = ref([]);
const editId = ref(null);
const editForm = ref({ fname: '', lname: '', username: '' });

const fetchUsers = async () => {
  const res = await fetch('http://localhost:3000/users');
  users.value = await res.json();
};

const startEdit = (user) => {
  editId.value = user.id;
  editForm.value = { ...user };
};

const cancelEdit = () => {
  editId.value = null;
  editForm.value = { fname: '', lname: '', username: '' };
};

const saveEdit = async (id) => {
  await fetch(`http://localhost:3000/users/${id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(editForm.value),
  });
  editId.value = null;
  fetchUsers();
};

const deleteUser = async (id) => {
  if (confirm('คุณต้องการลบ user นี้ใช่หรือไม่?')) {
    await fetch(`http://localhost:3000/users/${id}`, { method: 'DELETE' });
    fetchUsers();
  }
};

onMounted(fetchUsers);
</script>

<style scoped>
.dashboard {
  max-width: 1000px;
  margin: 40px auto;
  padding: 20px;
  background: #ffffff;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  border-radius: 8px;
}

h2 {
  text-align: center;
  margin-bottom: 20px;
  color: #a5a5a5; /* เทาเข้ม อ่านง่าย */
  font-size: 24px;
  font-weight: 600;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
  background-color: #ffffff;
  border-radius: 6px;
  overflow: hidden;
}

thead {
  background-color: #00aaff;
  color: rgb(255, 255, 255);
}

th, td {
  padding: 12px 16px;
  border-bottom: 1px solid #ffffff;
  text-align: left;
}

tr:hover {
  background-color: #00ff77;
}

button {
  margin: 0 4px;
  padding: 6px 12px;
  background-color: #00b894;
  border: none;
  color: rgb(255, 255, 255);
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

button:hover {
  background-color: #019875;
  transform: scale(1.03);
}

input[type="text"] {
  padding: 6px 10px;
  width: 90%;
  border: 1px solid #ffffff;
  border-radius: 6px;
  font-size: 14px;
  transition: border-color 0.3s ease;
}

input[type="text"]:focus {
  border-color: #00aaff;
  outline: none;
}

@media (max-width: 768px) {
  .dashboard {
    padding: 10px;
  }

  table, thead, tbody, th, td, tr {
    display: block;
  }

  th {
    display: none;
  }

  td {
    position: relative;
    padding-left: 50%;
    text-align: right;
  }

  td::before {
    position: absolute;
    left: 10px;
    top: 12px;
    white-space: nowrap;
    font-weight: bold;
    color: #ffffff;
  }

  td:nth-of-type(1)::before { content: "#"; }
  td:nth-of-type(2)::before { content: "Full Name"; }
  td:nth-of-type(3)::before { content: "Username"; }
  td:nth-of-type(4)::before { content: "Actions"; }
}
</style>

