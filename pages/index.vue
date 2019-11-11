<template>
  <div class="container">
    <div>
      <AddIdea @add-idea="addIdea" />
      <a-card :loading="loading" class="dashboard">
        <IdeaDashboard
          :ideas="ideas"
          :is-api-success="isApiSuccess"
          @delete-idea="deleteIdea"
          @update-idea="updateIdea"
        />
      </a-card>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import AddIdea from "../components/AddIdea";
import IdeaDashboard from "../components/Ideas";

export default {
  head() {
    return {
      title: "Idea Dashboard"
    };
  },
  components: {
    AddIdea,
    IdeaDashboard
  },
  data() {
    return {
      loading: false,
      ideas: [],
      isApiSuccess: {}
    };
  },
  async created() {
    try {
      this.loading = true;
      const res = await axios.get(`/api/v1/idea`);
      console.log("resp ", res.data.result);
      this.loading = false;
      this.ideas = res.data.result;
    } catch (err) {
      console.log(err);
    }
  },
  methods: {
    async deleteIdea(id) {
      console.log("deleteIdea ", id);
      if (!id) return;
      try {
        const res = await axios.delete(`/api/v1/idea/${id}`);

        const updatedList = this.ideas.filter(val => val.id !== res.data.id);
        console.log("after delete ", updatedList);
        this.ideas = updatedList;
      } catch (err) {
        console.log("unable to delete ", err);
      }
    },
    async addIdea(value) {
      console.log("addIdea ", value);
      try {
        const res = await axios.post("/api/v1/idea/", { params: value });
        console.log("after add ", res);
        this.ideas.splice(0, 0, res.data);
      } catch (err) {
        console.log("unable to add idea ", err);
      }
    },
    async updateIdea(obj) {
      console.log("updateIdea ", obj);
      try {
        const res = await axios.put("/api/v1/idea/", { params: obj });
        console.log("after update ", res.data);

        const tempArr = this.ideas;

        const idx = this.ideas.findIndex(val => val.id === obj.id);
        tempArr[idx] = res.data;
        this.ideas = tempArr;
        this.isApiSuccess = { ...res.data, action: "update" };
        console.log("after update ", this.ideas);
      } catch (err) {
        console.log("unable to add idea ", err);
      }
    }
  }
};
</script>

<style scoped>
.dashboard {
  margin: 0.5rem;
}
</style>
