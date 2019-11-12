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
  props: ["ideas", "isApiSuccess", "sortingorder"],
  data() {
    return {
      ideasList: this.ideas,
      editBox: {}
    };
  },
  watch: {
    ideas(value) {
      /* update ideas */
      console.log("add ideas");
      this.ideasList = this.ideas;
    },
    isApiSuccess(val) {
      console.log("isApiSuccess ", val);
      if (val.action === "update") {
        /* disbale edit box in ideas list */
        this.editBox = { hide: true, ...val };
      }
    },
    sortingorder(val) {
      console.log("sortingorder ", val);
      this.sortingIdeas(val);
    }
  },
  beforeUpdate() {
    console.log("beforeUpdate");
    this.sortingorder.length > 0 && this.sortingIdeas();
  },
  methods: {
    deleteIdea(id) {
      // console.log("deleteIdea ", id);
      this.$emit("delete-idea", id);
    },
    updateIdea(obj) {
      this.$emit("update-idea", obj);
    },
    sortingIdeas(val = this.sortingorder) {
      console.log("sortingIdeas");
      this.ideas.sort(function(a, b) {
        if (val[0] === "title") {
          return val[1] === "asc"
            ? a[val[0]] - b[val[0]]
            : b[val[0]] - a[val[0]];
        } else if (val[0] === "created_date") {
          return val[1] === "asc"
            ? a[val[0]] - b[val[0]]
            : b[val[0]] - a[val[0]];
        }
      });
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
