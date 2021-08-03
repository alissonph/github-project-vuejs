<template>
  <div class="container">
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
    <div v-if="user.id" class="card mb-3">
      <div class="row g-0">
        <div class="col-md-4">
          <img
            :src="user.avatar_url"
            class="img-fluid rounded-start"
            alt="User profile image"
          />
        </div>
        <div class="col-md-8">
          <div class="card-body">
            <h5 class="card-title">{{ user.name }}</h5>
            <h6 class="card-subtitle mb-2 text-muted">
              <a :href="user.html_url" target="_blank">@{{ user.login }}</a>
            </h6>
            <div class="card-text d-flex flex-column">
              <p>
                {{ user.followers }} seguidores - {{ user.following }} seguindo
              </p>
              <p v-if="user.bio">{{ user.bio }}</p>
              <small v-if="user.location" class="text-muted">{{
                user.location
              }}</small>
              <small v-if="user.blog" class="text-muted"
                ><a :href="user.blog">{{ user.blog }}</a></small
              >
            </div>
            <p class="card-text"></p>
          </div>
        </div>
      </div>
    </div>
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">
          Repositórios
          <span
            v-if="user.public_repos"
            class="badge rounded-pill bg-secondary"
            >{{ user.public_repos }}</span
          >
        </h5>
        <div class="card-text">
          <span v-if="!repos.length">Nenhum repositório encontrado.</span>

          <div class="row">
            <div
              class="col-sm-6 col-md-6 col-lg-4 mb-4"
              v-for="repo in repos"
              :key="repo.id"
            >
              <div class="card repository">
                <div class="card-body">
                  <h5 class="card-title">
                    <a :href="repo.html_url" target="_blank">{{ repo.name }}</a>
                  </h5>
                  <div class="card-text">
                    <p>
                      {{ repo.description }}
                    </p>
                    <small v-if="true" class="text-muted"
                      >Última atualização:
                      {{ repo.updated_at | formatDate }}</small
                    >
                  </div>
                </div>
                <div class="card-footer">
                  {{
                    repo.language ? repo.language : "Linguagem não informada"
                  }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Home",
  data: function () {
    return {
      username: "",
      user: {},
      repos: [],
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
        this.errorMessage = "";
        this.getUser();
        this.getRepos();
      }
    },
    getUser() {
      const url = `https://api.github.com/users/${this.username}`;
      this.loader.user = true;
      axios
        .get(url)
        .then((response) => (this.user = response.data))
        .catch(() => {
          this.clean();
          this.errorMessage = "Usuário não encontrado.";
        })
        .finally(() => (this.loader.user = false));
    },
    getRepos() {
      const url = `https://api.github.com/users/${this.username}/repos`;
      this.loader.search = true;
      axios
        .get(url)
        .then((response) => (this.repos = response.data))
        .catch(() => {
          this.clean();
          this.errorMessage = "Usuário não encontrado.";
        })
        .finally(() => (this.loader.search = false));
    },
    clean() {
      this.username = "";
      this.user = {};
      this.repos = [];
      this.errorMessage = "";
    },
  },
};
</script>

<style scoped>
.repository {
  min-height: 200px !important;
}

a {
  text-decoration: none;
}

.clean {
  margin-left: 10px !important;
}
</style>
