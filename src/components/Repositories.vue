<template>
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
                {{ repo.language ? repo.language : "Linguagem não informada" }}
              </div>
            </div>
          </div>
        </div>
        <div class="d-flex flex-row justify-content-center" v-if="hasMore">
          <button
            type="button"
            class="btn btn-primary align-self-center"
            @click="loadMore"
          >
            Carregar Mais...
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Repositories",
  props: {
    user: Object,
    repos: Array,
    hasMore: Boolean,
  },
  methods: {
    loadMore() {
      this.$emit("loadMore");
    },
  },
};
</script>

<style scoped>
.repository {
  min-height: 200px !important;
}
</style>
