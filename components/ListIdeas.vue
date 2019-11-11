<template>
  <div class="tilesWrapper">
    <a-card :loading="loading" class="tiles">
      <h3
        v-if="!editTitle"
        @mouseover.once="titleHovered"
        @click="eleClick('title')"
      >
        {{ ideas.title }}
      </h3>
      <a-icon
        v-if="showIcon && !editTitle"
        type="close"
        title="close"
        class="close"
        @click="$emit('delete-idea', `${id}`)"
      />
      <a-input
        v-if="editTitle"
        ref="titleInputRef"
        v-model="title"
        class="titleInput"
        @blur="inputBlur(title, idea.title, 'title')"
      />
      <h5>{{ ideas.created_date }}</h5>
      <div class="ideaWrapper" @click="eleClick('body')">
        <span v-if="!editDescription" class="ideaBody" :title="ideas.body">
          {{ ideas.body }}
        </span>
      </div>
      <a-input
        v-if="editDescription"
        ref="descritpionInputRef"
        v-model="description"
        class="titleInput"
        @blur="inputBlur(description, idea.body, 'body')"
      />
    </a-card>
  </div>
</template>

<script>
export default {
  name: "ListIdeas",
  props: ["idea", "id", "editBox"],
  data() {
    const date = new Date(this.idea.created_date);
    const x = {
      ...this.idea,
      created_date: date.toLocaleString("en-US")
    };
    return {
      ideas: x,
      loading: false,
      showIcon: false,
      editTitle: false,
      editDescription: false,
      title: this.idea.title,
      description: this.idea.body
    };
  },
  watch: {
    editTitle(value) {
      if (value) {
        setTimeout(() => {
          /* focus on title input box on title click */
          this.$refs.titleInputRef.$el.focus();
        }, 100);
      }
    },
    editDescription(value) {
      if (value) {
        setTimeout(() => {
          /* focus on title input box on title click */
          this.$refs.descritpionInputRef.$el.focus();
        }, 100);
      }
    },
    editBox(val) {
      console.log("editBox ", val);
      if (val.hide && val.id === this.ideas.id) {
        this.ideas = this.computeState(val);
        // this.ideas.title = val.title;
        // this.ideas.body = val.body;
        this.title = val.title;
        this.description = val.body;
        this.editDescription = false;
        this.editTitle = false;
      }
    }
  },
  methods: {
    computeState(obj) {
      const date = new Date(obj.created_date);
      const x = {
        ...obj,
        created_date: date.toLocaleString("en-US")
      };
      return x;
    },
    titleHovered() {
      this.showIcon = true;
    },
    inputBlur(newVal, oldVal, attr) {
      console.log("blur ", newVal, oldVal, attr);
      if (newVal !== oldVal) {
        /* update title */
        console.log("changed ", newVal, oldVal, attr, this.idea);
        const obj = { id: this.idea.id };
        obj[attr] = newVal;
        this.$emit("update-idea", obj);
      } else {
        this.editTitle = false;
        this.editDescription = false;
      }
    },
    eleClick(type) {
      console.log("click ", type, this.editTitle);

      if (type === "title" && !this.editTitle && !this.editDescription) {
        this.editTitle = !this.editTitle;
      } else if (type === "body" && !this.editTitle && !this.editDescription) {
        this.editDescription = !this.editDescription;
      }
      // if (type === "body" && !this.editTitle) {
      //   this.editDescription = !this.editDescription;
      // } else if (type === "title" && !this.editDescription && !this.editTitle) {
      //   this.editTitle = !this.editTitle;
      // }
    }
  }
};
</script>

<style>
.tiles {
  height: 150px;
  width: 150px;
  margin: 10px;
}
.tiles .ant-card-body {
  padding: 0.5rem !important;
}

.tiles .close {
  /* position: relative;
  top: -70px;
  display: inline; */
  float: right;
  padding: 6px;
}
.tiles h3 {
  display: inline-block;
}
.titleInput:hover {
  border-color: #cccc;
}
.ideaBody {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.ideaWrapper {
  display: flex;
}
</style>
