<template>
  <div class="container">
    <a-layout>
      <a-layout-header>
        <a-row type="flex" justify="end">
          <a-col
            :xl="{ span: 6, pull: 8 }"
            :lg="{ span: 6, pull: 8 }"
            :md="{ span: 8, pull: 6 }"
            :sm="{ span: 0 }"
            :xs="{ span: 0 }"
          >
            <h2>Idea's Dashboard</h2>
          </a-col>
          <a-col
            :xl="{ span: 3 }"
            :lg="{ span: 3, offset: 2 }"
            :md="{ span: 5 }"
            :sm="{ span: 8 }"
            :xs="{ span: 12 }"
          >
            <SortIdeasList @sort="val => (sortingorder = val)" />
          </a-col>
          <a-col
            :xl="{ span: 2, offset: 2 }"
            :lg="{ span: 3, offset: 2 }"
            :md="{ span: 4, offset: 1 }"
            :sm="{ span: 6, offset: 1 }"
            :xs="{ span: 8, offset: 2 }"
          >
            <AddIdea @add-idea="addIdea" />
          </a-col>
        </a-row>
      </a-layout-header>
      <a-layout-content>
        <div>
          <a-card :loading="loading" class="dashboard">
            <IdeaDashboard
              v-if="ideas.length > 0"
              :ideas="ideas"
              :is-api-success="isApiSuccess"
              :sortingorder="sortingorder"
              @delete-idea="deleteIdea"
              @update-idea="updateIdea"
            />
            <h3 v-if="ideas.length === 0 && !loading && !apiErr">
              No idea's to display
            </h3>
            <h3 v-if="apiErr">
              Error in fetching data
            </h3>
          </a-card>
        </div>
      </a-layout-content>
    </a-layout>
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
      sortingorder: [],
      apiErr: false
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
      this.loading = false;
      this.apiErr = true;
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

<style lang="less" scoped>
.container {
  padding: 0px 1rem;
  h2 {
    color: #ffff;
  }
}
</style>
