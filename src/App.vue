<template>
  <div id="app">
    <div>
      <input v-model="profil" placeholder="modifiez-moi">
      <button @click="validate">
        Valider
      </button>
    </div>
    <div v-if="userInfos != null">
      <p>
        Name: {{ userInfos.name }}<br/>
        Created date: {{ userInfos.created_date }}<br/>
        avatar: {{ userInfos.avatar }}<br/>
      </p>
      <h3> Repos list </h3>
      <div v-for="(repo, index) in userInfos.list_repos" :key="index">
        <p>
          <b>Url Repo:</b> {{ repo.url }}<br/>
          <b>Description:</b> {{ repo.description }}<br/>
          <b>Created at:</b> {{ repo.created_at }}<br/>
        </p>
        <div v-if="repo.has_issues">
          <p> test</p>
          {{ getIssues(repo.issues_url, repo.open_issues_count) }}
          <!-- if has issues, show title of more recent assigned user -->
        </div>
        <hr />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'App',
  data() {
    return {
      profil: "",
      userInfos: null,
    };
  },
  methods: {
    async validate() {
      try {
        let response = await axios.get(`https://api.github.com/users/${this.profil}`);

        let repo_list = await axios.get(response.data.repos_url);
  
        this.userInfos = {
          name: response.data.name,
          created_date: response.data.created_at,
          avatar: response.data.avatar_url,
          list_repos: repo_list.data,
        };
      } catch(e) {
        console.error(e);
        this.userInfos = null;
      }
    },
    getIssues(issues_url, open_issues_count) {
      return issues_url.replace("{/number}", "/" + open_issues_count);
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
