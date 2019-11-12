<template>
  <div class="tilesWrapper">
    <a-card
      :loading="loading"
      class="tiles"
      :style="{
        width:
          editDescription || editTitle || showDescriptionErr
            ? '300px !important'
            : '150px',
        height: editDescription ? '200px !important' : '150px',
        background: '#F7F8FB'
      }"
    >
      <h2
        v-if="!editTitle"
        @mouseover.once="titleHovered"
        @click="eleClick('title')"
      >
        {{ ideas.title }}
      </h2>
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
      <span v-if="showTitleErr" class="titleErr">
        Title cannot be empty
      </span>
      <h5>{{ ideas.created_date }}</h5>
      <div class="ideaWrapper" @click="eleClick('body')">
        <span v-if="!editDescription" class="ideaBody" :title="ideas.body">
          {{ ideas.body }}
        </span>
      </div>
      <a-textarea
        v-if="editDescription"
        ref="descritpionInputRef"
        v-model="description"
        class="titleInput"
        :rows="4"
        @blur="inputBlur(description, idea.body, 'body')"
        @change="inputChanged"
      />
      <span
        v-if="
          editDescription && description.length > 125 && !showDescriptionErr
        "
        class="titleWarning"
      >
        remaining characters : {{ 140 - description.length }}
      </span>
      <span v-if="showDescriptionErr" class="titleErr">
        Description is mandatory and max of 140 characters allowed
      </span>
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
      description: this.idea.body,
      showTitleErr: false,
      showDescriptionErr: false,
      styleObj: {
        width: "150px"
      }
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
      if (val.hide && val.id === this.ideas.id) {
        this.ideas = this.computeState(val);
        this.title = val.title;
        this.description = val.body;
        this.editDescription = false;
        this.editTitle = false;
      }
    },
    title(value) {
      this.showTitleErr = value.trim().length === 0;
    },
    description(value) {
      this.showDescriptionErr =
        value.trim().length === 0 || value.trim().length > 140;
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
      if (newVal.trim().length === 0) {
        if (attr === "title") {
          this.showTitleErr = true;
        }
        return;
      }
      if (
        newVal !== oldVal &&
        newVal.trim().length > 0 &&
        !this.showDescriptionErr
      ) {
        /* update title */
        const obj = { id: this.idea.id };
        obj[attr] = newVal.trim();
        this.$emit("update-idea", obj);
      } else if (this.showDescriptionErr) {
        this.editDescription = true;
      } else {
        this.editTitle = false;
        this.editDescription = false;
      }
    },
    eleClick(type) {
      if (type === "title" && !this.editTitle && !this.editDescription) {
        this.editTitle = !this.editTitle;
      } else if (type === "body" && !this.editTitle && !this.editDescription) {
        this.editDescription = !this.editDescription;
      }
    }
  }
};
</script>

<style lang="less">
.tilesWrapper {
  .tiles {
    height: 150px;
    width: 150px;
    margin-right: 10px;
    margin-bottom: 10px;
    .ant-card-body {
      padding: 0.5rem !important;
    }
    .close {
      float: right;
      padding: 6px;
    }
    h2 {
      display: inline-block;
    }
    .titleInput:hover {
      border-color: #cccc;
      resize: none;
    }
    .ideaBody {
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
    .ideaWrapper {
      display: flex;
    }
    .titleErr {
      font-size: 12px;
      color: #f5222d;
    }
    .titleWarning {
      font-size: 12px;
      color: #faad14;
    }
  }
}
</style>
