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

          <div class="mt-3">
            <draggable v-model="users" :options="dragOptions" item-key="id">
              <template #item="{ element, index }">
                <li class="border-2 p-1 mt-2 mb-2 cursor-grab">
                  {{ element.name }} ({{ element.email }})
                  <button @click="deleteUser(index)" class="btn btn-danger text-red-400">Delete</button>
                </li>
              </template>
            </draggable>
          </div>
        </div>
        <div class="w-full md:w-1/2 px-4 mt-3">
          <button class="btn btn-primary border-2 p-1" @click="copyJSON">Copy All JSON</button>
          <div class="json-container">
            <pre>{{ jsonOutput }}</pre>
          </div>
        </div>
      </div>
    </div>

    <div v-if="showComponent" class="bg-indigo-900 text-center py-4 lg:px-4 fixed top-0 left-0 w-full">
      <div class="p-2 bg-indigo-800 items-center text-indigo-100 leading-none lg:rounded-full flex lg:inline-flex" role="alert">
        <span class="flex rounded-full bg-indigo-500 uppercase px-2 py-1 text-xs font-bold mr-3">✅</span>
        <span class="font-semibold mr-2 text-left flex-auto">JSON copied successfully</span>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  components: {
    draggable
  },

  data() {
    return {
      users: [],
      newUser: {
        name: '',
        email: ''
      },
      showComponent: false,
      dragOptions: {
        handle: '.drag-handle'
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

      this.showComponent = true
      setTimeout(() => {
        this.showComponent = false
      }, 2000)
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

.drag-handle {
  cursor: move;
  display: inline-block;
  padding: 0 4px;
}
</style>
