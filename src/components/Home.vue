<template>
  <div class="container my-3">
    <div class="card mb-3">
      <div class="card-body">
        <h5 class="card-title">GitHub Repositórios</h5>
        <div class="card-text">
          <form @submit.prevent.stop="search">
            <div class="row">
              <div class="col-xs-12 col-sm-6">
                <label for="username" class="form-label">Username</label>
                <input
                  type="text"
                  id="username"
                  v-model="username"
                  class="form-control"
                  placeholder="Username"
                />
              </div>
              <div class="col-xs-12 col-sm-6 align-self-end">
                <button
                  type="submit"
                  class="btn btn-primary"
                  :disabled="loadingData"
                >
                  Pesquisar
                  <span
                    v-if="loadingData"
                    class="spinner-border spinner-border-sm"
                    role="status"
                    aria-hidden="true"
                  ></span>
                </button>
                <button
                  type="button"
                  class="btn btn-warning clean"
                  @click="clean"
                >
                  Limpar
                </button>
              </div>
            </div>
            <div class="row mt-3" v-if="errorMessage">
              <div class="col-6">
                <div class="alert alert-danger" role="alert">
                  {{ errorMessage }}
                </div>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
    <Profile :user="user" />

    <Repositories :user="user" :repos="repos" :hasMore="hasMore" @loadMore="getRepos" />
  </div>
</template>

<script>
import axios from "axios";
import Profile from "./Profile.vue";
import Repositories from "./Repositories.vue";

export default {
  name: "Home",
  components: {
    Profile,
    Repositories,
  },
  data: function () {
    return {
      username: "",
      user: {},
      repos: [],
      page: 0,
      hasMore: false,
      loader: {
        user: false,
        search: false,
      },
      errorMessage: "",
    };
  },

  computed: {
    loadingData: function () {
      return this.loader.user || this.loader.search;
    },
  },

  methods: {
    search() {
      if (this.username) {
        this.clean();
        this.page = 0;
        this.getUser();
      }
    },
    getUser() {
      const url = `https://api.github.com/users/${this.username}`;
      this.loader.user = true;
      axios
        .get(url)
        .then((response) => {
          this.user = response.data
          this.getRepos();
        })
        .catch((error) => {
          this.clean();
          if (error.response.status === 403) {            
            this.errorMessage = "Limite da API do GitHub excedido, tente novamente mais tarde.";
          } else {
            this.errorMessage = "Usuário não encontrado.";
          }
        })
        .finally(() => (this.loader.user = false));
    },
    getRepos() {
      this.page++;
      const url = `https://api.github.com/users/${this.username}/repos`;
      this.loader.search = true;
      axios
        .get(url, { params: { page: this.page }})
        .then((response) => {
          this.repos = [ ...this.repos, ...response.data ];
        })
        .catch((error) => {
          this.clean();
          if (error.response.status === 403) {            
            this.errorMessage = "Limite da API do GitHub excedido, tente novamente mais tarde.";
          } else {
            this.errorMessage = "Repositório não encontrado.";
          }
        })
        .finally(() => (this.loader.search = false));
    },
    clean() {
      this.user = {};
      this.repos = [];
      this.errorMessage = "";
    }
  },

  watch: {
    page: function () {
      this.hasMore = (30 * this.page) < this.user.public_repos;
    }
  }
};
</script>

<style scoped>
.clean {
  margin-left: 10px !important;
}
</style>
