<template>
  <SignupModal v-if="openSignup" @signup="signupUser" v-bind:title="'Créer un utilisateur'" v-bind:from-admin="true"
               v-bind:open="openSignup" @close="openSignup = false"/>

  <div class="container mx-auto">
    <div class="card">
      <div class="flex pb-16 justify-between">
        <h1 class="title text-left uppercase font-bold">Gestion des utilisateurs</h1>
        <button @click="openSignup = true" type="button" class="button uppercase">Créer un utilisateur</button>
      </div>

      <div class="mb-10">
        <label class="label">
          Filtrer par rôle
          <select class="input-select mt-3" name="filterByRole" id="filterByRole" v-model="filterByRole">
            <option selected value="">TOUT</option>
            <option value="ADMIN">Administrateur</option>
            <option value="VENDEUR">Vendeur</option>
            <option value="CLIENT">Client</option>
          </select>
        </label>
      </div>

      <div class="overflow-auto">
        <table class="w-full">
          <thead>
          <tr class="font-bold uppercase border-b border-gray-300">
            <th>ID</th>
            <th>Mail</th>
            <th>Nom</th>
            <th>Prenom</th>
            <th>Num. téléphone</th>
            <th>Date de naissance</th>
            <th>Role</th>
          </tr>
          </thead>
          <tr class="text-left border-b border-gray-200 hover:bg-gray-50 transition cursor-pointer"
              v-for="user in usersFiltered">
            <td>{{ user._id }}</td>
            <td>{{ user.mail }}</td>
            <td>{{ user.lastName }}</td>
            <td>{{ user.firstName }}</td>
            <td>{{ user.phoneNumber }}</td>
            <td>{{ new Date(user.birthday).toLocaleDateString() }}</td>
            <td>{{ user.role }}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import SignupModal from "../../../components/SignupModal.vue"

export default {
  name: 'GetAllUsers',
  components: { SignupModal },
  data() {
    return {
      users: [],
      usersNotFiltered: [],
      user: null,
      openSignup: false,
      filterByRole: ""
    }
  },
  methods: {
    async getUsers() {
      try {
        const response = await this.$store.dispatch('getUsers')
        this.users = response.data
        this.usersNotFiltered = response.data
      } catch (e) {
        console.error(e)
      }
    },

    signupUser(user) {
      this.user = user
      this.openSignup = false
    },
  },
  created() {
    this.getUsers()
  },
  computed: {
    usersFiltered() {
      return this.filterByRole === "" ? this.usersNotFiltered : this.users.filter(user => user.role === this.filterByRole)
    }
  }
}
</script>
