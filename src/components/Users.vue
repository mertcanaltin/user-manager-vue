<template>
  <div>
    <form @submit.prevent="addUser">
      <input type="text" v-model="newUser.name" placeholder="Name">
      <input type="text" v-model="newUser.email" placeholder="Email">
      <button type="submit">Add User</button>
    </form>

    <ul>
      <li v-for="user in users" :key="user.id">
        {{ user.name }} ({{ user.email }})
        <button @click="deleteUser(user.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: [],
      newUser: {
        name: '',
        email: ''
      }
    }
  },
  
  mounted() {
    this.fetchUserData()
  },

  methods: {
    fetchUserData() {
      fetch('https://jsonplaceholder.typicode.com/users')
        .then(response => response.json())
        .then(users => {
          this.users = users
        })
        .catch(error => {
          console.error('Error fetching user data:', error)
        })
    },

    addUser() {
      const newUserId = this.users.length + 1

      const newUser = {
        id: newUserId,
        name: this.newUser.name,
        email: this.newUser.email
      }

      this.users.push(newUser)

      this.newUser.name = ''
      this.newUser.email = ''
    },

    deleteUser(userId) {
      const updatedUsers = this.users.filter(user => user.id !== userId)
      this.users = updatedUsers
    }
  }
}
</script>
