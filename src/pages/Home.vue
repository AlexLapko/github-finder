<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">

        <!-- errors -->
        <div class="error" v-if="error">
          <p>{{ error }}</p>
        </div>

        <!-- search -->
        <Search 
          :value="search"
          placeholder="Type username..."
          @search="search = $event"
        />
        <!-- buttons -->
        <button v-if="!repos" class="btn btnPrimary" @click="getRepos">Search!</button>
        <button v-else class="btn btnPrimary" @click="getRepos">Search Again!</button>

        <!-- user wrapper -->
        <div class="user__wrapper" v-if="repos">
          <div class="img">
            <img :src="user.avatar_url" alt="">
          </div>
          <a target="_blank" :href="user.html_url" class="name">{{user.name}}</a>
          <span>{{repos.length}}</span>
        </div>

        <!-- repos wrapper -->
        <div class="repos__wrapper" v-if="repos">
          <!-- item -->
          <div class="repos-item" v-for="repo in repos" :key="repo.id">
            <div class="repos-info">
              <a class="link" target="_blank" :href="repo.html_url">{{ repo.name }}</a>
              <span> {{ repo.stargazers_count }} ⭐</span>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import Search from '../components/Search'
import axios from 'axios'

export default {
  components: { Search },
  data() {
    return {
      search: '',
      error: '',
      user: null,
      repos: null
    }
  },
  methods: {
    getRepos() {
      axios.all([
        axios.get(`https://api.github.com/users/${this.search}`),
        axios.get(`https://api.github.com/users/${this.search}/repos`)
      ])
        .then(([user, repos]) => {
          this.user = user.data
          this.repos = repos.data
          this.error = null
        })
        .catch(err => {
          this.repos = null
          this.error = 'Can`t find this user'
        })
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  align-items: center;
  flex-direction: column;
}
button {
  margin-top: 40px;
}
.repos__wrapper {
  width: 400px;
  margin: 30px 0;
}
.repos-info {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
  padding: 10px 0;
  border-bottom: 1px solid #dbdbdb;
}
.error {
  margin-bottom: 20px;
}
.user__wrapper {
  display: flex;
  align-items: center;
  width: 400px;
  margin-top: 30px;
  & .img {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    margin-right: 20px;
    & img {
      width: 100%;
      height: 100%;
      border-radius: 50%;
    }
  }
  & span {
    margin-left: auto;
  }
}
</style>