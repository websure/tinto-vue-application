<template>
  <div class="wrapper">
    <h2>
      Idea's List
    </h2>
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
      this.ideasList = this.ideas;
    },
    isApiSuccess(val) {
      /* update idea tile when edit is success */
      if (val.action === "update") {
        this.editBox = { hide: true, ...val };
      } else if (val.action === "add") {
        this.sortingIdeas();
      }
    },
    sortingorder(val) {
      this.sortingIdeas(val);
    }
  },
  beforeUpdate() {
    this.sortingorder.length > 0 && this.sortingIdeas();
  },
  methods: {
    deleteIdea(id) {
      this.$emit("delete-idea", id);
    },
    updateIdea(obj) {
      this.$emit("update-idea", obj);
    },
    sortingIdeas(val = this.sortingorder) {
      /* sorting idas based on selection */
      if (this.sortingorder.length === 0) {
        return;
      }
      try {
        this.ideasList = this.ideas.sort(this.compare);
      } catch (e) {
        this.ideasList = this.ideas;
      }
    },
    compare(a, b) {
      const val = this.sortingorder;
      if (val[0] === "title") {
        // Use toUpperCase() to ignore character casing
        const titleA = a.title.toUpperCase();
        const titleB = b.title.toUpperCase();
        let comparison = 0;
        if (titleA > titleB) {
          comparison = val[1] === "asc" ? 1 : -1;
        } else if (titleA < titleB) {
          comparison = val[1] === "asc" ? -1 : 1;
        }
        return comparison;
      } else if (val[0] === "created_date") {
        return val[1] === "asc" ? a[val[0]] - b[val[0]] : b[val[0]] - a[val[0]];
      }
    }
  }
};
</script>

<style lang="less" scoped>
.wrapper {
  padding: 0.5rem;
  .tilesWrapper {
    display: flex;
    flex-wrap: wrap;
  }
}
</style>
