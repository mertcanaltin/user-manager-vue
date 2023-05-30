<template>
  <div>
    <div class="container mx-auto">
      <div class="flex flex-wrap">
        <div class="w-full md:w-1/2 px-4 mt-3">
          <form @submit.prevent="addUser">
            <div class="mb-4 border-2 p-3">
              <label for="name" class="block text-gray-700">Name</label>
              <input type="text" v-model="newUser.name" class="form-input mt-1 block w-full" id="name" placeholder="Name">
            </div>
            <div class="mb-4 border-2 p-3">
              <label for="email" class="block text-gray-700">Email</label>
              <input type="text" v-model="newUser.email" class="form-input mt-1 block w-full" id="email"
                placeholder="Email">
            </div>
            <button type="submit" class="btn btn-primary border-2 p-1 mr-2 text-green-400">Add User</button>
            <button type="button" class="btn btn-primary border-2 p-1" @click="clearJSON">Clear JSON</button>
          </form>

          <ul>
            <li class="border-2 p-1 mt-2 mb-2" v-for="user in users" :key="user.id">
              {{ user.name }} ({{ user.email }})
              <button @click="deleteUser(user.id)" class="btn btn-danger text-red-400">Delete</button>
            </li>
          </ul>
        </div>
        <div class="w-full md:w-1/2 px-4 mt-3">
          <button class="btn btn-primary border-2 p-1" @click="copyJSON">Copy All JSON</button>
          <div class="json-container">
            <pre>{{ jsonOutput }}</pre>
          </div>
        </div>
      </div>
    </div>


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

  computed: {
    jsonOutput() {
      return JSON.stringify(this.users, null, 2)
    }
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
      // Generate a new unique ID for the user
      const newUserId = this.users.length + 1

      // Create a new user object with the entered data and generated ID
      const newUser = {
        id: newUserId,
        name: this.newUser.name,
        email: this.newUser.email
      }

      // Add the new user to the users array
      this.users.push(newUser)

      // Reset the input fields
      this.newUser.name = ''
      this.newUser.email = ''
    },

    deleteUser(userId) {
      const updatedUsers = this.users.filter(user => user.id !== userId)
      this.users = updatedUsers
    },

    clearJSON() {
      this.users = []
    },

    copyJSON() {
      const el = document.createElement('textarea')
      el.value = this.jsonOutput
      document.body.appendChild(el)
      el.select()
      document.execCommand('copy')
      document.body.removeChild(el)
    }
  }
}
</script>

<style>
.json-container {
  margin-top: 20px;
  border: 1px solid #ccc;
  padding: 10px;
  font-family: monospace;
  white-space: pre-wrap;
}
</style>
