<template>
  <div class="container">
    <div>
      <AddIdea @add-idea="addIdea" />
      <SortIdeasList @sort="val => (sortingorder = val)" />
      <a-card :loading="loading" class="dashboard">
        <IdeaDashboard
          :ideas="ideas"
          :is-api-success="isApiSuccess"
          :sortingorder="sortingorder"
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
import SortIdeasList from "../components/SortIdeaList";

export default {
  head() {
    return {
      title: "Idea Dashboard"
    };
  },
  components: {
    AddIdea,
    IdeaDashboard,
    SortIdeasList
  },
  data() {
    return {
      loading: false,
      ideas: [],
      isApiSuccess: {},
      sortingorder: []
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
    showMessage(txt, type = "success") {
      type === "success"
        ? this.$message.success(txt, 3)
        : this.$message.error(txt, 3);
    },
    async deleteIdea(id) {
      console.log("deleteIdea ", id);
      if (!id) return;
      try {
        const res = await axios.delete(`/api/v1/idea/${id}`);

        const updatedList = this.ideas.filter(val => val.id !== res.data.id);
        console.log("after delete ", updatedList);
        this.ideas = updatedList;
        this.showMessage("Idea deleted successfully");
      } catch (err) {
        console.log("unable to delete ", err);
        this.showMessage("Error in deleting ", "error");
      }
    },
    async addIdea(value) {
      console.log("addIdea ", value);
      try {
        const res = await axios.post("/api/v1/idea/", { params: value });
        console.log("after add ", res);
        this.ideas.splice(0, 0, res.data);
        this.showMessage("Idea added successfully");
      } catch (err) {
        console.log("unable to add idea ", err);
        this.showMessage("Error in adding new Idea ", "error");
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
        this.showMessage("Idea updated successfully");
        console.log("after update ", this.ideas);
      } catch (err) {
        console.log("unable to add idea ", err);
        this.showMessage("Error in updating ", "error");
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
