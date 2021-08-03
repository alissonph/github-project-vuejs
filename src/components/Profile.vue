<template>
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
            <small v-if="user.created_at" class="text-muted"
              >Membro desde: {{ user.created_at | formatDate }}</small
            >
          </div>
          <p class="card-text"></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Profile",
  props: {
    user: Object,
  },
};
</script>

<style scoped>
</style>