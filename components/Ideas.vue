<template>
  <div class="wrapper">
    <Grid>
      <Grid.Row class="tilesWrapper">
        <ListIdeas
          v-for="idea in ideasList"
          :id="idea.id"
          :key="idea.id"
          :idea="idea"
          :edit-box="editBox"
          @delete-idea="deleteIdea"
          @update-idea="updateIdea"
        />
      </Grid.Row>
    </Grid>
  </div>
</template>

<script>
import ListIdeas from "./ListIdeas";
export default {
  name: "IdeaDashboard",
  components: {
    ListIdeas
  },
  props: ["ideas", "isApiSuccess"],
  data() {
    return {
      ideasList: this.ideas,
      editBox: {}
    };
  },
  watch: {
    ideas(value) {
      /* update ideas */
      this.ideasList = this.ideas;
    },
    isApiSuccess(val) {
      console.log("isApiSuccess ", val);
      if (val.action === "update") {
        /* disbale edit box in ideas list */
        this.editBox = { hide: true, ...val };
      }
    }
  },
  methods: {
    deleteIdea(id) {
      // console.log("deleteIdea ", id);
      this.$emit("delete-idea", id);
    },
    updateIdea(obj) {
      this.$emit("update-idea", obj);
    }
  }
};
</script>

<style scoped>
.wrapper {
  padding: 0.5rem;
}
.tilesWrapper {
  display: flex;
  flex-wrap: wrap;
}
</style>
